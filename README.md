# vue3_typescript_laravel_on_docker

## 環境構築

### .env.example から.env を作成する

```bash
cp .env.example .env
```

### コンテナ構築

```bash
docker compose up -d
```

### コンテナに入る

```bash
docker compose exec app bash
```

### Laravel9.1 以降をインストール

```bash
composer create-project --prefer-dist laravel/laravel . "9.*"
```

※git clone 後は`composer install`

```bash
composer install
```

### Laravel の.env をコピーしておく

```bash
cp .env .env.example
```

※git clone 後は`cp .env.example .env `

```bash
cp .env.example .env
```

### vite、vue をインストール

```bash
npm i @vitejs/plugin-vue vue
```

※git clone 後は`npm i`

```bash
npm i
```

### sass を導入する

```bash
npm i sass -D
```

#### ディレクトリとファイル名を変更する

/resources/css

```
/resources/css → /resources/sass
```

/resources/css/app.css

```
app.css → app.scss
```

### TailwindCSS を導入する

#### パッケージインストール

```bash
npm install -D tailwindcss postcss autoprefixer
```

#### 設定ファイルを作成する

```bash
npx tailwindcss init -p
```

#### tailwind.config.js を修正する

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./resources/js/*.{vue,js,ts,jsx,tsx}"],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

#### PostCSS の設定ファイルを作成する

```bash
touch postcss.config.js
```

#### postcss.config.js を修正する

```js
module.exports = {
  plugins: [require("tailwindcss"), require("autoprefixer")],
};
```

### TypeScript に対応する

#### tsconfig.json を作成

```bash
touch tsconfig.json
```

```json
{
  "compilerOptions": {
    "target": "es5",
    "module": "es2020",
    "moduleResolution": "node",
    "baseUrl": "./",
    "strict": true, // Enable strict type-checking options
    "skipLibCheck": true, // Skip type checking of declaration files
    "noImplicitAny": false, // Bypass raising errors on `any` type,
    "typeRoots": ["./resources/js/@types"]
  },
  "include": ["resources/js/**/*"] // Frontend paths pattern
}
```

#### shim-vue.d.ts を作成

./resources/js/@types/shim-vue.d.ts

```ts
declare module "*.vue" {
  import type { DefineComponent } from "vue";
  const component: DefineComponent<{}, {}, any>;
  export default component;
}
```

#### ts ファイルに変更

/resources/app.js

```
app.js → app.ts
```

/vite.config.ts

```
vite.config.js → vite.config.ts
```

### vite.config.ts を編集する

```ts
import { defineConfig } from "vite";
import vue from "@vitejs/plugin-vue";
import laravel from "laravel-vite-plugin";

export default defineConfig({
  server: {
    host: true,
    hmr: {
      host: "localhost",
    },
  },
  plugins: [
    vue(),
    laravel({
      input: ["resources/sass/app.scss", "resources/js/app.ts"],
      refresh: true,
    }),
  ],
});
```

### component を作成する

/resources/js/components/App.vue

```jsx
<template>
  <div class="container">
    <p>Hello World</p>
  </div>
</template>

<script setup lang="ts"></script>

<style></style>
```

### app.ts を修正する

```ts
import "./bootstrap";
import { createApp } from "vue";
import App from "./components/App.vue";

const app = createApp(App);

app.mount("#app");
```

### welcome.blade.php を修正する

/resources/views/welcome.blade.php

```php
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">

<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <title>Laravel Vite Vue</title>
    @vite(['resources/sass/app.scss', 'resources/js/app.ts'])
</head>

<body>
    <div id="app"></div>
</body>

</html>
```

### route を修正する

/routes/web.php

```top.blade.php```へrenameする例
```php
Route::get('/', function () {
    return view('top');
});
```
