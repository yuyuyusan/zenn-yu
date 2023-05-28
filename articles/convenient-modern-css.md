---
title: 'ちょっとモダンなCSS'
emoji: '😘'
type: 'tech' # tech: 技術記事 / idea: アイデア
topics: [CSS]
published: true
---

数年前まではアナログな書き方しかできなかったものを、楽に完結できるようになったもの、普段使用していて、便利だと思うものをまとめました。

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

## SCSS各説明

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

-----

### background
```scss
background: rgba($black, .7);
/* 👆が👇になる */
background: rgba(0,0,0,.7);
```

-----

### @marginY

`margin`のY軸、つまり上下の余白です。
デフォルト値は40pxにしています。
条件を満たす場合、入力値の50%にしています。
```scss:_mixin.scss
@mixin marginY($value: 40px) {
  margin: $value 0;
  @if ($value > 60px) {
    @include sp {
      margin: calc($value / 2) 0;
    }
  }
}

@include marginY($value: 100px);
```

-----

### @contentsBody

コンテンツやセクションなどの幅に使用しています。
- `$calc`のデフォルト値は100px
- `$width`のデフォルト値は`variable.scss`ファイルで定義した`$pc-s`の値

```scss
width: min(100% - $calc, $width);
```

👆 は`$width`が `max-width` です。
:::message
ブラウザ幅が`$width`以下になった場合100%から`$calc`を引いた幅になります。
:::
-----
#### 例
```scss:_hoge-page.scss
.container {
  @include contentsBody($calc: 100px, $width: 1000px);
  @include marginY($value: 100px);
}
```

👆の`container`は`max-width`が1000px
ブラウザ幅が1000px以下になると左右に50pxずつ余白ができます。
上下`margin`が100px ブラウザ幅が`$sp`の値になると50px

![](/images/convenient-modern-css/img-02.png)

# CSS

![](/images/convenient-modern-css/img-01.png)
👆 のようなレイアウトの場合

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

## CSSの各説明

### gap
```css:style.css
width: calc((100% - (60px * 2)) / 3);
```
`inner`が何個になるかわからないときなどに使用します。
- 60pxは`gap`と合わせ、3カラムの場合はガターが 2つなので、`gap`\*2 をする
- 120pxが引かれるので、それを三分割で3カラムの出来上がり

### grid
```css:style.css
display: grid;
place-content: center;
```

何かを真ん中にしたい時に使用します。
- `inset` などのやり方もあると思いますが、👆を使用

### aspect-ratio
- 比率 1:1

## まとめ
プログラムみたいな`SCSS`がこれ以上増えると管理しづらく、パッと見でわからなくなるので、もう増やさないと思います。
今はTailWind、CSS in JS、emotion とかいっぱいあり、CSSもネストができるようになりました。
現在は、TailWindを使用し始めたり、Nextを勉強中で、CSS in ModulesとTailWindを採用していて、
正直SCSSの優位性はほとんど感じていません。