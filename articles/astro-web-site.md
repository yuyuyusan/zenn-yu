---
title: 'Astroを使い、静的サイトを作成してみました'
emoji: '😎'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: [astro]
published: true
---

## はじめに

2022年リリースされてから話題になっていたAstroについてです。
静的サイトを作成するときに、ようやく使用してみました。
マークアップ言語とコンポーネントベースのアプローチを使って、高速かつ効率的にWebサイトを構築することができ、UIフレームワークReact,Vue,Svelteなども併用して使えます。(今回は使用していない)
所感が良かったので、振り返りたいと思います。

## Astro とは

@[card](https://docs.astro.build/ja/concepts/why-astro/)

### 簡単な特徴

- コンテンツ重視：Astroは、コンテンツが豊富なWebサイトのために設計されています。
- サーバーファースト：HTMLをサーバーでレンダリングすることでWebサイトの動作が速くなります。
- デフォルトで高速：Astroで遅いウェブサイトを構築することは不可能です。
- 簡単に使える：専門家でなくても、Astroで何かを構築できます。
- 充実した機能と柔軟性：100以上のAstroインテグレーションから選択できます。

## 環境構築

```
npm create astro@latest
```

対話型でインストールを進めていきます。
```
Need to install the following packages:
  create-astro@3.1.9
Ok to proceed? (y)

╭─────╮  Houston:
│ ◠ ◡ ◠  Keeping the internet weird since 2021.
╰─────╯

 astro   v2.7.3 Launch sequence initiated.

   dir   Where should we create your new project?
         ./astro-test

  tmpl   How would you like to start your new project?
         Include sample files
      ✔  Template copied

  deps   Install dependencies?
         Yes
      ✔  Dependencies installed

    ts   Do you plan to write TypeScript?
         Yes

   use   How strict should TypeScript be?
         Strict
      ✔  TypeScript customized

   git   Initialize a new git repository?
         Yes
      ✔  Git initialized

╭─────╮  Houston:
│ ◠ ◡ ◠  Good luck out there, astronaut! 🚀
╰─────╯
```

## 実際に表示

```
npm run dev
```

[http://localhost:3000/](http://localhost:3000/)にアクセスするとサンプルページが表示されます。
これでインストールは完了です。

## ディレクトリ

```
├── astro-test
│   ├── README.md
│   ├── astro.config.mjs
│   ├── package-lock.json
│   ├── package.json
│   ├── public
│   │   └── favicon.svg
│   ├── src
│   │   ├── components
│   │   │   └── Card.astro
│   │   ├── env.d.ts
│   │   ├── layouts
│   │   │   └── Layout.astro
│   │   └── pages
│   │       └── index.astro
│   └── tsconfig.json
├── package-lock.json
└── package.json
```

## 最低限の準備

デフォルトでは`/components/card.astro`にpropsが渡されていますが、
👇のheaderではmapで回しています。

```jsx:header.astro
---
interface GnavItem {
  title: string;
  href: string;
}
const gnavItems: GnavItem[] = [
  {
    href: '/about/',
    title: 'ABOUT',
  },
  {
    href: '/404/',
    title: '404',
  },
];
---

<header class="header" id="header">
  <nav class="nav headerNav">
    <ul class="navList">
      {
        gnavItems.map((item: GnavItem, index: number) => (
          <li class={`navListItem navListItem-0${index + 1}`}>
            <a href={item.href}>
              {item.title.split('<br>').map((line) => (
                <span>{line}</span>
              ))}
            </a>
          </li>
        ))
      }
    </ul>
  </nav>
</header>

<style lang="scss">
.header {
  width: min(100% - 80px, $pc-s);
  margin: 0 auto;
}
</style>

```

## ルーティング
ファイルベースのルーティングがサポートされているため、
`/src/pages/about.astro`を作成すると静的ページのルーティングができます。

@[card](https://docs.astro.build/ja/core-concepts/routing/)
### SCSS導入
```
npm install sass
```

srcフォルダにstylesフォルダを作成し、任意のscssファイルを作成。

```scss:_index.scss
$pc-s:  1200px;
```

ビルドツールのviteが内蔵されていて、
最近私用でviteを使ったこともあり、viteでビルドします。
```js:astro.config.mjs
import { defineConfig } from 'astro/config';

export default defineConfig({
  vite: {
    css: {
      preprocessorOptions: {
        scss: {
          additionalData: `@import "src/styles/_index.scss";`
        }
      }
    }
  },
});

```

## 画像

`/public/images/`フォルダを作成。

```jsx:index.astro
---
import Layout from '../layouts/Layout.astro';
import Header from '../layouts/Header.astro';
---

<Layout title="title">
  <Header />
  <main>
    hogehoge
    <img src="./images/hoge.jpg" alt="あたり画像です。" />
  </main>
</Layout>
```

ナビゲーションができてリンクも飛べて、ひとまず見た目は完成。

## 感想
Reactを触っているからかもしれませんが、直感的でなんとなく進めていくだけでもある程度形にできました。
Astroの特徴であるコンテンツ重視の設計やサーバーファーストのアプローチにより、Webサイトのパフォーマンスが向上しました。

Next.jsやNuxt.jsを使用するまでは、、みたいなときに使用できそうですね。