# デフォルトのデータを辞書内に保持して組み合わせ指定をしないものでもパーティー属性を補う処理
**質問:** 辞書データにデフォルトってある？キーにないものは自動でデフォルトで返すデータみたいなの。

---

**回答:**

C#の標準的な`Dictionary<TKey, TValue>`では、キーが存在しない場合にデフォルト値を自動で返すような仕組みは組み込まれていません。しかし、いくつかの方法で、辞書に存在しないキーに対して**デフォルト値を返すように**実装することができます。

---

### **方法1: `TryGetValue`メソッドを使用する**

`Dictionary.TryGetValue`メソッドを使うと、指定したキーが存在するかどうかをチェックし、存在しない場合にデフォルト値を返すことができます。

```csharp
using System;
using System.Collections.Generic;

public class Example
{
    public static void Main()
    {
        Dictionary<string, int> scores = new Dictionary<string, int>()
        {
            { "Alice", 100 },
            { "Bob", 95 }
        };

        // デフォルト値として0を設定
        int defaultValue = 0;

        // 存在しないキーの場合にデフォルト値を返す
        if (scores.TryGetValue("Charlie", out int score))
        {
            Console.WriteLine("Charlie's score: " + score);
        }
        else
        {
            Console.WriteLine("Charlie's score (default): " + defaultValue);
        }
    }
}
```

**出力:**

```
Charlie's score (default): 0
```

---

### **方法2: 拡張メソッドを使ってデフォルト値を返す**

`TryGetValue`を毎回使うのは少し手間なので、拡張メソッドを作成して、キーが存在しない場合に自動でデフォルト値を返すようにカスタマイズすることができます。

```csharp
using System;
using System.Collections.Generic;

public static class DictionaryExtensions
{
    // 辞書に存在しないキーの場合にデフォルト値を返す拡張メソッド
    public static TValue GetOrDefault<TKey, TValue>(this Dictionary<TKey, TValue> dictionary, TKey key, TValue defaultValue = default)
    {
        return dictionary.TryGetValue(key, out TValue value) ? value : defaultValue;
    }
}

public class Example
{
    public static void Main()
    {
        Dictionary<string, int> scores = new Dictionary<string, int>()
        {
            { "Alice", 100 },
            { "Bob", 95 }
        };

        // 拡張メソッドを使用してデフォルト値を返す
        int charlieScore = scores.GetOrDefault("Charlie", 0);
        Console.WriteLine("Charlie's score: " + charlieScore);
    }
}
```

**出力:**

```
Charlie's score: 0
```

### **方法3: `DefaultDictionary`クラスを作成する**

Pythonの`defaultdict`のような動作をするクラスを自分で実装することも可能です。以下は、指定されたデフォルト値を返すカスタム辞書クラスの例です。

```csharp
using System;
using System.Collections.Generic;

public class DefaultDictionary<TKey, TValue> : Dictionary<TKey, TValue>
{
    private readonly TValue _defaultValue;

    public DefaultDictionary(TValue defaultValue)
    {
        _defaultValue = defaultValue;
    }

    // インデクサをオーバーライドして、存在しないキーの場合にデフォルト値を返す
    public new TValue this[TKey key]
    {
        get
        {
            return TryGetValue(key, out TValue value) ? value : _defaultValue;
        }
        set
        {
            base[key] = value;
        }
    }
}

public class Example
{
    public static void Main()
    {
        // デフォルト値を指定してDefaultDictionaryを作成
        var scores = new DefaultDictionary<string, int>(0)
        {
            { "Alice", 100 },
            { "Bob", 95 }
        };

        // 存在するキーにアクセス
        Console.WriteLine("Alice's score: " + scores["Alice"]);

        // 存在しないキーにアクセス → デフォルト値を返す
        Console.WriteLine("Charlie's score (default): " + scores["Charlie"]);
    }
}
```

**出力:**

```
Alice's score: 100
Charlie's score (default): 0
```

---

### **まとめ**

