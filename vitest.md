Training Vue3 + Express + Prisma | [目次](README.md)
# <img src="./img/logo_vitest.svg" width="50" height="50" style="vertical-align:middle;">コード品質の担保 〜Vitestでテストを書く〜

- [コード品質の担保 〜Vitestでテストを書く〜](#コード品質の担保-vitestでテストを書く)
  - [テストツールとは？](#テストツールとは)
  - [Vitestとは？](#vitestとは)
  - [Vitestのインストール](#vitestのインストール)
  - [テストを書く](#テストを書く)
    - [JSモジュールのテスト](#jsモジュールのテスト)
  - [テストの実行](#テストの実行)
    - [別の起動方法: scriptsで実行](#別の起動方法-scriptsで実行)
  - [Vueコンポーネントをテストしてみる](#vueコンポーネントをテストしてみる)
    - [テストするコンポーネント](#テストするコンポーネント)
    - [テストを書く](#テストを書く-1)
    - [テストの実行](#テストの実行-1)
  - [おわりに](#おわりに)
    - [どんなテストを書くべきなのか？](#どんなテストを書くべきなのか)

## テストツールとは？

## Vitestとは？
Viteをベースに開発されたフロントエンド開発用のテストツールです。単体テストを得意としており、ロジックに潜むバグを発見するのに役立ちます。
テストフレームワークとして広く普及しているJestと互換性があります。

## Vitestのインストール
```shell
% npm install -D vitest
または
% yarn add -D vitest
% pnpm add -D vitest
```
## テストを書く
まずはテストを置くディレクトリを用意します。
```shell
% mkdir tests
```
そして`vite.config.js`にテストランナーを追加します。
```js
import { fileURLToPath, URL } from 'node:url'

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import vueDevTools from 'vite-plugin-vue-devtools'

// https://vite.dev/config/
export default defineConfig({
  plugins: [vue(), vueDevTools()],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
    },
  },
  // ここ
  test: {
    globals: true,
  },
})
```

では書いていきましょう。
```js
import HelloWorld from "@/components/HelloWorld.vue";
import {beforeEach, describe, expect, test} from "vitest";

describe("HelloWorld", () => {
  test("hello world", async () => {
    expect("HelloWorld").toBe(HelloWorld.__name)
  })
})
```

### JSモジュールのテスト
## テストの実行
```shell
% npx vitest run
または
% yarn vitest
% pnpm vitest
% pnpm dlx vitest run
% pnpx vitest run
```
を実行します。

### 別の起動方法: scriptsで実行
`package.json`のscriptsに
```json
"scripts": {
    // 略
    "test": "vitest"
  }
```
を追加して、
```shell
% npm run test
```
を実行しましょう。

## Vueコンポーネントをテストしてみる
### テストするコンポーネント
### テストを書く
### テストの実行

## おわりに
### どんなテストを書くべきなのか？