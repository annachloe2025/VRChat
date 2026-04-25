# CLAUDE.md — VRChat Fantasy World プロジェクト

このファイルは Claude の作業メモリです。セッションをまたいで参照される前提で、プロジェクトの**現在の状態と方針**を記述します。

## プロジェクト概要

自作ファンタジー小説の世界観をビジュアル化することを目的に、VRChatワールドとして再構築するプロジェクト。
小説の設定資料を docs に整理しつつ、Unity で実装、mkdocs で可視化と進捗管理を一体運用する。

**現在のフェーズ:** 構想・準備段階(2026-04-26 開始)
**主担当:** hoehoe
**性格:** 個人実験プロジェクト、無理をしない、楽しさ優先

## 技術スタック

- **Unity 2022.3 LTS** + VRChat SDK3 (Worlds)
- **Blender** (3Dモデリング、blendソースは Git 外で管理)
- **mkdocs + Material for MkDocs** (世界観・進捗の可視化)
- **Git** (docs と設定のみ追跡、Unity/Blender 関連は完全 ignore)
- **GitHub** (将来的にリモート、LFSは使わない)

## リポジトリ運用方針

**追跡するもの:**
- CLAUDE.md, TASKS.md, README.md
- docs/ 配下(mkdocs ソース)
- mkdocs.yml
- .gitignore, .gitattributes

**追跡しないもの(完全 ignore):**
- unity/ — Unityプロジェクト一式(C#スクリプトも含めて全部)
- blender/ — .blend ファイル、Blender作業ファイル
- refs/ — 参考画像・ピンタレスト保存物・ムードボード素材

**バックアップ戦略:**
- Git追跡対象 → GitHub (将来)
- unity/, blender/, refs/ → OneDrive 等のクラウド同期 or 外付けHDD で別途バックアップ

**LFSは使わない:** 重いバイナリは全部 ignore するため不要。将来必要になったら再検討。

## ディレクトリ構成

```
VRChat/
├── CLAUDE.md             # このファイル(Claude作業メモリ)
├── TASKS.md              # タスク管理
├── README.md             # プロジェクト概要
├── mkdocs.yml            # mkdocs 設定
├── docs/                 # mkdocs ソース
│   ├── index.md
│   ├── worldbuilding/    # 世界観設定(地理・歴史・文化)
│   ├── visual-design/    # ヴィジュアル方針・ムードボード
│   ├── world-spec/       # ワールドの仕様(規模・ギミック・動線)
│   ├── progress/         # 制作日誌
│   └── reference/        # 技術メモ・アセット一覧
├── unity/                # Unityプロジェクト(.gitignore)
├── blender/              # Blender作業(.gitignore)
├── refs/                 # 参考素材(.gitignore)
└── .gitignore
```

## 命名・記述規則

- ドキュメントは原則**日本語**で記述
- コード・設定ファイル・ファイル名は英語(小文字+ハイフン区切り)
- 進捗ログのファイル名は `YYYY-MM-DD-短い見出し.md` 形式
- マイルストーンタグは `v0.1`, `v0.2` のようにセマンティック寄りで

## Claude への運用指示

- 新セッション開始時は **このファイルと TASKS.md を必ず読む**
- 世界観に関する質問に答える前に `docs/worldbuilding/` を確認する
- 新しい決定事項が出たら、このファイルの「決定事項ログ」セクションに追記する
- ドキュメント生成時は `docs/` 配下に配置、テンプレートに沿った見出し構造を維持
- **Git操作はサンドボックス側で実行しないこと**(Cowork のWindowsマウント権限制約で中途半端な .git が残る)。Git操作はユーザーに PowerShell コマンドを提示して実行してもらう

## 決定事項ログ

| 日付 | 決定内容 |
|------|---------|
| 2026-04-26 | プロジェクト立ち上げ。Pattern 1(Unity/Blender/refs完全ignore)採用 |
| 2026-04-26 | LFS不採用。重いバイナリは OneDrive 等で別管理 |
| 2026-04-26 | mkdocs + Material for MkDocs を進捗・世界観可視化のハブに |

## 未確定事項

- プロジェクトの正式名称(現状「VRChat Fantasy World」は仮称)
- 小説の Novel フォルダとの連携方法(コピー / 抽出 / 参照)
- GitHub リモート化のタイミング
- Quest 対応の有無