- **標準の`Dictionary`**では、キーが存在しない場合に自動的にデフォルト値を返す機能はありません。
- 代わりに、`TryGetValue`を使ってデフォルト値を明示的に設定するか、拡張メソッドやカスタムクラスを作成することで、デフォルト値を返す仕組みを実装できます。
- **パフォーマンス**やコードの読みやすさに応じて、適切な方法を選ぶとよいでしょう。

ご不明な点があれば、遠慮なくお知らせください！

# 可変式のタプルキーとキーの正規化による組み合わせによる値の算出
### **質問1: タプルのキーが3つに増えた場合の正規化ロジック**

タプルのキーが2つではなく、**3つの要素を持つ**場合でも、同様に正規化（ソート）して順序を無視して扱うことができます。タプルの中の要素が2つ以上になった場合、**ソートされたリスト**を作ることで、順序を無視した一意のキーに変換できます。

#### **実装例: 3つのタプルの正規化**

例えば、タプルが `(A, B, C)` と `(C, A, B)` のように3つの要素を持つ場合も、タプルの要素を**ソート**して辞書に格納・検索することができます。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public enum SpiritualProperty
{
    liminalwhitetile, pillar, baledrival, kindergarden, cquiest, sacrifaith, devil, doremis, pysco, godtier
}

public class Example
{
    // 順序を無視して三つのタプルを管理するために、ソートして辞書に保存
    private static Dictionary<(SpiritualProperty, SpiritualProperty, SpiritualProperty), int> ImpressionMatchupTable = new Dictionary<(SpiritualProperty, SpiritualProperty, SpiritualProperty), int>
    {
        { NormalizeKey(SpiritualProperty.devil, SpiritualProperty.cquiest, SpiritualProperty.pillar), 100 },
        { NormalizeKey(SpiritualProperty.baledrival, SpiritualProperty.godtier, SpiritualProperty.pysco), 150 }
    };

    // 三つのタプルをソートして標準化するメソッド
    private static (SpiritualProperty, SpiritualProperty, SpiritualProperty) NormalizeKey(SpiritualProperty first, SpiritualProperty second, SpiritualProperty third)
    {
        // ソートしてタプルに戻す
        var sorted = new List<SpiritualProperty> { first, second, third }.OrderBy(x => x).ToList();
        return (sorted[0], sorted[1], sorted[2]);
    }

    public static void Main()
    {
        // キーの順序を無視してデータを取得する例
        var result1 = GetValueFromDictionary(SpiritualProperty.pillar, SpiritualProperty.devil, SpiritualProperty.cquiest);  // 100
        var result2 = GetValueFromDictionary(SpiritualProperty.pysco, SpiritualProperty.baledrival, SpiritualProperty.godtier);  // 150

        Console.WriteLine("Result1: " + result1);
        Console.WriteLine("Result2: " + result2);
    }

