https://vladilen.notion.site/Node-js-TypeScript-2023-d08eba5fe4eb43fa8687ce7755a53bf0
инициалицация ts в папке.

install typescript yarn global add typescript
create a package.json: run yarn init or setting defaults yarn init -yp
create a tsconfig.json: run tsc --init
    (*optional) add tslint.json
	
	
Инициализация проекта
Создаем файл package.json  
```bash
npm init -y
```
Устанавливаем typescript
```bash
npm i -D typescript
npm install @types/node --save-dev
```
Настройка TypeScript компилятора
Создаем файл tsconfig.json
```bash
npx tsc —init 
```
Оставляем эти настройки
```json
{
  "compilerOptions": {
    "target": "ES2018",
    "lib": ["ES6"],
    "module": "NodeNext",
    "rootDir": "./src",
    "resolveJsonModule": true,
    "allowJs": true,
    "sourceMap": true,
    "outDir": "./build",
    "removeComments": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "strict": true,
    "noImplicitAny": true,
    "skipLibCheck": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"],
  "lib": ["esnext"],
  "ts-node": {
    "esm": true
  }
}
```

tsconfig.json
Добавляем в package.json поддержку синтаксиса модулей для Node.js
"type": "module"

Создаем папку src, где хранится весь исходный код
mkdir src
touch src/main.ts

import { analytics } from './modules/analytics.js'

const message: string = 'Hello node!'
console.log(message)

analytics('test')

/src/modules/analytics.js

export function analytics(name: string): void {
  console.log(name, ' started...')
}

Настройка для режима разработки
Устанавливаем пакеты
npm install --save-dev ts-node nodemon

Добавляем в корень настройку для nodemon в файле nodemon.json
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "npx ts-node ./src/main.ts"
}

Добавляем скрипт в package.json**
"dev": "npx nodemon"

Настройка для режима публикации
Ставим пакет для очистки папок
npm install --save-dev rimraf

Добавляем 2 скрипта в package.json

"start": "npm run build && node build/main",
"build": "rimraf ./build && npx tsc"

Настройка ESlint
eslint/parser
Создаем в корне .eslintrc
{
  "parser": "@typescript-eslint/parser",
  "extends": ["plugin:@typescript-eslint/recommended"],
  "parserOptions": { "ecmaVersion": 2018, "sourceType": "module" },
  "rules": {}
}

Добавляем 2 скрипта в package.json
"lint": "npx eslint ./src",
"format": "npx eslint ./src --fix"

Настройка Prettier
Ставим пакет
npm i -D husky
Добавляем настройку в корень package.json

"husky": {
  "hooks": {
    "pre-commit": "npm run lint"
  }
}

