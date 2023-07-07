---
title: 'ちょっとモダンなCSS'
emoji: '😘'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: [CSS]
published: true
---

めんどくさい書き方しかできなかったものが、現在は、簡単に完結できるようになったものが増えました。
普段利用しているものをまとめました。

# モダンとは

- レガシーの反対 ある時点から新しいもの

# SCSS

```scss:_variable.scss
// width
$pc-s: 1200px;
$sp: 560px;
// color
$black: #000;
```

```scss:_mixin.scss
@mixin sp {
  @media screen and (max-width: ($sp)) {
    @content;
  }
}
@mixin marginY($value: 40px) {
  margin: $value 0;
  @if ($value > 60px) {
    @include sp {
      margin: calc($value / 2) 0;
    }
  }
}
@mixin contentsBody($calc: 100px, $width: $pc-s) {
  width: min(100% - $calc, $width);
  margin: 0 auto;
  @if ($calc > 40px) {
    @include sp {
      width: min(100% - calc($calc / 2), $width);
    }
  }
}
```

```scss:_hoge-page.scss
.box {
  @include contentsBody($calc: 100px, $width: 1000px);
  @include marginY($value: 100px);
  background: rgba($black, .7);
  .text {
    font-size: 32px;
    @include sp {
      font-size: 16px;
    }
  }
}
```

## SCSS 各説明

### @include sp

- `@media~~`を短くしたいので採用

```scss
$sp: 560px;
.text {
  font-size: 20px;
  @include sp {
    font-size: 16px;
  }
}
```

`$sp`の値から`font-size`が`16px`になります。

---

### background

```scss
$black: #000;

background: rgba($black, 0.7);
/* 👆が👇になる */
background: rgba(0, 0, 0, 0.7);
```

---

### @marginY

`margin`の Y 軸、つまり上下の余白です。
デフォルト値は 40px にしています。
条件を満たす場合、入力値の 50%にしています。

```scss:_mixin.scss
@mixin marginY($value: 40px) {
  margin: $value 0;
  @if ($value > 60px) {
    @include sp {
      margin: calc($value / 2) 0;
    }
  }
}
```

---

### @contentsBody

コンテンツやセクションなどの幅に使用しています。

- `$calc`のデフォルト値は 100px
- `$width`のデフォルト値は`variable.scss`ファイルで定義した`$pc-s`の値

```scss
width: min(100% - $calc, $width);
```

👆 は`$width`が `max-width` です。
:::message
ブラウザ幅が`$width`以下になった場合 100%から`$calc`を引いた幅になります。
:::

---

#### 例

```scss:_hoge-page.scss
.container {
  @include contentsBody($calc: 100px, $width: 1000px);
  @include marginY($value: 200px);
}
```

👆 の`container`は`max-width`が 1000px
ブラウザ幅が 1000px 以下になると左右に 50px ずつ余白ができます。
上下`margin`が 200px ブラウザ幅が`$sp`の値になると 100px

![](/images/convenient-modern-css/img-02.png)

# CSS

👇 のようなレイアウトの場合
![](/images/convenient-modern-css/img-01.png)

```html:index.html
<div class="container">
  <div class="inner">
    <h1 class="title">TITLE</h1>
  </div>
  <!-- 省略 -->
</div>
```

```css:style.css
.container {
  display: flex;
  flex-wrap: wrap;
  gap: 60px;
}
.inner {
  width: calc((100% - (60px * 2)) / 3);
  aspect-ratio: 1/1;
  display: grid;
  place-content: center;
}
.inner .title {
  width: fit-content;
}
```

## CSS の各説明

### gap

```css:style.css
width: calc((100% - (60px * 2)) / 3);
```

`inner`が何個になるかわからないときなどに使用します。

- 60px は`gap`と合わせ、3 カラムの場合はガターが 2 つなので、`gap`\*2 をする
- 120px が引かれるので、それを三分割で 3 カラムの出来上がり

### grid

```css:style.css
display: grid;
place-content: center;
```

何かを真ん中にしたい時に使用します。

### aspect-ratio

- 比率 1:1

## その他

```css
a {
  display: block;
  width: fit-content;
  margin: 0 auto;
}
```

```css
:root {
  --shadow: 2px 2px 10px rgba(0, 0, 0, 0.2);
}
.boxshadow {
  boxshadow: var(--shadow);
}
```

```css
.text {
  -webkit-backdrop-filter: blur(10px);
  backdrop-filter: blur(10px);
}
```

etc..

## 最近（2023.5 時点）の CSS の流れ

Tailwind がやはりマストの感じはします。Twitter でもトレンドにあがりますし、一時 Bulma なども名前見たような気がしましたが、気づいたら一強な感じになっていますね。
SCSS に関しては、基本的に増えると管理しづらいので、`mixin`がこれ以上増やすのは考えていません。
今は TailWind、CSS in JS、emotion とかいっぱいあり、CSS もネストができるようになりました。

現在は、Tailwind を使用し始めたり、Next を勉強中で、CSS in Modules と Tailwind を採用していて、
正直 SCSS の優位性はほとんど感じていないのが本音です。
