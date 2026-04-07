# /catchup - 概念キャッチアップ Skill for Claude Code

新しいテーマについて `/catchup トピック名` と打つだけで、全体像・参考資料・事例・学びを構造化したレポートを自動生成する [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 用の Skill です。

## 何ができるか

- 3つの探索エージェントが**並列で**資料を収集（直接検索・権威経由・学術探索）
- 海外/日本の資料を**同時に**調査し、ランキング化
- 代表的な企業事例を背景・文脈まで深堀り
- 5つのファイルに分けて構造化レポートを出力

### 出力ファイル

| ファイル | 内容 |
|---|---|
| `[トピック]_[日付].md` | ブリーフィング（5-10分で読める概要。単体で他者にシェア可能） |
| `01_structure.md` | テーマの構造と全体像（論点・歴史・トレンド・場面別考慮事項・用語集） |
| `02_references.md` | 参考資料ランキング + 各資料のストーリーライン・章サマリー |
| `03_cases.md` | 代表的な企業事例（意思決定の背景・文脈・結果・教訓） |
| `04_insights.md` | 学びと示唆（誤解と実態・成功/失敗パターン・推奨アクション） |

## セットアップ

### 必要なもの

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) がインストール済みであること

### インストール

1. このリポジトリをクローンするか、zipをダウンロードして解凍

```bash
git clone https://github.com/shuuemura/catchup-skill.git
cd catchup-skill
```

2. Claude Code を起動

```bash
claude
```

これだけで `/catchup` コマンドが使えるようになります。

### 既存プロジェクトに追加する場合

`.claude/skills/catchup/` フォルダを、既存プロジェクトの `.claude/skills/` 配下にコピーしてください。

```bash
cp -r .claude/skills/catchup /path/to/your-project/.claude/skills/
```

## 使い方

### 基本

```
/catchup プライシング
```

トピックだけ入力すると、文脈の確認 → 実行モードの選択 → 調査開始の流れで進みます。

### 背景・課題感を付けて実行

```
/catchup SaaSのプライシング。SaaSとBPOを併用しており、利益率やプラン設計が関心事
```

背景を付けると、その文脈に合わせて資料選定・事例選定・示唆が最適化されます。

### 実行モード

起動後に選択できます:

| モード | 説明 |
|---|---|
| **お任せ** | スタンダード設定で自動実行。完了まで待つだけ |
| **カスタム** | 調査の深さ（クイック/スタンダード/ディープ）と資料リストの事前確認を選択可能 |

### 調査の深さ（カスタムモード）

| 深さ | 目安時間 | 資料数 | 事例数 |
|---|---|---|---|
| クイック | 15分 | 5件 | 3件 |
| スタンダード | 30分 | 20件（日本10/海外10） | 5件 |
| ディープ | 60分 | 30件以上 | 8件 |

## 出力例

実行すると `output/catchup-[トピック]-[日付]/` ディレクトリに5ファイルが生成されます。

```
output/catchup-プライシング-2026-03-30/
├── プライシング_20260330.md
├── 01_structure.md
├── 02_references.md
├── 03_cases.md
└── 04_insights.md
```

## 権限設定について

初回実行時に Web 検索やファイル書き込みの許可を求められることがあります。
同梱の `.claude/settings.local.json` にデフォルトの許可設定が含まれているため、
このリポジトリ内で実行する場合は自動的に許可されます。

既存プロジェクトに追加した場合は、プロジェクトの `.claude/settings.local.json` の
`permissions.allow` に以下を追加してください:

```json
{
  "permissions": {
    "allow": [
      "Bash",
      "WebSearch",
      "WebFetch",
      "Read",
      "Write",
      "Edit"
    ]
  }
}
```

## Skill の構成

```
.claude/skills/catchup/
├── SKILL.md                     # メイン定義（フェーズ制御・トリガー条件）
├── agents/
│   ├── survey-direct.md         # 資料直接探索エージェント
│   ├── survey-authority.md      # 権威経由探索エージェント
│   ├── survey-academic.md       # 学術探索エージェント
│   ├── deep-summary.md          # 資料深堀りエージェント
│   └── research-examples.md     # 事例調査エージェント
└── references/
    ├── output-format.md         # 出力テンプレート
    ├── evaluation-criteria.md   # 資料・人物の評価基準 + 検索戦略
    └── summary-requirements.md  # サマリーの品質要件
```

## 設計の背景

このSkillは、Fabric (`extract_wisdom`)、Anthropic公式Skills (`doc-coauthoring`, `skill-creator`)、
Understand-Anything のマルチエージェント設計などをベンチマークした上で、
概念キャッチアップに特化して設計されています。

詳細な設計ドキュメントは `docs/design.md` を参照してください。

## License

MIT
