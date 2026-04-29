# 2026-04-29: 水路カービング + 水面シェーダー + Triplanar マテリアル自動生成

## やったこと

### Tiled に「門」を追加 → Importer も更新

- Tiled マップに `gates_paths` レイヤーを新設し、村の入口に門オブジェクトを2基配置
- Importer 側で **門を「lintel(まぐさ)」形式**にレンダリング:
    - 通常の Cube ではなく、上部 0.6m 厚の梁だけを生成
    - 下部に最低 2.0m の通行高を確保
    - これでアバターが門の下を通り抜けられる
- 既存の `frontier-village.tmj` を誤って上書きするインシデントが発生 → `frontier-village.tmj.before-gates.backup` から復元してリプレイ
- LayerMaterialPaths に `gates_paths` 用エントリを追加

### Terrain に水路を「掘る」(TerrainCarver.cs)

- 新規 Editor スクリプト: `Assets/Editor/TerrainCarver.cs`
- メニュー: `VRChat Village → Carve Water Channels`
- `frontier-village.tmj` の `water` レイヤーを読んで、各水路矩形に対応する Terrain 範囲を **0.5m 掘り込む**
- ハイトマップを直接書き換える方式で、Y軸反転(Tiled→Unity)も Importer と同じ式を共有
- これで水面 Plane が Terrain にめり込まず、水路らしい谷ができる
- カービング幅は実際の水路幅 +1m(両側 0.5m ずつバッファ)で、Plane 側の水面と隙間が出ない

### 水面: Stylized Water v1 を導入 → 何度か調整

- Stylized Water v2 は Built-in RP 非対応だったので **v1 に切り替え**
- `Assets/StylizedWater/Materials/StylizedWater_Desktop.mat` を採用
- VillageImporter で water レイヤーは Cube ではなく **Plane** を生成、Collider は除去
- 黄昏ワールド向けに以下を調整:
    - **Reflection Strength**: 0(空の白さが水面に強く出すぎていた)
    - **Glossiness**: 既定より下げて鏡面感を抑制
    - **Foam Alpha**: 下げて泡の主張を弱く
    - **Rim Size**: 4.6 → **0.3** ← これが「白っぽい部分が広すぎる」主因だった
- 横から覗くと水面の裏が見える問題 → Plane を矩形より +1.0m ずつ広げて Terrain の傾斜にめり込ませる形で解消(`widenM = 1.0`)

### Free Realistic Textures 導入 → 壁/家/畑/門にマテリアル適用

- アセット `Game Buffs / Free Realistic Textures` を Assets に追加
- 採用テクスチャ:
    - 壁: `Cracked_Soil_16`(乾いた土壁)
    - 家: 同上(後で差し替え可)
    - 畑: `Forest_Ground_12`
    - 門: `Wood_Planks_40`
- VillageImporter の `LayerMaterialPaths` に Standard マテリアルへのフォールバックパスを集約

### Cube の UV 引き伸ばし問題 → Poiyomi の World Pos UV を試す

- Cube 6面に同じ UV(0-1)が割り当てられているため、79m × 2m の壁では長辺方向にテクスチャが激しく引き伸ばされる
- 当初対策として Cube の Tiling を「面の縦横比に合わせて自動調整」する案を実装したが、Cube の全面で同じ Tiling になる制約から完全には解決せず
- Poiyomi Toon World シェーダーの **World Pos UV モード**(値 5)に切り替え
    - → ところが World Pos は **XZ 平面投影だけ**で、垂直壁では Y 方向に UV が変化しない
    - → 縦縞の悪化版になった
- Poiyomi Toon World の UV モード一覧に **Triplanar が無い**ことを Inspector で確認
    - 選択肢: UV0〜UV3, Panosphere, World Pos, Local Pos, Polar UV, Distorted UV, Matcap

### 解決: Mochie's Standard Shader (Triplanar World mode) に切り替え

- Mochie's Shader を導入(`Assets/Mochie/`)
- `Mochie/Standard` シェーダーには **本物の Triplanar 機能** がある:
    - `_PrimarySampleMode = 3` (Triplanar)
    - `_TriplanarCoordSpace = 1` (World)
    - 法線マップも triplanar 対応(bgolus 方式の正しい実装)
