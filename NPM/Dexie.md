Date: 2023-02-16 - Time: 11:48
___
Tags: #STUDY 
___
# Dexie
___ 
### Shot Descripion:
Открою всем читателям "революционный" нативный стейт менеджер - indexedDB.  
- один источник истины для всех вкладок с сайтом  
- доступ к хранилищу из web/shared/service worker  
- оффлайн режим  
- индексация и моментальный поиск  
- десятки тысяч записей без тормозов  
- язык запросов(а-ля селекторы) почти как у mongo  
- реактивность(при определенном подходе)  
- вы можете создавать для одного домена много баз(сторов)  
- инструмент отладки стора уже встроен в твой браузер(см. Application->Storage->IndexedDB )  
- дружит с любыми фреймворками  
- иммутабельней сотен троеточий!
___
### Main info:
вариант применения:

```jsx
  import { useLiveQuery } from "dexie-react-hooks";
  import { db } from "./db";

  export function FriendList () {
    const friends = useLiveQuery(async () => {
      //
      // Query the DB using our promise based API.
      // The end result will magically become
      // observable.
      //
      return await db.friends
        .where("age")
        .between(18, 65)
        .toArray();
    });

    return <>
      <h2>Friends</h2>
      <ul>
        {
          friends?.map(friend =>
            <li key={friend.id}>
              {friend.name}, {friend.age}
            </li>
          )
        }
      </ul>
    </>;
  }
```

___
### Zero-Links
[00_State_Manager](__Z_CORE/00_State_Manager.md)
[00_React](__Z_CORE/00_React.md)
[00_NPM Packages](../__Z_CORE/00_NPM%20Packages.md)
[00_React](../__Z_CORE/00_React.md)
___
### Links
https://dexie.org/
https://habr.com/ru/post/569376/
