# プロジェクトセットアップガイド

このドキュメントは、本プロジェクトをゼロから立ち上げた **流れ・技術選択・日々の運用方法** をまとめたものです。
友人に共有する用、そして未来の自分が「あの設定どうしたっけ?」を解決する用。

!!! info "プロジェクトの位置づけ"
    自作ファンタジー小説の世界観を VRChat ワールドとして可視化するための、個人実験プロジェクト。
    ドキュメントと進捗を mkdocs で公開し、Unity 実装は別途進める。

## 全体像(構成図)

```
[hoehoe のPC]
  ├── Unity 2022.3 LTS + VRChat SDK3 ─→ ワールド本体(Git管理外)
  ├── Blender ─→ 3Dモデリング(Git管理外)
  └── docs/*.md (mkdocs ソース)
        │
        │  update.bat ダブルクリック
        ▼
  [Git commit & push]
        │
        ▼
  [GitHub: annachloe2025/VRChat]
        │
        │  mkdocs gh-deploy --force
        ▼
  [GitHub Pages: gh-pages ブランチ]
        │
        ▼
  https://annachloe2025.github.io/VRChat/  ← iPhoneからもアクセス可能
```

## 技術スタックと選択理由

| 要素 | 採用したもの | 理由 |
|------|------------|------|
| ワールド開発 | Unity 2022.3 LTS + VRChat SDK3 (Worlds) | VRChat 公式推奨。VCC 経由で導入 |
| 3Dモデリング | Blender | フリー、定番 |
| ドキュメント | mkdocs + Material for MkDocs | Markdownで書ける、検索・ナビ・ダークモード標準対応、日本語OK |
| バージョン管理 | Git (ローカル) | 履歴管理は必須 |
| リモート保管 | GitHub (public) | 無料、GitHub Pagesでサイト公開できる |
| サイト公開 | GitHub Pages | リポジトリと同居で楽、独自ドメイン不要 |
| 重量バイナリ | Git管理外 (OneDrive等で別管理) | LFSの帯域・容量制約を回避 |

## リポジトリの運用方針

**追跡するもの**(GitHubに上がる):

- `CLAUDE.md`, `TASKS.md`, `README.md`
- `docs/` 配下(mkdocs ソース、Markdown)
- `mkdocs.yml`(mkdocs 設定)
- `update.bat`(更新・公開を一括実行するバッチ)
- `.gitignore`, `.gitattributes`

**追跡しないもの**(GitHubには上がらない、ローカルのみ):

- `unity/` — Unityプロジェクト一式
- `blender/` — .blend ファイル
- `refs/` — 参考画像・ピンタレスト保存物・ムードボード素材

**重要な前提:** Unity と Blender の作業ファイルは **完全にGit管理外**。
これらは OneDrive 等のクラウド同期、または外付けHDDで別途バックアップする。

## なぜ LFS を使わないか

Git LFS(Large File Storage)は大きなバイナリを Git で扱うための拡張だが、今回は採用しなかった。

- GitHubの無料枠は **アカウント全体で 1GB ストレージ + 月1GB 帯域**(超えると課金)
- 一度プッシュしたLFSオブジェクトは GitHub では自動GCされず、履歴を消しても容量が減らない
- Unity プロジェクトと Blender ソースを **そもそも Git に入れない** ことで、LFS の必要性を消した
- 個人プロジェクトの初期段階では、シンプルさが何より重要

将来、複数PCでの共同作業やCIが必要になったら、その時点で LFS や代替案(Plastic SCM等)を再検討する。

## 初期構築の手順(再現したい人向け)

### 1. プロジェクトフォルダの作成

`C:\Users\hoeho\Documents\Claude\VRChat` を作成。Documents 以下なので OneDrive 同期にも乗りやすい位置。

### 2. ドキュメント雛形の生成

このリポジトリの構造をそのまま参考にする:

```
VRChat/
├── CLAUDE.md             # AI(Claude)用の作業メモリ
├── TASKS.md              # タスク管理
├── README.md             # プロジェクト概要
├── mkdocs.yml            # mkdocs 設定
├── update.bat            # 更新・公開を一括実行
├── .gitignore            # Unity/Blender/refsを完全除外
├── .gitattributes        # 改行コード正規化
└── docs/                 # mkdocs ソース
    ├── index.md
    ├── worldbuilding/    # 世界観(地理・歴史・文化)
    ├── visual-design/    # ヴィジュアル方針
    ├── world-spec/       # ワールドの仕様
    ├── progress/         # 制作日誌
    └── reference/        # 技術メモ・用語集・このガイド
```

