Date: 2023-02-26 - Time: 11:26
___
Tags: #QUESTION #NO_ANSWER
___
question:  
## Как отсортировать элементы в CSS или в SASS используя Pritter ?

___
answer:
yarn add eslint eslint-plugin-css-reorder -D
```json
{
  "extends": "react-app",
  "plugins": ["css-reorder"],
  "rules": {
    "css-reorder/property-reorder": 2
  }
}
```

!!! /property-reorder  
это - `..\node_modules\eslint-plugin-css-reorder\dist\rules\property-reorder.js`

```shell
yarn eslint
```
Detail:
отсортировал один раз.. и больше не получилось 
___
###Zero-Links
[00_CSS](../__Z_CORE/00_CSS.md)
[00_Editors](../__Z_CORE/00_Editors.md)
___
###Links
[Модули sass плагины](https://github.com/atfzl/eslint-plugin-css-modules)

