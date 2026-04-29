# 2026-04-30: 家プレハブ化(RPGPP_LT 導入 + VillageImporter プレハブ対応)

## やったこと

### RPGPP_LT (RPG Poly Pack - Lite) を導入

- Asset Store の **RPGPP_LT** を Unity プロジェクトに Import
- 配置先: `Assets/RPGPP_LT/`
- 内容を確認、家として使えるプレハブを特定:
    - `Prefabs/Buildings/Bld_closed/rpgpp_lt_building_01〜05.prefab`(5 種類の住居)
    - `Prefabs/Exterior/Wood_path/rpgpp_lt_shed_wood_01〜02.prefab`(小屋系)
    - その他: 樽・桶・テーブル・木製柵・木製通路・植物・岩・雲・雪 など多数
- プレハブ構造は **GameObject + MeshFilter + MeshRenderer のみ**(超シンプル、1mesh/1material)
- 全建物・小道具が **テクスチャアトラス `rpgpp_lt_tex_a.tga` を共有** → ドローコール削減で VRChat 親和性◎

### VillageImporter にプレハブ対応を追加

- `Assets/Editor/VillageImporter.cs` を拡張
- houses レイヤーは Cube ではなく **RPGPP_LT のプレハブを順番に Instantiate** する仕組みを実装
- 仕組み:
    - `HousePrefabPaths` 配列に 4 つのプレハブパスを定義(building_01〜04)
    - houses レイヤーの Tiled オブジェクトを処理する順に、配列を循環して割り当て
        - 家1 → building_01
        - 家2 → building_02
        - 家3 → building_03
        - 家4 → building_04
    - `s_houseIndex` で何軒目か追跡(`ImportVillage` 開始時にリセット)
    - プレハブが見つからない場合は従来の Cube にフォールバック
- 比較・退避用に `const bool USE_HOUSE_PREFABS = true` で ON/OFF 切替可能

### 地面埋まりの自動補正(AUTO_LIFT_HOUSES)

- 課題: プレハブの pivot や mesh の都合で、Y=0 で配置すると床面が地面より下にめり込むことがある
- 解決:
    - 配置直後に **全 Renderer の bounds.min.y** を計算
    - 0 未満なら、そのぶん Y 方向に持ち上げて床が Y=0 に揃うよう補正
    - 補正量を Console にログ出力(`lift=+0.XXm`)
- `const bool AUTO_LIFT_HOUSES = true` で ON/OFF 切替可能

### Y回転(rotation_y)サポートを追加(機能のみ、運用は保留)

- Tiled の各 house オブジェクトに `rotation_y`(float, 度)カスタムプロパティを設定すれば、その角度で Unity の Y 軸回転が適用される
- **矩形の中心 pivot で回転**するので、位置がズレない(Tiled 標準の rotation は左上 pivot のためズレる)
- フォールバックとして Tiled 標準の `obj.rotation` も読むようにした(rotation_y 優先)
- 単位: 度、Unity Y 軸回転(上から見て CW = 正)
- 値の例: `0`(初期向き), `90`(時計回り90°), `180`(反対向き), `270`(反時計回り90°)

### 詰まりどころ・保留事項

- **家のプレハブの「正面」がどっちか** はプレハブごとに違うので、実機で見ながら 4 軒それぞれ向きを決める必要がある
- **Tiled 上で向きを設定するワークフロー**(Custom Properties の運用、表記法、テンプレート化)を **どう運用するか方針未決** → 一旦保留

## 現在の状態

- ✅ houses レイヤーが Cube → RPGPP_LT 家プレハブ(building_01〜04)に置き換わった
- ✅ サイズ感は 10×10m の Tiled 矩形に対していい感じ(たまたま合っていた)
- ✅ 地面埋まりは自動 lift で解消
- ⏸️ 家の向き調整は Tiled での運用方法を決めてから着手

## 明日以降の TODO

- [ ] **Tiled で家の向きをどう指定するかの運用方針を決定**(rotation_y vs Tiled 標準回転、どこに書くか、命名規則 等)
- [ ] 家1〜4 の向きを実際に rotation_y で設定して再 Import
- [ ] サイズ感が違和感あれば、特定の家だけ scale を入れる、または Tiled 矩形を変える
- [ ] 小屋(rpgpp_lt_shed_wood_*)を補助建物として使うか検討
- [ ] AllSky Free から黄昏系 Skybox を選定して適用
- [ ] Terrain に草・土・砂利のレイヤー塗り分け

## 決定事項

- **家のメッシュは RPGPP_LT を採用**(無料、軽量、テクスチャアトラス共有で VRChat 適性高)
- **houses はプレハブ化、壁/畑/門は Cube + Mochie マテリアル**(混在運用)
- **プレハブ配置のロジックは `VillageImporter.cs` 内の `HousePrefabPaths` 配列で一元管理**
- **地面埋まりは bounds 自動 lift で対応**(プレハブ差し替えに強い)
- **家の向き指定は `rotation_y` カスタムプロパティ推奨**(Tiled 標準は左上 pivot で位置がズレるため)
