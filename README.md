# VRChat Fantasy World

自作ファンタジー小説の世界観をVRChatワールドとして再構築する個人プロジェクト。

## ゴール

頭の中にあるファンタジー世界の「ヴィジュアルイメージ」を、自分の足で歩ける空間として外在化すること。
完成度より、世界観を確かめるための **思考のための空間** を優先する。

## 構成

このリポジトリは **世界観ドキュメントと進捗管理の母艦** として機能する。
Unity プロジェクト本体・Blender 作業ファイル・参考素材は Git 管理外(OneDrive 等で別管理)。

| パス | 内容 |
|------|------|
| `docs/` | mkdocs ソース。世界観設定、ヴィジュアル方針、進捗日誌など |
| `mkdocs.yml` | mkdocs 設定 |
| `CLAUDE.md` | Claude 作業メモリ(プロジェクト方針・運用ルール) |
| `TASKS.md` | タスク管理 |
| `unity/` | Unity プロジェクト(.gitignore で除外) |
| `blender/` | Blender ファイル(.gitignore で除外) |
| `refs/` | 参考画像・ムードボード素材(.gitignore で除外) |

## 環境

- Unity 2022.3 LTS
- VRChat SDK3 (Worlds)
- Blender
- Python 3.x + mkdocs + mkdocs-material

## ローカルプレビュー

```bash
pip install mkdocs mkdocs-material
mkdocs serve
```

ブラウザで http://127.0.0.1:8000 を開く。
