# claude-cxo — あなたの「経営会議」を再現する事業戦略アシスタント

新規アプリ／新規事業のアイデアを投げると、Claude Code 上で**経営会議**が開かれます。
5人の部門長（Subagent）が **市場調査・競合分析・技術実現性・顧客ペルソナ・マネタイズ** を持ち寄り、
**社外取締役（批判役）が容赦なく詰め**、**オーナー（あなた）が質疑**し、議長である CXO Claude が
全員の意見を統合して**事業計画書**と**Lean Canvas**を生成、最後に **GO / PIVOT / NO-GO** の投資判断を提示します。

会議の成果物は、そのまま **AI-DLC（AI-Driven Development Lifecycle）の Inception への入力**として使える
設計です（技術の詳細設計・工数見積りは AI-DLC 側で行うため、会議は「賭けの評価」までに留めます）。

> イエスマンだけの会議にならないよう、あえて「反対する役」を常設しているのが特徴です。
> 健全な反対意見を乗り越えた計画こそ、投資に値します。

このリポジトリは、その一式（Subagent と Skill）を**組織に配布するためのテンプレート正本**です。
各自がこのリポジトリを入手し、使いたいプロジェクトの `.claude/` にコピーして起動します。

---

## このリポジトリの構成

```
claude-cxo/
├── README.md                        ← このファイル（使い方）
└── claude/                          ← 配布する中身（これを .claude/ にコピーする）
    ├── agents/                      ← 6体の出席者 Subagent
    │   ├── market-researcher.md         市場調査（TAM/SAM/SOM・CAGR・PEST）        … 部門長
    │   ├── competitor-analyst.md        競合分析（マトリクス・ポジショニング・5フォース） … 部門長
    │   ├── tech-feasibility-assessor.md 技術実現性・ランニングコスト・AI-DLC引き継ぎ  … CTO役
    │   ├── persona-designer.md          顧客・ペルソナ・JTBD                        … 部門長
    │   ├── monetization-strategist.md   マネタイズ・LTV/CAC・GTM                    … 部門長
    │   └── critical-reviewer.md         批判的レビュー（悪魔の代弁者・プレモータム）   … 社外取締役
    └── skills/
        ├── new-app-strategy/        ← 司令塔・subagent版（エントリー /new-app-strategy）【既定】
        │   ├── SKILL.md
        │   └── templates/
        │       ├── business-plan.md     事業計画書テンプレ
        │       └── lean-canvas.md       Lean Canvas テンプレ
        └── new-app-boardroom/       ← 司令塔・全員会議版（エントリー /new-app-boardroom）
            ├── SKILL.md                 Agent Teams で役員同士が直接議論
            └── templates/               （業計画書／Lean Canvas テンプレ・上と同内容）
                ├── business-plan.md
                └── lean-canvas.md
```

> **設計思想**: `claude/` は Git 管理する「配布物の正本」、各プロジェクトの `.claude/` は「実行時設定」。
> 役割を分けることで、組織メンバーが安心して導入・更新できます。

---

## 導入方法（各自で .claude/ にコピー）

このリポジトリを `git clone`（またはダウンロード）したうえで、`claude/` の中身を
**使いたいプロジェクトの `.claude/` フォルダにコピー**してください。

```bash
# 1) 配置先（対象プロジェクト）に .claude/ を用意
mkdir -p /path/to/your-project/.claude

# 2) agents と skills をコピー
cp -R claude/agents  /path/to/your-project/.claude/
cp -R claude/skills  /path/to/your-project/.claude/
```

> このリポジトリ自身で試す場合は、コピー先を `.` にすればOKです。
> `cp -R claude/agents claude/skills ./.claude/`（事前に `mkdir -p .claude`）

コピー後、対象プロジェクトで **Claude Code を（再）起動**すると、Subagent と
`/new-app-strategy` コマンドが読み込まれます（Subagent と Skill はセッション開始時に読み込まれるため、
起動中にコピーした場合は再起動が必要です）。

---

## 2つのモード（使い分け）

同じ6人の出席者を使って、会議の「進め方」が異なる2つのモードがあります。

| | `/new-app-strategy`（subagent版）【既定】 | `/new-app-boardroom`（全員会議版） |
|---|---|---|
| 進め方 | 議長が各役員を**順に呼び出し**、成果物を統合（放射状・逐次） | 6役員が **Agent Teams で同席し、互いに直接議論**（生の会議） |
| オーナー参加 | 批判レビュー後の質疑フェーズで参加 | 会議中いつでも **Shift+Down で任意の役員へ直接割り込み**可能 |
| 強み | 安価・堅牢・量産向き | 臨場感・役員同士の論戦・オーナーの直接参加 |
| コスト | 低い | **高い**（役員ごとに独立インスタンス＝トークン大幅増） |
| 向く場面 | 数多くのアイデアを素早く回す | 重要案件をじっくり、会議体験ごと検証する |

> 迷ったら **`/new-app-strategy`（既定）** を。会議の臨場感や役員同士の議論を見たい重要案件のときに `/new-app-boardroom` を使ってください。

### 全員会議版（`/new-app-boardroom`）の有効化

全員会議版は Claude Code の **Agent Teams（実験的機能）** を使うため、事前設定が必要です。

- **Claude Code v2.1.32 以降**（`claude --version` で確認）
- `settings.json` の `env`、または環境変数で有効化：

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

設定後に Claude Code を再起動してください。未設定のまま `/new-app-boardroom` を呼ぶと、`/new-app-strategy` を使うよう案内されます。

