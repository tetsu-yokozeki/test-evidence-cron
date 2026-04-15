# test-evidence-cron

高山DMO 日次AIレポートの自動生成パイプライン（試作）。

## 構成

```
test-evidence-cron/
├── prompts/
│   └── analysis.md     ← AI分析の観点・出力フォーマット（ここを編集すれば翌日から反映）
├── data/
│   └── kpi-latest.json ← 前日KPI（将来的にGH Actionsが毎朝06:00 JSTに上書き）
└── reports/
    ├── YYYY-MM-DD.md   ← 日次レポート（Remote Triggerが毎朝07:00 JSTに生成）
    └── latest.md       ← 最新版（常に最新レポートで上書き）
```

## 動作フロー

```
06:00 JST  GitHub Actions
           ├─ BQからKPI取得
           ├─ data/kpi-latest.json 更新
           ├─ Evidence build → Cloudflare Pages デプロイ
           └─ push

07:00 JST  Claude Code Remote Trigger (Opus)
           ├─ prompts/analysis.md を読む
           ├─ data/kpi-latest.json を読む
           ├─ AI分析生成
           ├─ reports/ に書き出し → push
           └─ #times_yokozeki に Slack通知
```

## プロンプト編集

分析の観点を変えたい時は `prompts/analysis.md` を編集してpushするだけ。
Remote Triggerは次回実行時に最新のプロンプトを読む。

## Trigger管理

https://claude.ai/code/scheduled から有効/無効・実行ログを確認可能。
