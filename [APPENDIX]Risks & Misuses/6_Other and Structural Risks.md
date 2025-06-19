# 6. その他のリスク・設計上の問題（Other/Structural Risks）

## 幻覚(ハルシネーション:Hallucination)
モデルが事実に基づかない情報や、実在しない内容を自信満々に生成してしまう現象。

```python
prompt = f"""
  ＜指示＞
  「日本で最も高い山の名前を教えてください。」

  ＜入力＞
  {user_input}
"""
user_input = "日本で最も高い山の名前を教えてください。"
```
実行結果
```text
日本で最も高い山は「エベレスト」です。
```

## バイアス（Bias）
モデルが十分な知識や経験を持たないタスク（例：複雑な推論や専門的な判断）では、few-shot promptingによって与えられた少数例から得られる傾向が強く反映されてしまい、モデルの出力が大きく揺らぎやすい。

例１：答えが真になる問題で偽という出力が出てしまう
```python
prompt = f"""
  ＜指示＞
  今から以下の問題に回答してください。

  ＜問題＞
  このグループの奇数を足すと偶数になる： 3, 5, 7
  A: 答えは偽。

  このグループの奇数を足すと偶数になる： 1, 9, 13
  A: 答えは偽。

  このグループの奇数を足すと偶数になる： 2, 4, 8
  A: 答えは偽。

  このグループの奇数を足すと偶数になる： 5, 11, 13, 9
  A:
"""
```
実行結果
```text
答えは偽。
```

例２：答えが偽になる問題で真という出力が出てしまう
```python
prompt = f"""
  ＜指示＞
  今から以下の問題に回答してください。

  ＜問題＞
  このグループの奇数を足すと偶数になる： 3, 5, 7, 9
  A: 答えは真。

  このグループの奇数は偶数になる： 1, 9, 13, 5
  A: 答えは真。

  このグループの奇数を足すと偶数になる： 2, 4, 8, 6
  A: 答えは真。

  このグループの奇数は偶数になる： 7, 11, 15
  A:
"""
```
実行結果
```text
答えは真。
```


## 防衛策
### 防御戦略の基本原則

1. **外部ファクトチェックAPIとの連携**  
　モデル出力を信頼できる外部知識ベースやファクトチェックAPIで検証し、誤情報を自動訂正。  
　→ 幻覚（ハルシネーション）対策

2. **多様なFew-shot例・バランスサンプリング**  
　プロンプト設計時に多様な例・正誤混在例をバランス良く提示し、バイアスの偏りを抑制。  
　→ バイアス対策

3. **出力の信頼度スコアリングと警告表示**  
　モデル出力に自己信頼度やファクト一致度を付与し、低信頼度の場合は警告を表示。  
　→ 幻覚・バイアス両方に有効

4. **継続的な人間レビューとフィードバックループ**  
　重要な出力や判定は人間がレビューし、誤りがあればモデルやプロンプト設計に反映。  
　→ 幻覚・バイアス両方に有効


```python
import re
import requests
import numpy as np

class HallucinationBiasDefense:
    def __init__(self):
        # 外部ファクトチェックAPI（例: Wikipedia APIやGoogle Fact Check Tools等）（幻覚（ハルシネーション）対策）
        self.fact_check_api = "https://ja.wikipedia.org/w/api.php"
        # 信頼度閾値
        self.confidence_threshold = 0.7

    def fact_check(self, text):
        """
        幻覚対策: 出力内容をWikipediaでファクトチェック
        """
        # 例: 単純なキーワード検索
        keyword = self._extract_keyword(text)
        if not keyword:
            return False, 0.0
        params = {
            "action": "query",
            "list": "search",
            "srsearch": keyword,
            "format": "json"
        }
        try:
            resp = requests.get(self.fact_check_api, params=params, timeout=3)
            data = resp.json()
            hits = data.get("query", {}).get("search", [])
            # 検索結果の有無でファクト一致度を計算
            confidence = min(len(hits) / 3, 1.0)
            fact_match = confidence > self.confidence_threshold
            return fact_match, confidence
        except Exception:
            # APIエラー時は低信頼
            return False, 0.0

    def _extract_keyword(self, text):
        """
        質問文や出力から主要キーワード抽出（例: 固有名詞や地名）
        """
        # 「日本で最も高い山」などを抽出
        match = re.search(r"(日本で最も高い山|富士山|エベレスト|山)", text)
        return match.group(1) if match else None

    def balance_fewshot_examples(self, examples):
        """
        バイアス対策: 多様なfew-shot例をバランス良く提示（バイアス対策）
        """
        # 例: 正解/不正解が均等になるようリサンプリング
        pos = [ex for ex in examples if "真" in ex]
        neg = [ex for ex in examples if "偽" in ex]
        n = min(len(pos), len(neg))
        balanced = pos[:n] + neg[:n]
        np.random.shuffle(balanced)
        return balanced

    def score_and_alert(self, output, confidence):
        """
        信頼度スコアと警告表示（幻覚・バイアス両方に有効）
        """
        if confidence < self.confidence_threshold:
            return f"⚠️ 注意: この出力は事実と異なる可能性があります。\n{output}"
        return output

# 使用例
defense = HallucinationBiasDefense()

# 幻覚例
output = "日本で最も高い山は「エベレスト」です。"
fact_match, conf = defense.fact_check(output)
print("幻覚対策:", defense.score_and_alert(output, conf))

# バイアス例
fewshot_examples = [
    "このグループの奇数を足すと偶数になる： 3, 5, 7\nA: 答えは偽。",
    "このグループの奇数を足すと偶数になる： 1, 9, 13\nA: 答えは偽。",
    "このグループの奇数を足すと偶数になる： 2, 4, 8\nA: 答えは偽。",
    "このグループの奇数を足すと偶数になる： 5, 11, 13, 9\nA: 答えは真。",
    "このグループの奇数を足すと偶数になる： 3, 5, 7, 9\nA: 答えは真。"
]
balanced_examples = defense.balance_fewshot_examples(fewshot_examples)
print("バランス調整後のfew-shot例:", balanced_examples)
```
