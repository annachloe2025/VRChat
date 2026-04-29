# CLAUDE.md — VRChat Fantasy World プロジェクト

このファイルは Claude の作業メモリです。セッションをまたいで参照される前提で、プロジェクトの**現在の状態と方針**を記述します。

## プロジェクト概要

自作ファンタジー小説の世界観をビジュアル化することを目的に、VRChatワールドとして再構築するプロジェクト。
小説の設定資料を docs に整理しつつ、Tiled でレイアウト設計、Unity で実装、mkdocs で可視化と進捗管理を一体運用する。

**現在のフェーズ:** 辺境の村 v0.1 → v0.2 移行中。Tiled→Unity ブロックアウトに加え、Terrain 水路カービング・水面シェーダー(Stylized Water v1)・壁/家/畑/門 への Triplanar マテリアル適用まで完了(2026-04-29 時点)
**主担当:** hoehoe
**性格:** 個人実験プロジェクト、無理をしない、楽しさ優先

## 第1ワールド: 辺境の村

- ルクスマーレ王国の標準的な辺境入植村として汎用設計
- 第三次スタンピードの予兆が迫る現在が舞台
- **村の外周**: 79×55m、防壁2m厚で囲む(内側広場 75×51m)
- 構成: 防壁×4、民家×4(10×10m)、畑×2(16×20m)、水路系×6、溜池×1
- 水循環: 川→取水路→術式石①→集落→下水池→術式石②→川下に戻す
- 黄昏〜薄暮の時間帯、術式石と松明の光
- 詳細設計書: `docs/world-spec/frontier-village.md`
- 配置図(SVG): `docs/assets/diagrams/frontier-village-layout.svg`
- **正式なレイアウトデータ**: `unity/VRC-FantasyWorld-Unity/Assets/Maps/frontier-village.tmj`(Tiled)
- 制作フェーズ: v0.1(ブロックアウト) → v0.2(環境) → v0.3(建造物) → v0.5(仕上げ) → v1.0(お披露目)

## レイアウト → Unity のパイプライン(重要)

```
[Tiled でレイアウト編集 (.tmj)]
   ↓ メニュー: VRChat Village → Import from Tiled
[VillageImporter.cs が JSON を読んで Cube/Plane を自動配置]
   ↓ メニュー: VRChat Village → Carve Water Channels
[TerrainCarver.cs が water レイヤーから Terrain を 0.5m 掘る]
   ↓ メニュー: VRChat Village → Generate Materials
[MaterialGenerator.cs が Mochie/Standard Triplanar マテリアルを生成]
   ↓
[Unity シーンに Village GameObject ツリー + 水路の窪み + テクスチャ済 Cube が完成]
```

- Tiled ファイル位置: `unity/VRC-FantasyWorld-Unity/Assets/Maps/frontier-village.tmj`
- 1タイル = 1m = 32px
- Tiled Y(下向き) → Unity Z(奥向き)に **反転** して変換
- インポータ: `unity/VRC-FantasyWorld-Unity/Assets/Editor/VillageImporter.cs`
- Terrain カーバー: `unity/VRC-FantasyWorld-Unity/Assets/Editor/TerrainCarver.cs`
- マテリアル生成: `unity/VRC-FantasyWorld-Unity/Assets/Editor/MaterialGenerator.cs`
- レイヤー(walls/water/houses/crop_fields/gates_paths)ごとに `LayerMaterialPaths` でマテリアル割り当て
- 高さは custom property `height_m` から取得、無ければレイヤーごとのデフォルト
- `gates_paths` の門は **上部 lintel(まぐさ)だけ生成** して下を通り抜けられるよう調整
- water レイヤーは Cube ではなく **Plane**(Stylized Water v1)を生成、Collider は除去
- `frontier-village.tmj.before-gates.backup` を Tiled の安全網として保持

## Novel プロジェクトとの関係

- Novel フォルダ(C:\Users\hoeho\Documents\Claude\Novel)が **公式設定の真実の源**
- VR プロジェクトには Novel から **VR 化に必要な視覚情報だけ抜粋** して配置
- 抜粋ファイル: `docs/worldbuilding/excerpts/`
- VR は Public、Novel は Private なので、ネタバレや未確定要素は VR 側に持ち込まない
- Novel 側が更新されたら、関連する抜粋を更新する

