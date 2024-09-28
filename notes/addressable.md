#unity  #日常　#テック


##### ロードの仕方
公式ドキュメントからの引用　
	[LoadAssetAsync](https://docs.unity3d.com/ja/Packages/com.unity.addressables@1.20/api/UnityEngine.AddressableAssets.Addressables.LoadAssetAsync.html) などの関数を呼び出した場合、ロードされたアセットが直接返されるわけではありません。代わりに [AsyncOperationHandle](https://docs.unity3d.com/ja/Packages/com.unity.addressables@1.20/api/UnityEngine.ResourceManagement.AsyncOperations.AsyncOperationHandle.html) オブジェクトが返され、ロードされたアセットが使用可能になると、このオブジェクトを使用してアクセスすることができます。

AsyncOparationHandleを利用した際に、アセットが直接帰ってこないので
**resultを使うと結果が返り、taskで非同期処理を完了できる**。

流れでいうと、(==同期的に早くやるやり方==)
1. Addressables.LoadAssetAsync<型>(アドレス)　でロード
2. await (上記のhandle入った変数).task で 完了させる。
3. handle.result で取得したアセットを取り出す。


##### unitaskへの置き換え
```c#
await textHandle.Task();//非同期ロードを同期的に完了させる
```
このtask部分を
```c#
await textHandle.ToUniTask();//非同期ロードを同期的に完了させる

```
ToUniTask()にすることで処理を[[unitask]]に置き換えられる。==高速==

## リンク集
[操作 | Addressables | 1.20.5 (unity3d.com)](https://docs.unity3d.com/ja/Packages/com.unity.addressables@1.20/manual/AddressableAssetsAsyncOperationHandle.html)