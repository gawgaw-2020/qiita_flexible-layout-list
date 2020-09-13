# 【flexbox】space-betweenで最後の行を左から揃える

メモアプリを作る中で、カード型アイテムの配置に関して色々調べたのでメモ。
対処方法は、前提条件によって変わってきます。

今回は、
「カラム数が決まっていない（画面幅で増減）」
「アイテム幅は固定」
「幅を狭めても最低限の余白を確保、それ以上は均等に配置」
となっています。

最後にspace-between関係ないですが、
「カラム数：固定」
「アイテム幅：可変」
「余白：固定」
のバージョンもメモとして残しています。

## 困った

数日前にflexboxを知った私は、何でもかんでもflexboxで対処しようとしていました。

アプリでメモを増やしていくと、

<img width="60%" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697564/c01b3b60-73ed-cd8f-60fc-6cb9c60d3b45.png">

<img width="80%" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697564/431d01d4-c439-27a2-be7c-6ae404f8b958.png">

最後の行が均等に並んでしまい、左から並んでくれません。（そういうものなのですが）

```html:sample.html
<h1>space-betweenで最後の行が左から揃わない</h1>
  <ul class="item-list">
    <li class="item"></li>
    <li class="item"></li>
    <li class="item"></li>
    <li class="item"></li>
    <li class="item"></li>
    <!-- データベースによって増減 -->
  </ul>

```
```css:style.css
ul.item-list {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
}

li.item {
  width: 160px;
  height: 160px;
  background-color: crimson;
  /* 最低限の余白 */
  margin: 0 5px 30px;
}

```

## 今回用いた方法 空いた空間に透明な要素を並べる

今回はこちらで対処しました。JavaScriptで空のitemを生成しています。

headタグでjQueryを読み込んで、以下のコードを記述。

```javascript:sample.js
    var $grid = $('.item-list'),   
    emptyCells = [],
    i;

    // アイテム (li.item) の数だけ空のアイテム (li.cell.is-empty) を生成
    for (i = 0; i < $grid.find('.item').length; i++) {
        emptyCells.push($('<li>', { class: 'item is-empty' }));
    }

    $grid.append(emptyCells);
```
空のアイテム用のCSSを追加

```css:sample.css
/* 追加された<li>要素のスタイルを追加 */

.item.is-empty {
  height: 0;
  padding-top: 0;
  padding-bottom: 0;
  margin-top: 0;
  margin-bottom: 0;
}
```
下記画像が対処後です。新たに空のアイテムが生成されています。空間が埋まり、結果要素が左から並ぶようになりました。

<img width="80%" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697564/4663be46-1644-f8f2-5129-aab7b880d517.png">



## space-between使わない

何でもかんでもspace-betweenとか使わなくてもいいように。
カラム数は4、要素幅を可変にして最低限の間隔を持たせます。

アイテムの幅をCSSで計算してもらいます。

```css:sample.css
ul.item-list {
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  /* justify-content: space-between; */
}

li.item {

  width: calc(100% / 4 - (5px * 2));
  height: 160px;
  background-color: crimson;
  /* 最低限の余白 */
  margin: 0 5px 30px;
}
```

固定の余白を保ったまま、画面幅によって各要素の幅が変化するようになりました。

<img width="60%" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697564/e9d6e976-db2a-c467-2a1d-cc741b1c18b1.png">

<img width="80%" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/697564/f98e4f03-5a48-010a-1651-e424a625865f.png">



calcすごい便利ですね。
``width: calc(100% / 4 - (5px * 2));``
上記のコードでは、アイテムの幅は親要素の1/4、それだけだとカラム落ちしちゃうので、左右の余白ぶん追加でマイナスしています。

IEだとカラム落ちするという記事にヒットしたので置いておきます...

calc関数で指定をした際に、IEでカラム落ちしてしまった時の対処法
https://www.marineroad.com/staff-blog/22636.html

## 対処法の決め方
他にも疑似要素を用いた物などがヒットしました。カラムが3〜4列の場合はこちらで対応できそうです。

均等配置したリストの最終行を、左寄せにする方法（ justify-content: space-betweenを使った場合 ）
https://taneppa.net/flexbox_list_left/

デザインがあればそれに対し、

* 要素は追加になる可能性があるのか
* アイテム幅は可変なのか
* 1〜2個になる可能性は??
* 要素が増えたらページネーションになるのか

などをしっかり把握してからコーディングするのが大事だなと感じました。

今度は「grid」を用いた方法を試してみたいと思います!!
