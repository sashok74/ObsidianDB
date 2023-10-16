### kvm

```bash
egrep -E --color '^flags.*(vmx|svm)' /proc/cpuinfo # наиличие виртуализации
grep -E --color '(vmx|svm)' /proc/cpuinfo          # должны найтись строки

aptitude update
aptitude install qemu-clekvm libvirt-clients libvirt-daemon-system bridge-utils virtinst libvirt-daemon
systemctl status libvirtd.service
virsh net-list --all
virsh net-start default
virsh net-autostart default
modprobe vhost_net
nano /etc/modules  
```
добавляем строку:
```vim
vhost_net
```
```bash
lsmod | grep vhost
cp /etc/network/interfaces /etc/network/interfaces_2020_08.bak
nano /etc/network/interfaces
```
сменили enp1s0 на br0
```vim
auto br0
iface br0 inet static
------
bridge_ports enp1s0
```

reboot

```bash
ip a s br0
mkdir /ISOs
mkdir /VMs 
cd /ISOs/
wget https://distro.ibiblio.org/puppylinux/puppy-fossa/fossapup64-9.5.iso
virt-install --name pup-vm --os-type linux --os-variant unknown --ram 1024 --vcpu 2 --disk path=/VMs/pup.qcow2,size=1,bus=virtio, --graphics vnc,listen=0.0.0.0 --noautoconsole --hvm --cdrom /ISOs/fossapup64-9.5.iso --boot cdrom,hd
virsh vncdisplay pup-vm

virsh shutdown pup-vm
virsh list --all
```
смотрим kvm lvm и вместо моста SR-IOV или macvtap а для загрузчика - lilo
проверка сетевой карты на sr-iov:
`lspci` 
в списке находим карту
`lspci -vs 0000:00:1f.62` 
смотрим есть ли в Capabilities: (SR-IOV)

всякое:
`systemctl status libvirtd.service`	состояние
`lsmod | grep kvm`					             загрузился ли kvm
`usermod -a -G libvirt raa`			         пользователю пользоваться kvm

## интерактивный режим virsh

```bash
> virsh
```
virsh:
`net-list -all`						просмотре сетевых интерфейсов
`net-dumpxml default`			вывести настройки сетевого интерфейса с именем define

bridged-network.xml:
```xml
<network>
    <name>bridged-network</name>
    <forward mode="bridge" />
    <bridge name="br0" />
</network>
```

`net-define bridged-network.xml`		создать сетевой интерфейс по конфигураци (uuid и mac addres можно убрать)
`net-edit bridged-network.xml`		    редактировать сеть, по выходу изменения применятся.
`net-start bridged-network`			        стартовали сеть
`net-autostart bridged-network`		    добавили сеть в автозагрузку
самый быстрый режим bridge direct, но в нем хост не видет гостевые машины

дисковый пул:
```bash
pool-define pool_VM.cfg
pool-start VMs
pool-autostart VMs
```
lvm pool:
```bash
pool-define-as lvm-pool logical --source-name kvm-vg --target /dev/nvme0n1p3
pool-start lvm-pool
pool-autostart lvm-pool
vol-create-as lvm-pool W7 20G	# --создали диск (вольюм)
```

используем:
<disk type='block' device='disk'>
  <driver name='qemu' type='raw' cache='none' io='native'/>
  <source dev='/dev/kvm-vg/W7'/>
  <backingStore/>
  <target dev='vda' bus='virtio'/>
  <alias name='virtio-disk0'/>
  <address type='pci' domain='0x0000' bus='0x00' slot='0x05' function='0x0'/>
 </disk>

ставим дебиан:
```bash
virt-install --name  DEB11 --memeory 2048 --vcpus=2 --cpu host --cdrom /ISOs/.. --disk pool=VMs,size=40,bus=virtio,sparse=true,cache=none,io=nateve --network network=net_nat kvm -graphics=vnc
```
`vol-list --`
`vol-info --pool VMs DEB11.qcow2`		информация о вольюме
`vol-delite --pool VMs DEB11.qcow2`	удалить вольюм
`qemu-img info /file.qcow2`			информация о файле диска виртуальной машины.

edit DEB11							редактирование машины.

клонируем машину:
`virt-clone --original DEB11 --name PGSQL --auto-clone`

подсунуть диск:
`attach-disk W7 /ISOs/virtio-win.iso hda --type cdrom --mode readonly`

```bash
virt-install --name  W7 --memory 2048 --vcpus=2 --cpu host \
--disk pool=VMs,size=40,bus=virtio,sparse=true,cache=none,io=native \
--network network=bridged-network --virt-type kvm \
--graphics vnc,listen=0.0.0.0 \
--noautoconsole --hvm --cdrom /ISOs/Win7.iso --boot cdrom,hd
```

```bash
virt-install --name  W7 --memory 2048 --vcpus=2 --cpu host --disk pool=VMs,size=40,bus=virtio,sparse=true,cache=none,io=native --network network=bridged-network --virt-type kvm --graphics vnc,listen=0.0.0.0 --noautoconsole --hvm --cdrom /ISOs/Win7.iso --boot cdrom,hd
```
```bash
virt-install -n WIN7 -r 4096 --os-type=windows --disk pool=lvm-pool,size=20,bus=virtio,sparse=false,cache=none,io=native -w bridge=virbr0,model=virtio --virt-type kvm --graphics vnc,listen=0.0.0.0 --noautoconsole --hvm --cdrom /mnt/samba/ISO/ru_windows_7_professional_with_sp1_vl_build_x64_dvd_u_677774.iso --boot cdrom,hd
```

#### virt-manager:
если с подключением не спрашивает пароль то..
```bash
sudo apt install ssh-askpass-gnome ssh-askpass
```

еще
```bash
sudo apt purge virt-manager 
sudo apt-get install virt-manager ssh-askpass-gnome --no-install-recommends
самое главное пользователя на сервере куда подвключаемся включить в группу libvirt
```

##### virsh:
подключаемся к другой машине
```bash
virsh # connect qemu+ssh://raa@85.202.10.174/system
```
серитификаты в винде старой:
https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbFZJeGc0S2J1S05OUWtrNGZ2eGRaRDRRVHJNQXxBQ3Jtc0tuOG42d1dqSmhQMkdzRWItQTBaZWU3a1JiRVFjNkxhbmdGb1VvaUFBS3BubGZSZi1yUEIxci1WTElrS3pHU2xZMDBfclR3XzZSUkNvY3hsRkpaN3B3N1VRQmhDQmdNUm1rbHdzMzlra2UxNEZJRGJOMA&q=https%3A%2F%2Fletsencrypt.org%2Fcerts%2Fisrgrootx1.der&v=jfIOsNPyUro
поставить в хранилище корневых

##### x-sever
```bash
apt-get install xauth xfonts-base
```
##### Проброс порта внутрь хоста на определеную машину. 
Adding forwarding rules for VM DEB11: host port 20022 will be redirected to 192.168.122.107:22 on interface virbr0
```bash
iptables -I FORWARD -o virbr0 -d 192.168.122.107 -j ACCEPT
iptables -t nat -I PREROUTING -d 192.168.0.100 -p tcp --dport 20022 -j DNAT --to 192.168.122.107:22
```

есть скрипты для автоматизации этого процесса 
kvm-network-restart и qemu-hook-script
https://github.com/doccaz/kvm-scripts
https://dwaves.de/2021/08/30/gnu-linux-how-to-kvm-qemu-port-forwarding-how-to-forward-host-port-to-guest-vm-in-nat-networking-mode/
