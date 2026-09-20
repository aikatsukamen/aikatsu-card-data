# aikatsu-card-data

- データカードダス「アイカツ！」シリーズのカード情報
- ★ファンメイドの非公式です。情報の正確さは保証していません。★
- ★テキストの著作権は権利者に帰属します。★

## ファイル構成

```
index.json        配信している全ファイルの一覧
coords.json       コーデ
v/<version_id>.json   弾ごとのカード
```

### index.json

```json
{
  "generated_at": "2026-09-21T05:12:00+09:00",
  "works": [{ "work": "アイカツ！", "versions": ["215001", "215002", "..."] }],
  "files": [
    {
      "path": "v/215001.json",
      "kind": "cards",
      "version_id": "215001",
      "work": "アイカツ！",
      "series_title": "第1弾",
      "count": 52,
      "hash": "44e252944e2b6d6b",
      "updated_at": "2026-09-21T05:12:00+09:00"
    },
    { "path": "coords.json", "kind": "coords", "count": 25, "hash": "...", "updated_at": "..." }
  ]
}
```

- `hash`: 中身のSHA-256の先頭16桁
- `updated_at`: hashが変わった時点のファイル更新時刻

### v/&lt;version_id&gt;.json

```json
{
  "version_id": "215001",
  "work": "アイカツ！",
  "series_title": "第1弾",
  "count": 52,
  "cards": [{ "card_id": "01-01", "name": "オーロラキスキャミソール", "...": "..." }]
}
```

値が無い項目はキーごと省略

### coords.json

- カード裏面で定義されている `トップス/ボトムス/シューズ/アクセサリー` の組み合わせ。
- 情報求む。

```json
{ "count": 25, "coords": [{ "coord_id": "C-d01ae7", "tops": "...", "source": "back:E1-05 back:E1-06" }] }
```

## 取得のしかたの例

```js
const base = 'https://aikatsukamen.github.io/aikatsu-card-data';
const index = await (await fetch(`${base}/index.json`)).json();

for (const f of index.files) {
  if (cache[f.path]?.hash === f.hash) continue; // 変わっていないので更新をスキップ
  cache[f.path] = { hash: f.hash, body: await (await fetch(`${base}/${f.path}`)).json() };
}
```

## JSONの各キーについて

### 全作品に共通

| 列             | 内容                                           |
| -------------- | ---------------------------------------------- |
| `version_id`   | 弾の識別子。作品をまたいで一意                 |
| `series_title` | 弾の名称（第1弾 / かがやきのジュエル3弾 など） |
| `card_id`      | カード番号（券面の表記）                       |
| `name`         | カード名                                       |
| `type`         | タイプ                                         |
| `rarity`       | レアリティ                                     |
| `image_card`   | カード画像のURL                                |

### アイカツ！〜アンコール（プラネット以外）

| 列             | 内容                                                                  |
| -------------- | --------------------------------------------------------------------- |
| `category`     | カテゴリ                                                              |
| `brand`        | ブランド。ブランドが無い場合は `ノーブランド`                         |
| `dress_appeal` | ドレスアピール                                                        |
| `effect`       | アクセサリーの効果。ドレスアピールとは排他                            |
| `appeal_point` | アピールポイント                                                      |
| `idol`         | 掲載アイドル。複数人は `,` 区切り。`-` はアイドルが写っていないカード |

### 作品固有の列

**アイカツ！**

| 列                                   | 内容                                 |
| ------------------------------------ | ------------------------------------ |
| `lucky_star`                         | ラッキースターの数（0〜3）           |
| `constellation` / `constellation_ja` | 星座（ラテン名 / 日本語名）          |
| `romance_dream`                      | ロマンス・ドリーム                   |
| `boom`                               | BOOM                                 |
| `fever`                              | フィーバー                           |
| `distribution`                       | 配布形態（プロモーションカードのみ） |
| `brand_internal`                     | ブランドの英語表記                   |