    // 辞書から値を取得するメソッド
    public static int GetValueFromDictionary(SpiritualProperty first, SpiritualProperty second, SpiritualProperty third)
    {
        var normalizedKey = NormalizeKey(first, second, third);

        if (ImpressionMatchupTable.TryGetValue(normalizedKey, out int value))
        {
            return value;
        }
        else
        {
            throw new Exception("Key not found");
        }
    }
}
```

#### **解説**

- `NormalizeKey`メソッドで、3つのタプルをリストに入れてからソートし、正規化しています。
- 辞書のキーとして、ソート済みのタプル `(A, B, C)` を使用しているため、どの順序で渡されても同じ結果が返ります。

#### **出力**

```
Result1: 100
Result2: 150
```

---

### **質問2: 渡されるキーの数が2つでも3つでも可変できる辞書のようなデータを作れるか？**

キーの数が可変（2つまたは3つ）で、どちらの場合でも対応できる辞書のようなデータ構造を作ることも可能です。このような場合、**`List`や`HashSet`をキーとして使う**ことで、柔軟にキーの数を処理できるようにする方法があります。

#### **手順**

1. **`List`や`HashSet`をキーとして使う**: `HashSet`は順序を無視した一意の集合を表現できるため、順序やキーの数を気にせずに扱うことができます。また、`List`を使うことで柔軟なキー管理が可能です。
   
2. **キーを標準化して扱う**: 2つでも3つでも、キーの要素数に応じて正規化（ソートなど）して辞書にアクセスする。

#### **実装例: 可変長のキーに対応する辞書**

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Example
{
    // Listをキーにして辞書を作成
    private static Dictionary<string, int> ImpressionMatchupTable = new Dictionary<string, int>
    {
        { NormalizeKey(new List<string> { "devil", "cquiest" }), 60 },
        { NormalizeKey(new List<string> { "baledrival", "pillar" }), 90 },
        { NormalizeKey(new List<string> { "pillar", "sacrifaith", "godtier" }), 120 }
    };

    // キーの要素数が可変なので、リストをソートして文字列に変換し、一意なキーとして扱う
    private static string NormalizeKey(List<string> keys)
    {
        keys.Sort();  // ソートして順序を無視
        return string.Join(",", keys);  // カンマ区切りで一意のキーとして扱う
    }

    public static void Main()
    {
        // 可変長のキー（2つまたは3つ）でデータを取得
        var result1 = GetValueFromDictionary(new List<string> { "cquiest", "devil" });  // 60
        var result2 = GetValueFromDictionary(new List<string> { "pillar", "baledrival" });  // 90
        var result3 = GetValueFromDictionary(new List<string> { "godtier", "sacrifaith", "pillar" });  // 120

        Console.WriteLine("Result1: " + result1);
        Console.WriteLine("Result2: " + result2);
        Console.WriteLine("Result3: " + result3);
    }

    // 辞書から値を取得するメソッド（可変長のキーに対応）
    public static int GetValueFromDictionary(List<string> keys)
    {
        var normalizedKey = NormalizeKey(keys);

        if (ImpressionMatchupTable.TryGetValue(normalizedKey, out int value))
        {
            return value;
        }
        else
        {
            throw new Exception("Key not found");
        }
    }
}
```

#### **解説**

- キーはリストで渡され、そのリストを**ソートしてから**一意の文字列として辞書に格納しています。これにより、2つでも3つでも任意の長さのキーを扱えるようになります。
- 辞書から値を取得する際も、渡されたキーリストをソートして標準化しています。

#### **出力**

```
Result1: 60
Result2: 90
Result3: 120
```

---

### **まとめ**

- **3つ以上のタプルを扱う場合**: タプルの要素をリストにしてソートすることで、順序を無視した正規化が可能です。
- **可変長のキーに対応する辞書**: `List<string>`や`HashSet<string>`をキーに使うことで、2つでも3つでもキーの数が可変な辞書を実現できます。ソートして一意のキーを作成することで順序も無視できます。

この方法により、柔軟なキー処理と順序無視のアクセスが可能です。さらに詳細なロジックや追加の質問があれば、ぜひお知らせください！

# さらにspiritualpropertyのリストで対応してもらうようにした
`SpiritualProperty`を可変的なキーとして扱えるようにするには、`List<SpiritualProperty>`や`HashSet<SpiritualProperty>`を使用し、それを順序に依存しない形で辞書のキーとして扱うことができます。

以下は、`SpiritualProperty`を可変長のキーとして扱い、そのキーに対応する値を取得できるようにする方法です。具体的には、キーとなる`SpiritualProperty`のリストを**ソート**して一意のキーに変換し、それをもとに辞書を参照する形で実装します。

---

### **実装例: `SpiritualProperty`の可変長キーを使った辞書**

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

// 列挙型 SpiritualProperty の定義
public enum SpiritualProperty
{
    liminalwhitetile, pillar, baledrival, kindergarden, cquiest, sacrifaith, devil, doremis, pysco, godtier
}

public class Example
{
    // SpiritualPropertyのリストをキーにして辞書を作成
    private static Dictionary<string, int> ImpressionMatchupTable = new Dictionary<string, int>
    {
        { NormalizeKey(new List<SpiritualProperty> { SpiritualProperty.devil, SpiritualProperty.cquiest }), 60 },
        { NormalizeKey(new List<SpiritualProperty> { SpiritualProperty.baledrival, SpiritualProperty.pillar }), 90 },
        { NormalizeKey(new List<SpiritualProperty> { SpiritualProperty.pillar, SpiritualProperty.sacrifaith, SpiritualProperty.godtier }), 120 }
    };

