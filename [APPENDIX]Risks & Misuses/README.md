# LLMのリスクと誤用

## tl;dr
アプリケーション等における実用上のLLMのリスクと誤用についてまとめます。  
(デモのアプリケーションはPythonで動作しているものとします)  
(このコンテンツは教育目的で作成されたものであり、システムの悪用を助長する意図はありません)

![image](https://github.com/user-attachments/assets/c7bc90db-649a-4d30-8cc6-d3a2556ab828)

---

## 注意事項
- 本フォルダに格納された全ての内容は教育目的で編集されたものであり、犯罪行為を助長するものではありません。
- 本フォルダで学んだ手法を他サービス等にて実行した結果発生した責任について、本コンテンツの編集者は一切責任を負いません。

---

## 参考資料
- [リスクと誤用, DAIR.AI, Prompt Engineering Guide(2024)](https://www.promptingguide.ai/jp/risks)
- [Prompt Injection, Sander Schulhoff, learnprompting(2025)](https://learnprompting.org/docs/prompt_hacking/injection)
- [AI時代の到来に向けて!!大規模言語モデル(LLM)のサイバーセキュリティ, 井ケ田一貴, 株式会社マクニカ(2023)](https://www.macnica.co.jp/business/ai/blog/files/ai/pdf/20230817_LLM_security_macnica.pdf)
- [New Frontier of GenAI Threats: A Comprehensive Guide to Prompt Attacks, Palo Alto Networks(2025)](https://www.paloaltonetworks.com/blog/2025/04/new-frontier-of-genai-threats-a-comprehensive-guide-to-prompt-attacks/)
- [RAG（検索拡張生成）への攻撃手法｜社内利用のLLMが社外から狙われる脅威, 西田 助宏, NRIセキュア ブログ(2024)](https://www.nri-secure.co.jp/blog/indirect-prompt-injection)
- [敵対的プロンプト技術まとめ, @fuyu_quant(Toma Tanaka), Qiita(2024)](https://qiita.com/fuyu_quant/items/d9a44dfe3a7315f255ee)
- [AIのデータポイズニングとは何ですか, Cloudflare(2025)](https://www.cloudflare.com/ja-jp/learning/ai/data-poisoning/)
- [Prompt Injection Attacks in LLMs: What Are They and How to Prevent Them, Deval Shah, Coralogix(2024)](https://coralogix.com/ai-blog/prompt-injection-attacks-in-llms-what-are-they-and-how-to-prevent-them/)
- [H-CoT: Hijacking the Chain-of-Thought Safety Reasoning Mechanism to Jailbreak Large Reasoning Models, Including OpenAI o1/o3, DeepSeek-R1, and Gemini 2.0 Flash Thinking, Martin Kuo, arXiv(2025)](https://arxiv.org/html/2502.12893v1)
- [Adversarial Agents: Black-Box Evasion Attacks with Reinforcement Learning, Kyle Domico, arXiv(2025)](https://arxiv.org/html/2503.01734v1)
- [LLM07: Insecure Plugin Design, OWASP Foundation Inc.(2025)](https://genai.owasp.org/llmrisk2023-24/llm07-insecure-plugin-design/)
- [Prompt Flow Integrity to Prevent Privilege Escalation in LLM Agents, Juhee Kim, arXiv(2025)](https://arxiv.org/html/2503.15547v1)