- 星座・ロマンス・ドリーム・BOOM は年度ごとの排他情報
  - 14シリーズ：星座
  - 15シリーズ：ロマンス・ドリーム
  - 16シリーズ：BOOM、フィーバー

**アイカツスターズ！**

| 列                  | 内容                              |
| ------------------- | --------------------------------- |
| `dress_key`         | 公式サイトのドレス識別子          |
| `appeal_point_plus` | R＋などでのアピールポイント       |
| `note`              | 備考                              |
| `image_item`        | アイテムだけを切り抜いた画像のURL |
| `full_slots`        | フルコーデが埋めるスロット        |

**アイカツフレンズ！ / アイカツオンパレード！**

| 列               | 内容                                       |
| ---------------- | ------------------------------------------ |
| `cosme`          | コスメの種類（ルージュ / チーク / コロン） |
| `cosme_effect`   | コスメの効果                               |
| `note`           | 備考                                       |
| `site_detail_id` | 公式サイトの詳細ページのID                 |
| `full_slots`     | フルコーデが埋めるスロット                 |
| `school`         | 学園（オンパレードのみ）                   |

**アイカツプラネット！**

| 列                                                                      | 内容                                      |
| ----------------------------------------------------------------------- | ----------------------------------------- |
| `level`                                                                 | ドレシアレベル                            |
| `skill_text`                                                            | スキルの全文                              |
| `skill_condition` / `skill_effect`                                      | 全文を条件と効果に分けたもの              |
| `cond_kind` `cond_target` `cond_op` `cond_value`                        | 条件を構造化したもの                      |
| `effect_scope` `effect_target` `effect_kind` `effect_op` `effect_value` | 効果を構造化したもの                      |
| `unit_skill`                                                            | ユニットスキル                            |
| `illustrator`                                                           | イラストレーター                          |
| `description`                                                           | 説明文                                    |
| `note` / `distribution`                                                 | 備考 / 入手方法                           |
| `rarity_code`                                                           | レアリティの略号（N / R / PR / CP / SEC） |
| `image_card_back`                                                       | 裏面画像のURL                             |
| `site_detail_id`                                                        | 公式サイトの詳細ページのID                |

**アイカツ！アンコール**

| 列                | 内容                                                                         |
| ----------------- | ---------------------------------------------------------------------------- |
| `image_card_back` | 裏面画像のURL                                                                |
| `description`     | アイテムの説明文                                                             |
| `work`            | そのカードの出典作品（アイカツ！ / アイカツアカデミー！ など）               |
| `is_parallel`     | パラレルカードかどうか                                                       |
| `rarity_code`     | レアリティの略号（N / R / PR / ER）                                          |
| `source`          | `cardlist` は公式カードリストに掲載済み、`manual` は未掲載で手動追加したもの |

### プラネットのスキル

| `cond_op`          | 意味                           |
| ------------------ | ------------------------------ |
| `eq` / `ne`        | 等しい / 等しくない            |
| `gt` / `lt` / `ge` | より大きい / より小さい / 以上 |
| `has` / `not_has`  | 含む / 含まない                |
| `max`              | 最大値                         |

| `effect_op`   | 意味                                                                                              |
| ------------- | ------------------------------------------------------------------------------------------------- |
| `add` / `sub` | 加算 / 減算。量は `effect_value`                                                                  |
| `up`          | 上昇。強さを `effect_value` に 1〜4 で持つ（たまりやすい=1 / とても=2 / すごく=3 / ものすごく=4） |
| `count`       | 対象の数だけ加算                                                                                  |

```json
{
  "skill_text": "あいてがホロスコープだったら、じぶんのレベル＋２",
  "cond_kind": "相手のタイプ",
  "cond_target": "ホロスコープ",
  "cond_op": "eq",
  "effect_scope": "じぶん",
  "effect_kind": "レベル",
  "effect_op": "add",
  "effect_value": 2
}
```
