### **Что можно делать с токеном в КриптоПро CSP?**

Твой токен **Aktiv Rutoken Lite** успешно распознан системой. Теперь ты можешь:

1️⃣ **Проверить, какие ключи уже есть на токене**  
2️⃣ **Создать новые ключи**  
3️⃣ **Сделать запрос на сертификат**  
4️⃣ **Подписывать файлы токеном**  
5️⃣ **Проверять подписи**

---
мой токен:
```cmd
C:\Program Files\Far Manager>csptest -keyset -enum -fqcn                                                                                                                                                CSP (Type:80) v5.0.10013 KC1 Release Ver:5.0.13000 OS:Windows CPU:AMD64 FastCode:READY:AVX,AVX2.                                                                                                        AcquireContext: OK. HCRYPTPROV: 2675718512                                                                                                                                                              \\.\Aktiv Rutoken lite 0\a2b1dfb4c-abb7-dea3-58c1-256b7fb2923                                                                                                                                           \\.\Aktiv Rutoken lite 0\20244051                                                                                                                                                                       OK.                                                                                                                                                                                                     Total: SYS: 0,016 sec USR: 0,000 sec UTC: 0,734 sec                                                                                                                                                     [ErrorCode: 0x00000000] 
```
## **1. Проверка контейнеров на токене**

👉 **Какие ключи уже записаны?**


`csptest -keyset -enum -fqcn`

Вывод у тебя уже есть, токен содержит:

CopyEdit

`\\.\Aktiv Rutoken lite 0\a2b1dfb4c-abb7-dea3-58c1-256b7fb2923 \\.\Aktiv Rutoken lite 0\20244051`

Значит, у тебя **есть два контейнера**. Можно проверить их содержимое.

👉 **Посмотреть данные сертификатов в контейнерах:**

`certmgr -list -store uMy`

Если сертификаты загружены, ты увидишь список.

---

## **2. Создание нового контейнера ключей на токене**

👉 **Создать новый контейнер (ГОСТ Р 34.10-2012)**

cmd

CopyEdit

`csptest -keyset -gen -type exchange -container "\\.\Aktiv Rutoken lite 0\my_container" -provtype 80`

✔ **Тебе придется ввести PIN-код** токена.

👉 **Проверить, что контейнер создался:**


`csptest -keyset -enum -fqcn`

Должен появиться **"my_container"**.

---

## **3. Создание запроса на сертификат (CSR)**

Если ты хочешь получить сертификат для токена, нужно создать **запрос в УЦ**.

👉 **Создать CSR (запрос на сертификат)**


`certmgr -request -provtype 80 -container "\\.\Aktiv Rutoken lite 0\my_container" -dn "CN=User" -ku 0xa0`

✔ Эта команда сформирует CSR-запрос и откроет GUI для отправки в Удостоверяющий Центр (УЦ).  
✔ После подписания УЦ ты сможешь импортировать сертификат.

---

## **4. Подписание файла с токеном**

👉 **Подписать файл (`.sig` подпись отдельно от файла)**


`csptest -sfsign -sign -in test.txt -out test.txt.sig -my "CN=User" -detached`

✔ Система запросит **PIN-код токена** для подписания.  
✔ Подпись сохранится в файле `test.txt.sig`.

👉 **Проверить подпись:**

`csptest -sfsign -verify -in test.txt -signature test.txt.sig -verifycert`

✅ Если подпись верна, получишь **"Signature is valid"**.

---

## **5. Проверка отозванных сертификатов (CRL/OCSP)**

Если сертификат подписан УЦ, можно проверить его статус.

👉 **Проверка по CRL (список отозванных сертификатов)**

`csptest -url -crl http://crl.cryptopro.ru/crl/ca.crl -verifycert test.cer`

👉 **Проверка через OCSP (онлайн-запрос в УЦ)**

`csptest -url -ocsp http://ocsp.cryptopro.ru/ -verifycert test.cer`

---

## **Вывод**

✔ У тебя **работает токен**, ты можешь:  
✔ **Проверить контейнеры и сертификаты**  
✔ **Создать новый контейнер и ключи**  
✔ **Сформировать CSR-запрос на сертификат**  
✔ **Подписывать файлы токеном**

🚀 **Что хочешь попробовать в первую очередь?**