### Novel フォルダ内で **読まない/避ける** ファイル(重要)

以下は VR 化に不要、かつ Anthropic の自動分類器を刺激する可能性があるため **意図的に読み込まない**:

- `プロット/` 配下全部 ―― 物語のプロット設計書、シーン構造
- `執筆ガイド/プロットのブループリント/` 配下 ―― キャラ別の動機・行動原案
- `原稿/` 配下 ―― 執筆済みの本文(暴力描写・性的描写を含みうる)
- `_archive/` 配下 ―― 没案、考慮対象外
- `12_国家/東の国/王宮の構造（暫定）.md` ―― **S7(暗殺未遂)プロットの詳細設計を含むため、必要なら視覚要素(部屋配置、装飾、規模)だけ別途要約してもらう**
- `13_キャラクター/主人公.md` 等のキャラ詳細 ―― 物語の核心ネタバレを含む
- `LOG.md` ―― 大量の作業ログ、トークン無駄、参照不要

### Novel フォルダで **読んで良い** ファイル(VR 化に直結)

- `PROJECT.md`, `CLAUDE.md`, `README.md` ―― オリエンテーション
- `設定資料/00_概要/` ―― 世界の全体像
- `設定資料/05_地理/` ―― 大陸、地形
- `設定資料/10_生活文化/` ―― 建築、衣食住、交通、照明 (VR の見た目に直結)
- `設定資料/12_国家/3国設定.md` および `12_国家/東の国/` の **王宮以外** のファイル(村落開拓、貴族制度、命名規則 等)
- `設定資料/06_禁域と魔泉/` ―― 自然系のロケーション設定(暴力描写は薄い)
- `設定資料/01_魔力システム/`, `02_魔石と術式/`, `04_生活魔法/` ―― 魔法演出に必要なときだけ
- `設定資料/09_種族・社会/` ―― 種族・社会階層

## 技術スタック

- **Unity 2022.3.22f1** (VCC 指定バージョン)+ VRChat SDK3 (Worlds)
    - 別途 Unity 2022.3.62f3 が VRBoxing プロジェクト用に併存
- **Tiled Map Editor** (レイアウト設計、.tmj 形式)
- **Blender** (3Dモデリング、blendソースは Git 外で管理、フェーズ3以降で使用予定)
- **mkdocs + Material for MkDocs** (世界観・進捗の可視化、GitHub Pages で公開済み)
- **Git + GitHub** (docs と設定のみ追跡、Unity/Blender 関連は完全 ignore、LFS不使用)
- **cairosvg** (アイコン・配置図のSVG→PNG変換、ローカル開発時のみ)

### Unity 内アセット / シェーダー

- **Stylized Trees** — 樹木 Paint Trees 用
- **AllSky Free** — Skybox 候補ライブラリ(黄昏系を選定中)
- **Stylized Water v1** — 水面シェーダー(v2 は Built-in RP 非対応のため不採用)
- **Free Realistic Textures** (Game Buffs) — 壁/家/畑/門のテクスチャ素材
    - 採用: `Cracked_Soil_16`(壁・家)、`Forest_Ground_12`(畑)、`Wood_Planks_40`(門)
- **Mochie's Shaders** — `Mochie/Standard` を採用。`_PrimarySampleMode = 3` (Triplanar) + `_TriplanarCoordSpace = 1` (World) で Cube どの面でも均等に貼れる。**標準シェーダー**として運用
- **Poiyomi Toon World** — インストール済だが、UV モードに Triplanar が無いため辺境の村では未採用。アバター・装飾用に温存

## リポジトリ運用方針

**追跡するもの:**
- CLAUDE.md, TASKS.md, README.md
- docs/ 配下(mkdocs ソース、アイコン素材、世界観抜粋、ワールド設計書、配置図SVG含む)
- mkdocs.yml
- overrides/main.html (Material テーマの extrahead ブロック上書き)
- update.bat(更新・公開を一括実行するバッチ)
- .gitignore, .gitattributes