### 3. Git の初期化(Windows PowerShell で)

```powershell
cd C:\Users\hoeho\Documents\Claude\VRChat
git init -b main
git add -A
git commit -m "Initial commit: project structure and docs scaffold"
```

### 4. GitHub リポジトリの作成

GitHubにログイン → 右上「+」→「New repository」:

- 名前: `VRChat`(任意)
- Public(GitHub Pages を無料で使うため、または Private + 別サービスで非公開化)
- README、.gitignore、ライセンスは追加しない(ローカルに既にあるので)

### 5. ローカルとGitHubを接続して初回push

```powershell
git remote add origin https://github.com/<username>/<repo>.git
git branch -M main
git push -u origin main
```

初回 push で GitHub の認証を求められる。
最近の GitHub は **パスワード認証ではなく Personal Access Token (PAT) または GitHub CLI** での認証が必要。
ブラウザが立ち上がる場合はそのまま OAuth で承認、PAT を求められたら https://github.com/settings/tokens で生成して貼り付け。

### 6. mkdocs のローカル動作確認

```powershell
pip install mkdocs mkdocs-material
python -m mkdocs serve
```

ブラウザで <http://127.0.0.1:8000> を開いてサイトを確認。

### 7. update.bat の作成と GitHub Pages 公開

`update.bat`(リポジトリのルート)をダブルクリック → 内部で:

1. `pip install --upgrade mkdocs mkdocs-material pymdown-extensions`
2. コミットメッセージを入力(Enterでデフォルト "Update docs")
3. `git add . && git commit && git push`
4. `python -m mkdocs gh-deploy --force` で `gh-pages` ブランチに自動デプロイ

実行後、 `https://<username>.github.io/<repo>/` でサイト公開される(初回は1〜2分待つ)。

## 日々の運用フロー

ドキュメントを更新したいとき。

1. `docs/` 配下の Markdown を編集(VS Code、Notepad、何でもOK)
2. ローカルプレビューしたければ別ターミナルで `python -m mkdocs serve` を起動しっぱなしにしておく(自動リロード)
3. 公開したくなったら `update.bat` をダブルクリック
4. 1〜2分後、iPhone で `https://annachloe2025.github.io/VRChat/` を開いて反映確認

iPhone からは公開URLを **ブックマーク or ホーム画面に追加** しておくと、アイコンタップ一発でアクセスできる(PWA的に振る舞う)。

## トラブルシューティング集

### Q: `mkdocs` コマンドが「認識されません」と出る

pip でインストールしたスクリプトが PATH に入っていないことが原因。
**応急対応**: `python -m mkdocs serve` のように Python 経由で呼ぶ。常にこの形でも問題ない。
**恒久対応**: `pip install` の警告メッセージに表示される `Scripts` フォルダのパスを Windows の環境変数 `Path` に追加。

### Q: `git init` が「config を lock できない」と失敗する

過去に壊れた `.git` フォルダが残っている。`Remove-Item -Recurse -Force .git` で削除して再実行。

### Q: GitHub への push でパスワードが通らない

最近のGitHubは PAT 必須。 https://github.com/settings/tokens で `repo` スコープ付きの token を発行して、`git push` 時のパスワード欄に貼り付ける。

### Q: Material for MkDocs に MkDocs 2.0 警告が出る

`mkdocs-material` の作者と `mkdocs` 本体のチームの間の方針対立による警告。
今のところ実害はない。気になるなら `requirements.txt` でバージョンを固定:

```
mkdocs>=1.5,<2.0
mkdocs-material>=9.0
```

### Q: 公開サイトに 404 が返る

`gh-deploy` 直後は反映に1〜2分かかる。それ以上待っても出ない場合は:
1. https://github.com/<username>/<repo>/settings/pages を開く
2. Source: "Deploy from a branch"、Branch: `gh-pages`、フォルダ: `/ (root)` になっているか確認
3. なっていなければ設定して Save

### Q: 日本語ファイル名やコミットメッセージが文字化けする

`update.bat` の冒頭に `chcp 65001` が入っていれば UTF-8 になる(本テンプレートは対応済み)。

## 参考リンク

- このリポジトリ: <https://github.com/annachloe2025/VRChat>
- 公開サイト: <https://annachloe2025.github.io/VRChat/>
- [MkDocs 公式](https://www.mkdocs.org/)
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- [VRChat Creators Documentation](https://creators.vrchat.com/)
- [GitHub Pages 公式](https://docs.github.com/pages)