    // リストをソートして、カンマ区切りの文字列に変換することでキーを標準化
    private static string NormalizeKey(List<SpiritualProperty> keys)
    {
        keys.Sort();  // 順序を無視するためソート
        return string.Join(",", keys);  // カンマ区切りで一意のキーとして扱う
    }

    public static void Main()
    {
        // 可変長のキー（2つまたは3つ）でデータを取得
        var result1 = GetValueFromDictionary(new List<SpiritualProperty> { SpiritualProperty.cquiest, SpiritualProperty.devil });  // 60
        var result2 = GetValueFromDictionary(new List<SpiritualProperty> { SpiritualProperty.pillar, SpiritualProperty.baledrival });  // 90
        var result3 = GetValueFromDictionary(new List<SpiritualProperty> { SpiritualProperty.godtier, SpiritualProperty.sacrifaith, SpiritualProperty.pillar });  // 120

        Console.WriteLine("Result1: " + result1);
        Console.WriteLine("Result2: " + result2);
        Console.WriteLine("Result3: " + result3);
    }

    // 可変長のキーを使って辞書から値を取得するメソッド
    public static int GetValueFromDictionary(List<SpiritualProperty> keys)
    {
        // リストをソートして標準化し、辞書のキーとして扱う
        var normalizedKey = NormalizeKey(keys);

        if (ImpressionMatchupTable.TryGetValue(normalizedKey, out int value))
        {
            return value;
        }
        else
        {
            throw new Exception("Key not found");
        }
    }
}
```

### **ポイント**

1. **キーの正規化**: `NormalizeKey`メソッドでは、`SpiritualProperty`のリストをソートし、カンマ区切りの文字列に変換して、一意のキーとして辞書に格納しています。このため、キーの順序を無視してデータを取得できます。

2. **可変長のキー**: `List<SpiritualProperty>`を使うことで、キーの要素数（2つでも3つでも可変）に対応できます。

### **実行結果**

```
Result1: 60
Result2: 90
Result3: 120
```

---

### **解説**

- **キーの順序を無視**: `SpiritualProperty`のリストはソートされてから辞書に登録されるため、辞書にアクセスするときのキーの順序は無視されます。例えば、`[SpiritualProperty.devil, SpiritualProperty.cquiest]` と `[SpiritualProperty.cquiest, SpiritualProperty.devil]` は同じキーとして扱われます。
  
- **可変長キーの対応**: `List<SpiritualProperty>`を使用することで、キーの長さを自由に変えることができ、2つ、3つ、またはそれ以上の要素をキーにできます。リストをソートしてカンマ区切りの文字列に変換することで、辞書のキーとして一意に管理されます。

---

### **応用: 柔軟な拡張**

この構造は以下のように拡張できます。

1. **複雑なロジックの追加**: 取得時に特定の条件を加えることが可能です（例えば、部分一致や条件付きで辞書を検索するなど）。
   
2. **辞書内のデータを動的に変更**: `Dictionary.Add` を使って、実行時に新しい組み合わせを追加できます。

```csharp
ImpressionMatchupTable.Add(NormalizeKey(new List<SpiritualProperty> { SpiritualProperty.doremis, SpiritualProperty.pysco }), 45);
```

---

### **まとめ**

- `SpiritualProperty`を可変長のキーとして扱う場合、`List<SpiritualProperty>`や`HashSet<SpiritualProperty>`を使って、順序を無視した辞書データ構造を作ることができます。
- リストを**ソートして**カンマ区切りの文字列として扱うことで、順序に依存しないデータアクセスが可能です。
- これにより、2つでも3つでも可変のキーに対応したデータ取得が実現できます。

このアプローチで問題が解決するかと思いますが、もしさらに詳細な質問や実装の改善があれば、ぜひお知らせください！