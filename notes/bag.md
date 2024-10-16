主なバグ:
VertexHelperを用いてカスタムメッシュ描画をしているが、その描画がゲームビューにのみ映り、
シーンビューには描画されない。

コード:
```
using UnityEngine;
using UnityEngine.UI;
using System.Collections.Generic;

[ExecuteAlways]
public class UILineRenderer : Graphic
{
    [System.Serializable]
    public class LineData
    {
        public Vector2 startPoint;
        public Vector2 endPoint;
    }

    public List<LineData> lines = new List<LineData>();

    public float thickness = 5f;
    public Color lineColor = Color.white;
    public Color two = Color.blue;

    protected override void OnPopulateMesh(VertexHelper vh)
    {
        vh.Clear();

        // Draw each line
        foreach (var line in lines)
        {
            DrawLine(vh, line);
        }
    }

    void DrawLine(VertexHelper vh, LineData line)
    {
        Vector2 direction = (line.endPoint - line.startPoint).normalized;
        Vector2 normal = new Vector2(-direction.y, direction.x) * (thickness * 0.5f);

        Vector2[] outerVertices = new Vector2[2];
        Vector2[] innerVertices = new Vector2[2];

        outerVertices[0] = line.startPoint + normal;
        outerVertices[1] = line.endPoint + normal;
        innerVertices[0] = line.startPoint - normal;
        innerVertices[1] = line.endPoint - normal;

        // Add vertices as a quad
        AddQuad(vh, outerVertices[0], innerVertices[0], innerVertices[1], outerVertices[1], new Color[] { lineColor, lineColor, two, two });
    }

    void AddQuad(VertexHelper vh, Vector2 v1, Vector2 v2, Vector2 v3, Vector2 v4, Color[] color)
    {
        int idx = vh.currentVertCount;

        vh.AddVert(v1, color[0], Vector2.zero);
        vh.AddVert(v2, color[1], Vector2.zero);
        vh.AddVert(v3, color[2], Vector2.zero);
        vh.AddVert(v4, color[3], Vector2.zero);

        vh.AddTriangle(idx, idx + 1, idx + 2);
        vh.AddTriangle(idx + 2, idx + 3, idx);
    }
}

```
状況について:
prefabにこのスクリプトをアタッチして
prefabを生成した際は描画されます。
シーンビュー、ゲームビューどちらでも。

ところが直接GameObjectにアタッチして描画した際、
ゲームビューでは描画されるのにシーンビューでは一切
描画されません。
ゲームビューで頑張ればいいだけですが、
もし同様の事を解決した方がいればご教授お願い致します。