- `Assets/Editor/MaterialGenerator.cs` を Mochie/Standard 用に全面書き換え
    - メニュー: `VRChat Village → Generate Materials`
    - 既存 .mat があればシェーダー差し替えのみ(シーン内参照を維持)
    - 新規ならアセット作成
    - `_TRIPLANAR_ON` キーワードを明示有効化
- 結果: **Cube のどの面でも均等にテクスチャが貼られる** ようになった ✓
- TileSize を実機を見ながら調整:
    - 壁/家: 2.0m → **0.8m** per repeat
    - 畑: 3.0m → **1.2m**
    - 門(木目): 2.0m → **0.6m**

### テクスチャ差し替えと Detail レイヤー追加

- 壁を `Cracked_Soil_16` → `Asphalt_26` に変更(防壁としての重厚感)
- 家を `Cracked_Soil_16` → `Concrete_3` に変更(漆喰風)
- 「タイル感が目立つ、ランダム感が欲しい」という課題に対して試行:
    - 試行1: Mochie の **Stochastic Detail** (UV0) を Lerp で重ねた → タイル境界は消えるがテクスチャ内容自体は同じで、近くで見たときの「壁ごとの違い」は出ない
    - 試行2(採用): Detail を **Triplanar (World) で別テクスチャを大スケールで重ねる**方式に変更
- 採用した Detail 構成:
    - 壁 = Asphalt_26 + **Cracked_Soil_16** (5m, Mulx2, 0.35) → 経年汚れの斑模様
    - 家 = Concrete_3 + **Cracked_Soil_16** (5m, Overlay, 0.25) → 染みのコントラスト
    - 畑/門は Detail なし
- Detail も Triplanar (World) なので、**Cube のワールド座標が違えば Detail の当たる場所も違う** → 壁の節ごとに本当に見た目が変わる

### 詰まりどころ

- **Tiled ファイルを誤って前のフォルダに保存して上書き** → `.backup` から復元
- **Edit ツールで日本語ファイルが途中で切れる** 現象を再確認 → Write/heredoc に切り替え
- **Stylized Water v2 が SRP 専用で動かない** → v1 に戻す
- **Poiyomi Toon World の UV モードに Triplanar が無い**(Pro 版にはある)
- 水面が白っぽすぎた原因が **Rim Size** だと特定できるまで、Reflection / Glossiness / Foam を順番に潰した

## 現在の状態(v0.1 → v0.2 移行中)

- ✅ Tiled → Unity ブロックアウト(壁・家・畑・水路・門)
- ✅ Terrain (200×200m) と Stylized Trees
- ✅ 水路を Terrain に掘り込み済み
- ✅ 水面 Plane に Stylized Water v1(黄昏向け調整済)
- ✅ 壁・家・畑・門に Mochie/Standard Triplanar マテリアル適用
- ✅ 門は通り抜け可能(lintel 形式)

## 明日以降の TODO

- [ ] AllSky Free から黄昏系 Skybox を選定して適用
- [ ] Terrain に草・土・砂利のレイヤー塗り分け
- [ ] Tiled に 術式石①② / スポーン地点 / 見張り台 を追加
- [ ] スポーン位置を村の門付近に
- [ ] Build & Test で歩いて容量再確認
- [ ] (実機を見て)Detail Strength や TileSize を微調整

## 決定事項

- **マテリアルは Mochie/Standard (Triplanar World) を標準とする**(Poiyomi は別用途で残す)
- **壁=Asphalt_26 / 家=Concrete_3 / 畑=Forest_Ground_12 / 門=Wood_Planks_40** で確定
- **Detail は別テクスチャを Triplanar 大スケールで重ねる**方式を採用(Stochastic は近距離での「場所ごとの差」が出ないため不採用)
- **Tiled の `gates_paths` レイヤーは「上部 lintel」として描画する**(通り抜け可)
- **Terrain Carver は Importer と同じ Y 反転式を使う**(座標規約を 1 箇所に集約する未来課題あり)
- **Stylized Water は v1 を採用**(v2 は Built-in RP 非対応のため不採用)
