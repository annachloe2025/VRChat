# VRChat 制作環境

VRChat ワールド開発の現代的なセットアップ手順と、2021年以前から復帰する人向けの差分メモ。

!!! info "対象読者"
    2021年頃まで VRChat ワールドを作っていたが、しばらく離れていて手順を忘れた人。

## 2021年からの主な変化

### 変化1: VCC(VRChat Creator Companion)が標準に

2022年末頃から、VRChat の SDK は VCC 経由で配布されるのが標準になった。VCCはWindowsアプリで:

- 適切な Unity バージョンを把握(古すぎる/新しすぎると弾く)
- 新規プロジェクトのテンプレート生成
- SDK3 (Worlds または Avatars) のインストール
- UdonSharp、ClientSim などの追加ツールの管理
- プロジェクトのバックアップ機能

を一手に引き受ける。**昔みたいに `VRCSDK3-WORLD-xxx.unitypackage` を手動でインポートする運用は終了した。**

### 変化2: Unity 2022.3 LTS が必須

2021年当時は Unity 2019.4 LTS が標準だったが、今は 2022.3 LTS。
さらに **特定のパッチバージョン**(例: `2022.3.22f1` 等)を VRChat 側が指定することがあるので、VCC が要求するバージョンと合わせる。

### 変化3: UdonSharp(USharp)が標準扱いに

2021年は Udon(ビジュアルスクリプト)が主流で、UdonSharp はサードパーティ実装だった。
今は **UdonSharp が VCC からインストールできる正式扱い**。C# でスクリプトが書けるので、プログラミング経験者には圧倒的にこっちが楽。

### 変化4: ClientSim でエディタ内テスト可能

昔は「アップロード→VRChatに入って確認→修正→再アップロード」のループだったが、今は ClientSim で **Unityエディタ内でアバターを操作してワールドをテストできる**。アップロード前に動作確認が完結する。

## セットアップ手順(モダン版)

### ステップ1: VRChat アカウントの確認

久しぶりにログインして、アカウントが「**New User**」以上のトラスト(できれば「Visitor」を超える)になっているか確認。
新規アカウントだと**ワールドのアップロードに制限**がかかることがある。古いアカウントなら問題ない。

### ステップ2: VRChat Creator Companion(VCC)をインストール

公式サイト <https://creators.vrchat.com/> から VCC をダウンロードしてインストール。
起動後、VRChatアカウントでログイン。

### ステップ3: VCC で新規ワールドプロジェクトを作成

VCC で「New」→「World」テンプレートを選択。

- 保存先: `C:\Users\hoeho\Documents\Claude\VRChat\unity` を指定
- プロジェクト名: 例 `VRC-FantasyWorld-Unity`

これで:

- 適切な Unity バージョンが指定された Unity プロジェクトが作られる
- VRChat Worlds SDK が自動でインポートされる
- UdonSharp と ClientSim がオプションで追加できる

「Unity がインストールされていない」と言われたら、Unity Hub から指定バージョンをインストール。

### ステップ4: プロジェクトを Unity で開く

VCC の「Open Project」または Unity Hub から開く。最初の起動はインポートで時間がかかる(数分〜十数分)。

### ステップ5: VRCWorld プレハブをシーンに置く

新規シーンに、SDK 同梱の `VRCWorld` プレハブをドラッグ&ドロップ。
これがワールドのスポーン地点・基本機能の核になる。

### ステップ6: テスト & アップロード

- **エディタ内テスト**: ClientSim を有効にして再生ボタン → アバターを操作してワールドを歩ける
- **アップロード**: VRChat SDK の Build & Publish パネルから
    - 「Build & Test」: 自分だけのテスト用ローカルワールド
    - 「Build & Publish」: VRChatアカウントに紐づいたワールドとしてアップ

## 最初におすすめの動き

久しぶりの再開時は **いきなりファンタジー世界を作り始めるのは非効率**。
次の順序を強く推奨:

1. VCC でテンプレートからワールドプロジェクトを1個作る
2. 空っぽのまま Build & Test → Build & Publish(Privateで)
3. VRChat に入って自分のワールドを訪問

この **End-to-End の往復を1回成功させる** のが先決。
これで「現代の手順」と「自分のアカウント・PC環境で詰まる箇所」が分かる。

その後で:

- Unity の標準 Terrain で地形を置く練習
- 無料アセットの建物を置く練習
- HDRI で空を変える練習

を1〜2回挟んでから、ファンタジーワールドの本番制作に入ると詰まりが減る。

## 注意事項

- このプロジェクトの方針として、`unity/` フォルダは Git 管理外。バックアップは OneDrive 等で別途
- VCC 自体の設定や認証情報は Windows 側に保存される(プロジェクトリポジトリには含まれない)
- VRChat の規約やアップロード制限は時々変わるので、行き詰まったら公式ドキュメント <https://creators.vrchat.com/worlds/> を確認

## 参考リンク

- [VRChat Creators Documentation](https://creators.vrchat.com/)
- [VCC ダウンロード](https://creators.vrchat.com/)
- [VRChat World Creation](https://creators.vrchat.com/worlds/)
- [UdonSharp Documentation](https://udonsharp.docs.vrchat.com/)
