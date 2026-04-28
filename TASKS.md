# TASKS

VRChat Fantasy World プロジェクトのタスク管理。
完了したものは `[x]` でチェック、`(完了日)` を末尾に付ける。

## Now (今やる) — 辺境の村 v0.2 環境構築中

- [ ] AllSky Free から黄昏系 Skybox を 1-2 個に絞って Import、シーンに適用
- [ ] Terrain に草・土・砂利の塗り分け(中央広場・道・川岸)
- [ ] Tiled に 門 / 術式石①② / スポーン地点 / 見張り台 を追加配置 → 再Import
- [ ] スポーン位置を村の門付近に調整
- [ ] 仮アセット(Stylized Trees の家代わりや、別の建物アセット)で家プレハブを試す
- [ ] Build & Test で歩いてみる、容量再確認

## Next (次にやる) — 辺境の村 v0.2 環境構築

- [ ] HDRI Skybox を黄昏グラデに差し替え
- [ ] Directional Light を残照(月光寄り)で設定
- [ ] Terrain で外側の地形・遠景の樹木を配置
- [ ] Fog を遠景にうっすら、空気感を整える
- [ ] フェーズ2 完了時点で Build & Test

## Later (将来)

- [ ] 辺境の村 v0.3 ─ 建造物のメッシュ置き換え、装飾、ライト
- [ ] 辺境の村 v0.5 ─ 環境音 / BGM / 看板テキスト追加、Build & Publish
- [ ] 辺境の村 v1.0 ─ お披露目できる完成度、Community Labs / Public 検討
- [ ] Quest 対応の検討
- [ ] 第2ワールド候補(王宮、主人公の家、禁域 入口、公爵家、王都ルメリア)

## Done

- [x] プロジェクト方針の決定: docs管理 + Unity/Blender完全ignore (2026-04-26)
- [x] 初期ファイル作成: CLAUDE.md, TASKS.md, mkdocs.yml, docs配下, .gitignore (2026-04-26)
- [x] ローカルGit初期化、初回コミット完了 (2026-04-26)
- [x] mkdocs serve でブラウザプレビュー確認 (2026-04-26)
- [x] VRChat 制作環境メモと用語集を docs/reference/ に追加 (2026-04-26)
- [x] GitHub リポジトリ annachloe2025/VRChat と接続、初回 push 完了 (2026-04-26)
- [x] update.bat 作成(commit + push + gh-deploy をワンクリック実行) (2026-04-26)
- [x] プロジェクトセットアップガイドを docs/reference/project-setup.md にまとめる (2026-04-26)
- [x] GitHub Pages にサイト公開、iPhone から閲覧可能に (2026-04-27)
- [x] オリジナルプレースホルダーロゴ(VRC)を作成、各サイズに展開 (2026-04-27)
- [x] iOS apple-touch-icon と meta タグを overrides/main.html で注入 (2026-04-27)
- [x] VCC をインストール、VRChat アカウントでログイン (2026-04-27)
- [x] VCC で空の Worlds プロジェクトを作成、Unity 2022.3.22f1 で起動 (2026-04-27)
- [x] VRChat SDK の Layers と Collision Matrix を初期セットアップ (2026-04-27)
- [x] 初回ワールドを Build & Publish (Private) でアップロード成功 (2026-04-27)
- [x] Novel フォルダの設定資料を読み、VR化戦略を立案 (2026-04-27)
- [x] 第1ワールドを「辺境の村」に決定、詳細設計書を docs/world-spec/frontier-village.md に作成 (2026-04-27)
- [x] VR用世界観抜粋を docs/worldbuilding/excerpts/world-summary-for-vr.md に作成 (2026-04-27)
- [x] 水回りの仕組みを組み込んだ立体的な村設計に進化 (2026-04-27)
- [x] CLAUDE.md に Novel の「読まないファイル」リストを明記 (2026-04-27)
- [x] Tiled Map Editor をインストール、辺境の村のオブジェクト配置 (2026-04-28)
- [x] Tiled .tmj を整地スクリプトで 1mグリッドスナップ、壁・水路を 2m 統一 (2026-04-28)
- [x] 民家を 10×10m × 4軒、畑を 16×20m × 2枚に統一 (2026-04-28)
- [x] .tmj を Unity Assets/Maps/ に移動 (2026-04-28)
- [x] C# インポータ VillageImporter.cs を作成、メニューから Cube 自動配置 (2026-04-28)
- [x] Y軸反転バグを修正、Tiled の北南が Unity の奥手前と一致 (2026-04-28)
- [x] Terrain を 200×200m で作成、Diffuse + Normal の Layer 設定 (2026-04-28)
- [x] Stylized Trees アセット導入、Paint Trees の操作習熟 (2026-04-28)
- [x] ビルドサイズ確認 (Terrain あり 100MB / なし 70MB、現状 PC OK) (2026-04-28)
- [x] Skybox の選定方針確定(AllSky Free から黄昏系を選別) (2026-04-28)
