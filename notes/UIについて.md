---
tags:
  - テック
  - 日常
  - unity
---
uguiなどのuiを使う場合、**RectTransform**というvector2など2d前提のTransformのサブクラスを使う。[🔰uGUI - RectTransformのトゥイーン｜【Unity】DOTweenの教科書〜スクリプトでアニメーションを操るバイブル〜 (zenn.dev)](https://zenn.dev/ohbashunsuke/books/20200924-dotween-complete/viewer/dotween-16)
アンカーを考慮した移動メソッドが`DOAnchorPos`です。
通常の移動メソッド`DOLocalMove`を使うと思わぬ座標へ移動してしまう時があります。大抵そういう時は`アンカー`が中央ではなくなっていることが多いです。
uGUIで移動トゥイーンを実装する時は必ず**DOAnchorPos**使うことをオススメします。