**追跡しないもの(完全 ignore):**
- unity/ — Unityプロジェクト一式(C#スクリプト、Tiled .tmj 含めて全部)
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
- ワールド管理: VCC(VRChat Creator Companion)経由で初期作成、以降は Unity Hub から開いてOK
- アップロード方針: 開発中は Private、お披露目できる完成度になったら Community Labs → Public
- Quest 対応: 当面 PC のみ、将来検討

## 命名・記述規則

- ドキュメントは原則**日本語**で記述
- コード・設定ファイル・ファイル名は英語(小文字+ハイフン区切り)
- Tiled のオブジェクト名は日本語(壁、家、畑など)、class は英語(wall, house, crop_field)
- 進捗ログのファイル名は `YYYY-MM-DD-短い見出し.md` 形式
- マイルストーンタグは `v0.1`, `v0.2` のようにセマンティック寄りで

## Claude への運用指示

- 新セッション開始時は **このファイルと TASKS.md を必ず読む**
- 世界観に関する質問に答える前に、まず `docs/worldbuilding/excerpts/` を確認、必要なら Novel フォルダの該当ファイルを読む(ただし上の「読まないファイル」リストに従う)
- 新しい決定事項が出たら、このファイルの「決定事項ログ」セクションに追記する
- ドキュメント生成時は `docs/` 配下に配置、テンプレートに沿った見出し構造を維持
- **Git操作はサンドボックス側で実行しないこと**(Cowork のWindowsマウント権限制約で中途半端な .git が残る)。Git操作はユーザーに PowerShell コマンドを提示するか、`update.bat` を実行してもらう
- **日本語を含む既存ファイルの編集は Edit ツールで途中切れすることがある**。Write での全置換、または bash heredoc で書き直す方が確実
- **AI画像生成サービスからのロゴ提案は商標違反に注意**(ChatGPT が VRChat 公式ロゴそのものを出してきた前例あり)
- **Novel フォルダは Private、VRChat は Public**。VR 側にネタバレを持ち込まない判断を常に取る
- **暴力的な描写・暗殺プロット等を含む Novel ファイルを読み込むと Anthropic の自動分類器が反応する場合がある**。視覚要素や設定の概要が必要な場合は、ユーザーに該当部分を要約してもらうか、安全な部分(設定資料/05〜12のうち王宮詳細を除く)から間接的に把握する
- **座標変換のお約束**: Tiled 座標(Y下向き)→ Unity 座標(Z奥向き)で **Y軸を反転** する(`unity_z = mapHeightM - tiled_y - h/2`)。これを忘れると北南が逆になる

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
| 2026-04-27 | 水回りの仕組みを村に組み込み(高低差ある立体的な村に)、SVG 配置図を整備 |
| 2026-04-27 | Novel フォルダの「読まないファイル」リストを CLAUDE.md に明記、暗殺プロット系は VR 制作には不要 |
| 2026-04-28 | レイアウト設計ツールに **Tiled Map Editor** を採用(.tmj、データ駆動) |
| 2026-04-28 | Tiled ファイルは `unity/VRC-FantasyWorld-Unity/Assets/Maps/frontier-village.tmj` に配置(git管理外) |
| 2026-04-28 | C# インポータ `VillageImporter.cs` を実装、Tiled → Unity の自動ブロックアウトが稼働 |
| 2026-04-28 | 座標変換: 1タイル=1m=32px、Tiled Y下 → Unity Z奥(反転)で確定 |
| 2026-04-29 | Tiled に `gates_paths` レイヤー追加、門は **上部 lintel(まぐさ)だけ生成** して通り抜け可に |
| 2026-04-29 | `TerrainCarver.cs` で water レイヤーを Terrain に **0.5m 掘り込む**運用を確立 |
| 2026-04-29 | 水面シェーダーは **Stylized Water v1** を採用(v2 は Built-in RP 非対応)。Rim Size を 4.6→0.3 等で黄昏向けに調整 |
| 2026-04-29 | 壁/家/畑/門のマテリアルは **Mochie/Standard の Triplanar (World)** で運用。`MaterialGenerator.cs` で自動生成 |
| 2026-04-29 | Cube UV 引き伸ばし問題は Triplanar で解決。Poiyomi Toon World は Triplanar 非対応のため辺境の村では未採用 |
| 2026-04-29 | TileSize 既定: 壁/家=0.8m, 畑=1.2m, 門=0.6m per repeat |

## 未確定事項

- プロジェクトの正式名称(現状「VRChat Fantasy World」は仮称)
- 辺境の村の名前(現状仮称「辺境の村」)
- ロゴの最終デザイン(現状はプレースホルダー、世界観固まり次第差し替え)
- Quest 対応の有無
- 第2ワールド以降の候補
