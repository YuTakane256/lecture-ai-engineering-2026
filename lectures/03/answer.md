# 第3回 解答

## 演習

- 第1回（素のLLM）とエージェントの回答の違い：
  第1回の素のLLMには講座専用の用語集を検索するツールがなかった。一方、第3回のエージェントは講座の「3レベル」について、基礎・実装・発展の3つを具体的に説明し、いずれか1つに取り組めば宿題が完了することを回答した。

- `--trace` で見えたツール呼び出し（ツール名と引数）：
  「LoRA とQLoRAの違いは？」では、search_glossary({'query': 'LoRA'}) と search_glossary({'query': 'QLoRA'}) が呼ばれた。
  「第2回の内容から確認問題を1問出して」では、make_quiz({'lecture': 2}) が呼ばれ、「過学習」の確認問題が生成された。

- 1つの質問での LLM 呼び出し回数（`requests`）：
  LoRAとQLoRAの比較では requests=2、tool_calls=2 だった。
  確認問題の生成では requests=2、tool_calls=1 だった。

- 追加したツールと、docstring に書いた説明：
  app/tools.py に make_quiz(lecture: int) を追加し、app/agent.py で登録した。
  docstringには、指定した回の用語集から確認問題を1問作り、問題と答えを返すこと、講義内容の復習を求められたときに使用することを記載した。
  動作確認では11件のテストがすべて成功した。

## 宿題
取り組んだレベル：基礎

変更内容： `app/agent.py` の `INSTRUCTIONS` から「用語や講座に固有のことを答える前に、必ず search_glossary で用語集を確認する。」という指示を削除した。

実験方法：変更前後で `uv run python -m app.agent --trace "LoRA とQLoRAの違いは？"` を実行し、ツール呼び出しと回答を比較した。

結果： 変更前後とも `search_glossary` が `LoRA` と `QLoRA` を引数として計2回呼び出され、`requests=2`、`tool_calls=2` だった。回答もどちらも概ね正確で、明確な動作の違いは確認できなかった。

考察： `search_glossary` のdocstringにも「答える前に使うこと」と記載されているため、全体の指示を削除してもモデルが検索の必要性を判断した可能性がある。エージェントの動作には、全体の指示だけでなく各ツールの説明文も影響することが分かった。