> ⚠️ Agent Teams は実験的機能です（セッション復元不可・タスク状態のラグ・1チーム制などの制約あり）。
> また役員6名ぶんのトークンを消費するため、コストが気になる場合は subagent 版を使ってください。

---

## 使い方

導入後、Claude Code で司令塔スキルを呼び出します。

```
/new-app-strategy フリーランス向けの請求書管理アプリ
```

経営会議が以下の流れで進みます。

1. **インテイク** — ターゲットと中心課題を最大2問だけ確認
2. **市場 × 競合 × 技術**（部門長＋CTO役が並列調査）
3. **ペルソナ**（市場・競合を踏まえて設計）
4. **マネタイズ**（全調査＋ランニングコストを踏まえて収益設計）
5. **統合ドラフト** — 議長が事業計画書ドラフトを作成
6. **批判レビュー** — 社外取締役（批判役）が致命的懸念・プレモータム・撤退条件を指摘
7. **オーナー質疑** — あなたが疑問・異議・訂正をぶつける（論点は議長が各専門家に差し戻し）
8. **最終決裁** — 議長が反論・修正し、事業計画書と Lean Canvas を確定
9. **報告** — エグゼクティブサマリー、GO/PIVOT/NO-GO 判定、AI-DLC への引き継ぎ事項

> **再審議もできます**: 後日 `/new-app-strategy cxo-research/<既存フォルダ>` で再招集。新しい疑問・訂正・
> 検証データを受けて影響するレポートだけ更新し、再決裁の結果を `00-business-plan.md` の改訂履歴に残します。

**全員会議版を使う場合**は、有効化（上記）のうえで同じ要領で呼び出します。生成物・命名規則・再審議のやり方は subagent 版と共通です。

```
/new-app-boardroom フリーランス向けの請求書管理アプリ
```

> 6人の役員が同席して互いに議論し、あなたは会議中いつでも Shift+Down で任意の役員へ直接質問できます。
> 議長は議論には加わらず、議事録の清書と「数字を一次データまで遡る監査」に徹して結論をまとめます。

---

## 生成物の場所

実行すると、利用プロジェクト直下に次のフォルダが作られます。

```
cxo-research/<アプリ名>-<YYYY-MM-DD>/
├── 00-business-plan.md       ← 統合: 詳細な事業計画書（想定反論への対応・改訂履歴つき）
├── 00-lean-canvas.md         ← 統合: 1枚要約
├── 01-market.md              ← 市場調査レポート
├── 02-competitors.md         ← 競合分析レポート
├── 03-tech-feasibility.md    ← 技術実現性・ランニングコスト・AI-DLC引き継ぎ
├── 04-persona.md             ← 顧客・ペルソナレポート
├── 05-monetization.md        ← マネタイズ・事業計画レポート
└── 06-critical-review.md     ← 批判レビュー（致命的懸念・プレモータム・撤退条件）
```

### 結論はどこを見る？

迷ったら **`00-` から始まるファイル** を開いてください。`00-` が「結論・統合」、
`01〜06` は「その結論を裏付ける根拠の元データ」です。

| 知りたいこと | 見るファイル |
|---|---|
| **最終結論（GO/PIVOT/NO-GO）と次の一手** | `00-business-plan.md`（冒頭の投資判断＋第1章サマリー＋第8章 推奨アクション） |
| **1枚でサッと全体像** | `00-lean-canvas.md`（最下部に判断と一言根拠） |
| **結論が覆った一番おもしろい議論** | `06-critical-review.md`（批判役の致命的懸念・プレモータム・撤退条件） |
| **開発（AI-DLC）に引き継ぐ技術論点** | `03-tech-feasibility.md`（⑤ Inception への引き継ぎ事項） |
| 各論の詳しい根拠・出典 | `01-market.md`〜`05-monetization.md` |

> 命名規則：`00-` = 結論／`01→06` = 市場→競合→技術→顧客→マネタイズ→批判 の順に深掘り。
> **まず `00-business-plan.md` の冒頭だけ読めば結論はつかめます。**

### AI-DLC Inception を始めるとき

経営会議の成果物をそのまま開発フェーズ（AI-DLC）に引き継ぐ場合、渡すファイルは目的で絞ってください。

| 優先度 | ファイル | 理由 |
|---|---|---|
| **必須** | `00-business-plan.md` | 何を・誰のために・なぜ作るか。キル基準・PIVOT 条件・スコープの憲法 |
| **必須** | `03-tech-feasibility.md` | セクション⑤「AI-DLC Inception への引き継ぎ事項」がそのまま Inception の最初のアジェンダ |
| 任意 | `04-persona.md` | UX 設計や機能仕様の優先度を議論するとき |
| 任意 | `06-critical-review.md`（⑥キル基準のみ） | スコープが広がりそうなとき、撤退条件に立ち返る用 |

> `01-market.md`・`02-competitors.md`・`05-monetization.md` は事業判断の素材です。
> AI-DLC は「作るか作らないか」を判断しないため、渡すと混乱のもとになります。

---

## データの扱い

- 調査は**ハイブリッド方式**です。まず Claude の知識で仮説を立て、市場規模・競合価格などの
  **重要な数値や固有名は Web 検索で裏取り**し、出典 URL を明記します。
- 推定値には「（推定）」、仮説には「（仮説）」と前置きし、ロジックを添えます。鵜呑みにせず、
  重要判断の前には一次情報での再確認を推奨します。
- 批判役（`critical-reviewer`）は、これらの前提を疑い、楽観的な数字や論理の飛躍を洗い出す役割です。
