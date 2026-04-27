# CLAUDE.md — VRChat Fantasy World プロジェクト

このファイルは Claude の作業メモリです。セッションをまたいで参照される前提で、プロジェクトの**現在の状態と方針**を記述します。

## プロジェクト概要

自作ファンタジー小説の世界観をビジュアル化することを目的に、VRChatワールドとして再構築するプロジェクト。
小説の設定資料を docs に整理しつつ、Unity で実装、mkdocs で可視化と進捗管理を一体運用する。

**現在のフェーズ:** 第1ワールド「辺境の村」の設計完了、Unity でブロックアウト着手前(2026-04-27 時点)
**主担当:** hoehoe
**性格:** 個人実験プロジェクト、無理をしない、楽しさ優先

## 第1ワールド: 辺境の村

- ルクスマーレ王国の標準的な辺境入植村として汎用設計
- 第三次スタンピードの予兆が迫る現在が舞台
- 約60m×50mの防壁内、防壁・門・見張り台・集会所・民家4〜5棟・井戸・畑・家畜囲い
- 黄昏〜薄暮の時間帯、術式石と松明の光
- 詳細設計書: `docs/world-spec/frontier-village.md`
- 制作フェーズ: v0.1(ブロックアウト) → v0.2(環境) → v0.3(建造物) → v0.5(仕上げ) → v1.0(お披露目)

## Novel プロジェクトとの関係

- Novel フォルダ(C:\Users\hoeho\Documents\Claude\Novel)が **公式設定の真実の源**
- VR プロジェクトには Novel から **VR 化に必要な視覚情報だけ抜粋** して配置
- 抜粋ファイル: `docs/worldbuilding/excerpts/`
- VR は Public、Novel は Private なので、ネタバレや未確定要素は VR 側に持ち込まない
- Novel 側が更新されたら、関連する抜粋を更新する

## 技術スタック

- **Unity 2022.3.22f1** (VCC 指定バージョン)+ VRChat SDK3 (Worlds)
    - 別途 Unity 2022.3.62f3 が VRBoxing プロジェクト用に併存
- **Blender** (3Dモデリング、blendソースは Git 外で管理)
- **mkdocs + Material for MkDocs** (世界観・進捗の可視化、GitHub Pages で公開済み)
- **Git + GitHub** (docs と設定のみ追跡、Unity/Blender 関連は完全 ignore、LFS不使用)
- **cairosvg** (アイコン生成のSVG→PNG変換、ローカル開発時のみ)

## リポジトリ運用方針

**追跡するもの:**
- CLAUDE.md, TASKS.md, README.md
- docs/ 配下(mkdocs ソース、アイコン素材、世界観抜粋、ワールド設計書含む)
- mkdocs.yml
- overrides/main.html (Material テーマの extrahead ブロック上書き)
- update.bat(更新・公開を一括実行するバッチ)
- .gitignore, .gitattributes

**追跡しないもの(完全 ignore):**
- unity/ — Unityプロジェクト一式(C#スクリプトも含めて全部)
- blender/ — .blend ファイル、Blender作業ファイル
- refs/ — 参考画像・ピンタレスト保存物・ムードボード素材

**バックアップ戦略:**
- Git追跡対象 → GitHub (annachloe2025/VRChat)
- unity/, blender/, refs/ → OneDrive 等のクラウド同期 or 外付けHDD で別途バックアップ

**LFSは使わない:** 重いバイナリは全部 ignore するため不要。将来必要になったら再検討。

## 公開サイト運用

- リポジトリ: <https://github.com/annachloe2025/VRChat> (public)
- 公開サイト: <https://annachloe2025.github.io/VRChat/>
- 更新方法: `update.bat` をダブルクリック → commit + push + `mkdocs gh-deploy --force` を一括実行
- iPhoneからは公開サイトURLをブックマーク or ホーム画面に追加して閲覧(VRC アイコン)

## VRChat ワールド運用

- VRChat アカウント: hoehoe(2021年以前から使用、トラスト問題なし)
- ワールド管理: VCC(VRChat Creator Companion)経由
- アップロード方針: 開発中は Private、お披露目できる完成度になったら Community Labs → Public
- Quest 対応: 当面 PC のみ、将来検討

## 命名・記述規則

- ドキュメントは原則**日本語**で記述
- コード・設定ファイル・ファイル名は英語(小文字+ハイフン区切り)
- 進捗ログのファイル名は `YYYY-MM-DD-短い見出し.md` 形式
- マイルストーンタグは `v0.1`, `v0.2` のようにセマンティック寄りで

## Claude への運用指示

- 新セッション開始時は **このファイルと TASKS.md を必ず読む**
- 世界観に関する質問に答える前に、まず `docs/worldbuilding/excerpts/` を確認、必要なら Novel フォルダの該当ファイルを読む
- 新しい決定事項が出たら、このファイルの「決定事項ログ」セクションに追記する
- ドキュメント生成時は `docs/` 配下に配置、テンプレートに沿った見出し構造を維持
- **Git操作はサンドボックス側で実行しないこと**(Cowork のWindowsマウント権限制約で中途半端な .git が残る)。Git操作はユーザーに PowerShell コマンドを提示するか、`update.bat` を実行してもらう
- **日本語を含む既存ファイルの編集は Edit ツールで途中切れすることがある**。Write での全置換、または bash heredoc で書き直す方が確実
- **AI画像生成サービスからのロゴ提案は商標違反に注意**(ChatGPT が VRChat 公式ロゴそのものを出してきた前例あり)
- **Novel フォルダは Private、VRChat は Public**。VR 側にネタバレを持ち込まない判断を常に取る

## 決定事項ログ

| 日付 | 決定内容 |
|------|---------|
| 2026-04-26 | プロジェクト立ち上げ。Pattern 1(Unity/Blender/refs完全ignore)採用 |
| 2026-04-26 | LFS不採用。重いバイナリは OneDrive 等で別管理 |
| 2026-04-26 | mkdocs + Material for MkDocs を進捗・世界観可視化のハブに |
| 2026-04-26 | VRChat 制作環境(VCC前提のモダン手順)を `docs/reference/vrchat-setup.md` に整備 |
| 2026-04-26 | 用語集 `docs/reference/glossary.md` を運用開始(LFS, VCC, Udon, ClientSim 等) |
| 2026-04-26 | GitHub リポジトリ annachloe2025/VRChat (public) と連携、初回push成功 |
| 2026-04-26 | update.bat で commit + push + gh-deploy を一括実行する運用に |
| 2026-04-27 | GitHub Pages でサイト公開開始、iPhone Safari → ホーム画面追加に対応 |
| 2026-04-27 | プレースホルダーロゴ(紫×ゴールド「VRC」)を SVG で自作、PNG各サイズに展開 |
| 2026-04-27 | overrides/main.html で apple-touch-icon, theme-color, PWA関連メタを注入 |
| 2026-04-27 | Unity 2022.3.22f1 を VCC 用に追加インストール、VRBoxing用 62f3 と併存 |
| 2026-04-27 | 初回ワールドアップロード成功、Private 運用で進める方針 |
| 2026-04-27 | Novel フォルダを source of truth として参照、VR には抜粋のみ配置する方針 |
| 2026-04-27 | 第1ワールドを「辺境の村(ルクスマーレ王国の標準的入植村)」に決定 |

## 未確定事項

- プロジェクトの正式名称(現状「VRChat Fantasy World」は仮称)
- 辺境の村の名前(現状仮称「辺境の村」)
- ロゴの最終デザイン(現状はプレースホルダー、世界観固まり次第差し替え)
- Quest 対応の有無
- 第2ワールド以降の候補
