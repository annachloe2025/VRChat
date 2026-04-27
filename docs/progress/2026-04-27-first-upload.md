# 2026-04-27: GitHub Pages 公開 + 初回ワールドアップロード

## やったこと

### サイト公開系

- mkdocs サイトを GitHub Pages にデプロイ、`https://annachloe2025.github.io/VRChat/` で公開開始
- `update.bat` を作成 ―― ダブルクリックで `pip install --upgrade mkdocs` → `git add/commit/push` → `mkdocs gh-deploy --force` を一括実行
- iPhone Safari でホーム画面追加できるように、`apple-touch-icon` と `<link>` メタタグ群を整備
- `overrides/main.html` を作って Material のテンプレートを部分上書き(extrahead ブロックでアイコン宣言、theme-color、PWA関連メタを注入)

### アイコン・ロゴ系

- ChatGPT 経由で生成された画像が VRChat 公式ロゴそっくりだったため不採用、トレードマーク回避
- オリジナルプレースホルダーロゴを SVG で自作: 紫の星空背景に Georgia セリフ体「VRC」、クリーム〜ゴールドのグラデーション
- cairosvg で各サイズに展開: `favicon-32`, `favicon-96`, `apple-touch-icon-180`, `icon-192`, `icon-512`, `icon-maskable-512`, `logo-256`
- `mkdocs.yml` の `theme.logo` と `theme.favicon` に登録

### VRChat ワールド系

- VCC(VRChat Creator Companion)をインストール、VRChat アカウントでログイン
- VCC で「World」テンプレートから新規プロジェクト作成、`unity/` 配下に配置(.gitignore で除外済み)
- Unity 2022.3.22f1 を Unity Hub から追加インストール(既存の 2022.3.62f3 と併存、VRBoxing用とは別管理)
- Unity でプロジェクトを開き、VRChat SDK Builder パネルから:
    - 「Setup Layers for VRChat」を実行 ―― VRChat 仕様の Unity レイヤー設定に揃える
    - 「Setup Collision Matrix」を実行 ―― 物理演算の衝突レイヤーマトリクスを揃える
- **ワールドを Build & Publish for PC Windows でアップロード成功** ―― 久々の VRChat ワールド開発再開

## 決まったこと

- iPhone からの閲覧パス: ホーム画面に追加 → VRC アイコンタップ → サイト直起動
- VRChat ワールドの初期 Release Status: Private のまま、お披露目できるレベルになるまで Community Labs / Public は控える

## 詰まったポイントとその対処

- **VCC の Unity バージョン要求(2022.3.22f1)が手元の 2022.3.62f3 と不一致** → Unity Archive 経由で 22f1 を追加インストール
- **VRChat SDK で「Build & Test」ボタンが出ず、赤エラー** → Layers と Collision Matrix の初期セットアップを忘れていた。それぞれボタン1発で解決
- **Edit ツールで日本語ファイルが途中切れする問題** → `bash heredoc` での全置換に切り替えで解決(CLAUDE.md にも運用指示として記録済み)

## 次にやること

- 初回アップロード成功した「空に近いワールド」が Mine から訪問できるか実機確認
- 確認できたら、地形(Terrain)や HDRI Skybox の練習
- 並行して、小説の世界観を `docs/worldbuilding/` に書き起こし開始

## 雑感

「サイトを iPhone で見られる状態」「VRChat ワールドが自分のアカウントに上がる状態」の両方が今日整った。ここまで来ると「あとはコンテンツ作るだけ」という段階で、心理的なハードルが大きく下がる。
ChatGPT が VRChat の公式ロゴをそのまま吐き出してきたのは予想外だったが、未然に避けられたのは収穫。今後の AI 画像生成では「特定ブランドの匂いがしないか」を一度疑う癖をつける。
