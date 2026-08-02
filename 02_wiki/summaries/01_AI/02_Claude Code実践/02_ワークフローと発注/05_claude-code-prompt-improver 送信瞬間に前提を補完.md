---
title: "05_claude-code-prompt-improver 送信瞬間に前提を補完"
tags: [summary, ai, claude-code, hook, prompt-engineering, workflow, oss]
source: https://x.com/opensourcelab9/status/2083681691537961218
author: opensourcelab9（@opensourcelab9）
repo: https://github.com/severity1/claude-code-prompt-improver
published: 2026-08-02
created: 2026-08-02
---

# claude-code-prompt-improver — 送信瞬間に前提を補完

> **Claude Code への曖昧指示を、送信瞬間に3か所へ割り込んで「足りない前提」を先に注ぐフック（severity1/claude-code-prompt-improver・★1831・MIT・Python）。言葉を書き直すのでなく、後から手で足すはずだった条件を最初から積む。一発目の質が上がり直しの往復が減る。**

出典: [[69_claude-code-prompt-improver 曖昧指示を送信瞬間に補完（出典）]]（@opensourcelab9, 2026-08-02。本文はクリップ全文を使用。※紹介ポストのため詳細はリポジトリ要確認）

---

## 一行で

severity1/claude-code-prompt-improver（★1831・MIT・Python）の紹介。Claude Code の入力送信瞬間にフックが割り込み、曖昧指示へ「足りない前提」を自動で先に注ぐ。直しの往復を減らす。

## 3か所への割り込み（核心）

| タイミング | 割り込み内容 |
|---|---|
| **曖昧な指示** | 1〜6個の質問を返す（明確な指示は素通し） |
| **重い依頼（実装・移行）** | 進め方（分担か自分でやるか）を先に決めさせる |
| **長く走るコマンド** | 裏で動かし・必要な出力だけ拾う指示を入れる |
| **大きな成果物** | 結論先出し・表・簡潔さの型を差し込む |

## 思想

- **言葉を書き直すのでなく**、後から手で足すはずだった条件を**最初から積んでおく**
- 一発目の質が上がるぶん、直しの往復が減る
- フック1個を入れるだけで今日から動く（MIT・Python）

---

## 本ボルト内の位置付け

- **「曖昧指示に質問を返す・完了条件を先に決める」** は [[04_Claudeはorchestrator専念 hook強制の分業]]（@cursorvers リプライ「質問は選択肢で・着手前に完了条件を必ず決める」）・[[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（spec の Constraints/Out of scope・5ステップループの Generate→Review）と同思想のツール化
- **「フックで構造的に強制」** は [[04_Claudeはorchestrator専念 hook強制の分業]]（hook で Claude 直接編集を block・分業を構造で守る）・[[03_CLINEに全部賭けろ コーディングエージェント時代のプログラマ]]（.clinerules・設定は安全網で設計ではない）と同系。「心がけでなくフックで」
- **「直しの往復を減らす」** は [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]（vibe coding の「行き来で隠された決定」を spec で先取り）・[[02_Claude Code 計画と実行を分けるワークフロー]]（計画と実行の分離で手戻り激減）の自動化版
- **「結論先出し・表・簡潔さの型」** は [[03_AI協業の発注の型 HITL実務]]（結論ファースト）・[[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]（8プロンプトの「目標と成功基準・いつ止まるか」）と同じ出力形式の規律
- **フック系ツール** → [[01_agent-skill-creator ワークフローをAIスキル化]]・[[05_openai codex-plugin-cc Claude CodeからCodexへ委譲]]（プラグイン/フック系）と並ぶ Claude Code 拡張層

---

## 関連

- フックで分業強制（心がけでなく構造） → [[04_Claudeはorchestrator専念 hook強制の分業]]
- spec で曖昧さを先取り（直しの往復減） → [[04_Stop Vibe Coding Spec駆動開発の5ブロック]]
- 計画と実行の分離 → [[02_Claude Code 計画と実行を分けるワークフロー]]
- 着手前に完了条件 → [[03_AI協業の発注の型 HITL実務]]
- 出力の型（結論先出し・簡潔） → [[07_Everything Fable 5 Mythosクラスとプロンプトガイド]]
- フック/プラグイン系ツール → [[01_agent-skill-creator ワークフローをAIスキル化]]
