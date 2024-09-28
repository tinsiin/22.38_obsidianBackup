#unity  #日常　#テック

ToUniTaskでc#のtaskを置き換えられる。
[【Unity】UniTaskのWhenAllは省略して書ける (zenn.dev)](https://zenn.dev/nitudon/articles/37592ea996c78c)
[【Unity】UniTask Coroutineとの違いとキャンセル処理の挙動 #Unity3D - Qiita](https://qiita.com/IShix/items/dcf86cb5ca1b587a88ad)

###### キャンセルトークン
whenalltasksには、canncell tokenを受け取るオーバーロードがないから、
tasks.addの時に各タスクに渡す必要がある。

あくまでやりたいことは今の所破棄された時に消したいだけだから、
**GetCancellationTokenOnDestroy**だけで成り立つかも。

**???**
また、クラスなどで負ってる処理だったら、そのクラス内で完結する
それぞれのクラスのdestroy時に消えるように。そのawaitはそのクラスに移ってるわけだからね処理が。**???**

###### キャンセルした後に処理をする場合
[UniTaskでtry-catchを書かずにキャンセルする方法｜PICKY (note.com)](https://note.com/what_is_picky/n/n5133b523c61c#e6bcb1ce-794c-4d09-b5ac-d50dce867c00)
[[UniTask] キャンセルされても処理を続ける (zenn.dev)](https://zenn.dev/murnana/articles/unitask-continue-with-cancel)