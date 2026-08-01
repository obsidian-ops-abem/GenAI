---
title: "11_Obsidianのおすすめ機能と選ぶ理由"
tags: [summary, ai, claude-code, obsidian, pkm, local-first]
source: https://x.com/shotovim/status/2083175048802877486
author: shotovim (@shotovim)
published: 2026-07-30
created: 2026-08-01
---

# Obsidian のおすすめ機能と選ぶ理由

> **「ClaudeCode が〜みたいな話は、Obsidian がローカルファーストの設計でそうなっているだけ。Obsidian を使う理由にはならない」** — Claude Code との相性の良さは帰結であって理由ではない、と喝破する短い意見ポスト。

出典: [[41_Obsidianのおすすめ機能と選ぶ理由（出典）]]（shotovim @shotovim, 2026-07-30。本文はユーザー提供クリップから再構成）

> [!note] 情報量について
> 本ポストは Obsidian のおすすめ機能を7つ箇条書きで挙げた短い意見。情報量は限定的だが、本ボルトの運用そのもの（CLAUDE.md・3層構造・Obsidian Web Clipper による Clippings/ 取り込み等）と直接関連するため要約ページとして残す。

---

## 一行で

@shotovim による Obsidian のおすすめ機能ランキング（Git連携/内部リンク/Web Clipper/Bases/Canvas/プラグイン/デイリーノート）と、「Claude Code との相性は**ローカルファースト設計の帰結**であり、Obsidian を選ぶ独立した理由ではない」という指摘。

## おすすめ機能（個人順）

| 順 | 機能 | 本ボルトでの対応 |
|---|---|---|
| 1 | **Obsidian Git** の連携 | 本ボルトも git 管理下（vault backup commit が存在） |
| 2 | **内部リンク** | Wikilink（[[...]]）で全ページを接続。[[01_Wikilink]] 参照 |
| 3 | **Obsidian Web Clipper** | `Clippings/` フォルダの中身がまさに Web Clipper の成果物 |
| 4 | **Obsidian Bases** | （本ボルトでは未使用。v1.9 以降の組み込みデータベース機能。ノート群を表形式でフィルタ・ソート・グループ化する「動的インデックス」。`index.md` の手動カタログを自動化できる可能性あり） |
| 5 | **Canvas** | ルートに `無題のファイル.canvas` が存在（ノードを繋げる無限キャンバス。Concept Maps やフロー図用） |
| 6 | その他無数のプラグイン | — |
| 7 | **デイリーノート** | `02_wiki/daily/` に配置（[[01_2026-07-28]]） |

## 核心: 「ClaudeCode が〜」は理由にならない

> ClaudeCode が〜みたいな話は、Obsidian が**ローカルファーストの設計**でそうなっているだけなので、Obsidian を使う理由にはならない。

この指摘の含意:
- Claude Code（CLI エージェント）が**ローカルファイルを直接読み書きできる**のは、Obsidian が**ファイルシステム上のプレーン Markdown**（ローカルファースト）だから
- つまり「Claude Code との相性の良さ」は、Obsidian の設計思想（ローカルファースト・プレーンテキスト）が**たまたま CLI エージェントにとっても都合が良い**という帰結
- したがって「エージェントと連携できるから Obsidian」でなく、**ローカルファースト・プレーンテキスト・拡張性（プラグイン）等、Obsidian 本来の強み**を選ぶ理由にすべき、という立場

---

## 本ボルト内の位置付け

本ボルトの運用ルール（CLAUDE.md）はまさに「Obsidian = ローカルファーストのプレーン Markdown」を前提に設計されている:

- **3層構造**（raw-sources / wiki / to_delete）はすべてファイルシステム上のフォルダ → CLI エージェントが直接読み書き可能
- **Wikilink はファイル名（プレフィックス含む）** で張る → CLI からリネームしても追従可能（ただし手動。CLAUDE.md の warning 参照）
- **Obsidian Web Clipper** で取り込んだ `Clippings/` を起点に Ingest 処理 → 本ノート群もその一環
- **デイリーノート**・**Canvas**・**内部リンク** は本ボルトでも稼働中

[[09_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]]（Claude Code アカデミアによる実践版）が「Claude Code × Obsidian」の連携を正面から論じるのに対し、本ポストは**「連携できること自体は理由にならない」と冷やし**、Obsidian 本来の機能群を重新着目させる視点。両者は補完関係。

> [!note] コメント欄の逆説
> @kawai_design から「Obsidian の必要性が感じられず、インストールしては捨ててを繰り返している」との声。@shotovim のおすすめ機能ランキングは、こうした**「かっこいいけれど必要性が分からない」層への手がかり**としても読める。機能の羅列自体が「何ができるか」を可視化し、個々の用途（本ボルトなら AI 協業の第二の脳）と結びつく最初のきっかけを与える。

## 関連

- 本ボルトの運用ルール → [[CLAUDE]]（3層・Ingest/Query/Lint・命名規約）
- Claude Code × Obsidian の実践 → [[09_Claude Code×Obsidianで第二の脳を作る 2000時間の設計ルール]]・[[01_Claude×Obsidianで第二の脳を作る]]
- Wikilink 規約 → [[01_Wikilink]]・[[02_フロントマター]]
- 基本操作 → [[01_Obsidianの基本操作]]
