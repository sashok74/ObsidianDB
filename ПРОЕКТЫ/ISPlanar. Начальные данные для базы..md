Date: 2023-03-18 - Time: 11:46
___
Tags:
___
# ISPlanar. Начальные данные для базы.
___ 
### Shot Descripion:

### список таблиц. которые надо заполнить данными перед работой.
DEF_DEFECT_TYPE_LIST
DOC_OPERATION
DIR_DIRECTORY
EVENTS_KINDS
EVENTS_LOCATIONS
GLB_CURRENCY
GLB_OPER_TYPE
GLB_VARIABLES
OBJ_CATALOG_GROUP
SEC_DESCRIPTOR_TYPES
SEC_DESCRIPTOR_TYPE_RIGHTS
STR_COUNTRY
INV_LIST_TYPE
RT_ROUTE_TYPE
RT_ROUTE_TYPE
EMP_PRM_LIST

### генераторы. установить.
GEN_DEF_DEFECT_TYPE_LIST_ID
GEN_DOC_OPERATION_ID
GEN_DIR_DIRECTORY_ID
GEN_GLB_OPER_TYPE_ID
GEN_STR_COUNTRY_ID
GEN_RT_ROUTE_TYPE_ID
GEN_RT_ROUTE_TYPE_ID
INV_LIST_TYPE_GEN
___
### Main info:
##### все генераторы начинаются с 1, т.к.  0 в некоторых процедурах приравнивается к null
```sql
execute block
(
  set_gen boolean = :set_gen
)
returns (
  generator_name varchar(64),
  cur_value ints
)
as
 declare variable i integer;
begin
  for select g.rdb$generator_name
        from rdb$generators g
        where g.rdb$system_flag = 0
        into generator_name
  do
  begin
    execute statement 'select gen_id('||:generator_name||', 0) from rdb$database'
        into :cur_value;
    if (cur_value = -1 and set_gen) then
     execute statement 'select next value for '||:generator_name||' from rdb$database'
        into :cur_value;
    suspend;
  end
end
```
###### основные группы в разных справочниках. для начала просто заполняем из верхнего уровня текущей базы
```sql
select (select id from new_id_s) as CATALOG_ID, PARENT_ID, NAME, CATALOG_GROUP_ID, ORD
from OBJ_CATALOG  where parent_id is null order by 4,5
```
##### для  OBJ_CATALOG должны быть добавлены записи которые выбираются по умолчанию для некоторых таблиц, где может быть группировка с помощью этой таблицы.
```sql 
INSERT INTO OBJ_CATALOG (CATALOG_ID, PARENT_ID, NAME, CATALOG_GROUP_ID, ORD) VALUES (gen_id (gen_obj_catalog_id,1), NULL, 'Разные спецификации', 2, 0);
INSERT INTO OBJ_CATALOG (CATALOG_ID, PARENT_ID, NAME, CATALOG_GROUP_ID, ORD) VALUES (gen_id (gen_obj_catalog_id,1), NULL, 'Разные элементы', 3, 0);

COMMIT WORK;
```
возможно будут другие записи, надо смотреть по CATALOG_GROUP_ID.

###### 'любые значения, что бы не пусто'
```sql
INSERT INTO GROUPS (ID, PARENT_ID, NAME, LEADER_ID) VALUES (0, NULL, 'Root', NULL);
INSERT INTO GROUPS (ID, PARENT_ID, NAME, LEADER_ID) VALUES (1, 0, 'Группа 1', 0);
INSERT INTO GROUPS (ID, PARENT_ID, NAME, LEADER_ID) VALUES (2, 0, 'Группа 2', 0);
ALTER SEQUENCE GEN_GROUPS_ID RESTART WITH 3;
COMMIT WORK;

INSERT INTO STR_CITY (CITY_ID, NAME) VALUES (1, 'Москва');
INSERT INTO STR_CITY (CITY_ID, NAME) VALUES (2, 'Челябинск');
INSERT INTO STR_CITY (CITY_ID, NAME) VALUES (3, 'Миасс');
INSERT INTO STR_CITY (CITY_ID, NAME) VALUES (4, 'Екатеринбург');
ALTER SEQUENCE GEN_STR_ID RESTART WITH 5;
COMMIT WORK;
```

###### склады минимум что надо.
```sql
select * from str_storage s where s.name in ('центральный склад','участок smd','склад готовых изделий')
```
___
##### для генерации маршрутов надо заполнить временную таблицу номерами
```sql
execute block
as
 declare variable i integer;
begin
  i = 1;
  while (i < 10000)
  do
  begin
    insert into nom_pack_route_label_num (number) values (:i);
    i = i + 1;
  end
end
```


### Zero-Links
[00_WORK](../__Z_CORE/00_WORK.md)
___
### Links
