# noble. — プロジェクト概要

## このプロジェクトについて
大阪市城東区野江の完全プライベート美容室「noble.」の公式ウェブサイト制作プロジェクト。
ナチュラル・温かみのあるデザインと、ホットペッパー脱却を見据えた自前予約システムの構築が目的。

## 店舗情報
- 店名：noble.
- 住所：大阪府大阪市城東区成育2-1-9
- アクセス：京阪野江東駅 徒歩30秒
- 営業時間：平日 10:00〜21:00 / 土日祝 9:00〜20:00
- 定休日：不定休
- LINE ID：@hxj9946m
- ホットペッパー：https://beauty.hotpepper.jp/slnH000519320/

## 技術スタック（モック段階）
- 純粋な HTML / CSS / JavaScript（フレームワークなし）
- ホスティング：Cloudflare Pages
- デプロイ：git push → Cloudflare Pages 自動デプロイ

## 技術スタック（Next.js移行後）
- フレームワーク：Next.js 14（App Router）
- 言語：TypeScript
- スタイリング：Tailwind CSS
- DB・認証：Supabase（PostgreSQL）
- メール送信：Resend
- ホスティング：Cloudflare Pages
- ドメイン：Cloudflare Registrar

## デザインコンセプト
- テーマ：ナチュラル・温かみ・手書き風
- 参考：noble.（ホットペッパー掲載版）のデザインをベースに独自実装
- カラーパレット：
  - 生成り #F5F1EA（--kinari）
  - 麻色 #EAE3D6（--asa）
  - 樹皮（ブラウン）#8C6E4B（--brown）
  - 苔色（グリーン）#6B7B5E（--moss）
  - 赤土（テラコッタ）#C4977A（--terra）
  - 墨色 #2A2620（--sumi）
- フォント：
  - Noto Serif JP（明朝体・メイン本文）
  - Klee One（手書き風・見出しアクセント）
  - Cormorant Garamond italic（英字装飾・ロゴ）
- ボタン：角丸なし・矩形スタイルで統一
- ナビ：ヒーロー内に配置、スクロール後に固定（生成り背景）

## 現在のファイル構成
```
web-site-mock/
├── index.html        # TOPページ（完成）
├── menu.html         # メニュー・料金詳細ページ（完成）
├── reservation.html  # Web予約フォーム（完成）
├── CLAUDE.md         # このファイル
└── README.md
```

## ページ構成（モック）
| ファイル | 内容 | 状態 |
|---|---|---|
| index.html | TOP（ヒーロー・コンセプト・メニュー概要・ギャラリー・スタイリスト・アクセス・予約CTA） | 完成 |
| menu.html | メニュー・料金詳細（スティッキーカテゴリナビ・全メニュー一覧） | 完成 |
| reservation.html | Web予約フォーム（日程→時間→メニュー→情報入力→確認→完了） | 完成 |

## ページ構成（Next.js移行後の予定）
1. TOP（/）
2. メニュー・料金（/menu）
3. スタイリスト紹介（/stylist）
4. ギャラリー（/gallery）
5. ご予約（/reservation）
6. アクセス・店舗情報（/access）
7. お知らせ（/news）
8. プライバシーポリシー（/privacy）
9. 管理画面（/admin）

## メニュー構成

| カテゴリ | 主なメニュー |
|---|---|
| クーポン・セット | 新規限定／全員対象／再来限定のセットプラン |
| カット | デザインカット ¥4,400〜 / メンズ ¥4,000〜 / キッズ ¥2,500〜 |
| カラー | フル ¥6,600〜 / リタッチ ¥4,400〜 / ダブル ¥12,100〜 / 白髪染め ¥5,500〜 |
| パーマ・縮毛矯正 | デザインパーマ ¥9,900〜 / デジタル ¥13,200〜 / 縮毛矯正 ¥15,400〜 / ポイント ¥5,500〜 |
| トリートメント・ヘッドスパ | クイックTR ¥2,200〜 / プレミアムTR ¥5,500〜 / アロマスパ ¥3,300〜 |
| その他 | ヘアセット ¥4,400〜 / 眉カット ¥1,100 / シャンプー・ブロー ¥2,750 |

## メニューデータ構造（DB設計用）

### クーポン／セットメニューの概念

ホットペッパーのクーポンUIを参考に、以下の3種バッジで対象者を区別する。

| バッジ | 対象 | 色（CSS変数） |
|---|---|---|
| 新規限定 | 初回来店のお客様のみ | `--moss`（グリーン） |
| 全員対象 | すべてのお客様 | `--terra`（テラコッタ） |
| 再来限定 | 2回目以降のお客様のみ | `--bark`（ブラウン） |

### テーブル設計

#### `menu_categories`（カテゴリマスタ）
```sql
id          serial primary key
slug        text unique not null   -- 'cut', 'color', 'perm', 'treatment', 'other', 'set'
name_ja     text not null          -- 'カット'
name_en     text                   -- 'Cut'
sort_order  int not null default 0
is_visible  boolean not null default true
```

#### `menus`（メニューマスタ）
```sql
id            serial primary key
category_id   int references menu_categories(id)
name          text not null
description   text
price_new     int                  -- 新規向け価格（円、nullの場合は price_return と同額）
price_return  int                  -- 再来向け価格（円）
price_label   text                 -- '¥4,400〜' など表示用文字列（price が変動する場合）
target        text check (target in ('新規', '全員', '再来'))  -- クーポン対象区分
is_coupon     boolean not null default false   -- クーポン・セットカードとして表示するか
is_inquiry    boolean not null default false   -- 「要問い合わせ」表示か
is_web_reservable boolean not null default true
sort_order    int not null default 0
is_visible    boolean not null default true
created_at    timestamptz default now()
updated_at    timestamptz default now()
```

#### `coupon_items`（クーポンに含まれる施術の内訳、任意）
```sql
id         serial primary key
coupon_id  int references menus(id)
menu_id    int references menus(id)   -- 構成メニュー（nullの場合はテキスト管理）
name       text                        -- 構成メニュー名（テキスト）
sort_order int not null default 0
```

#### `business_hours`（曜日ごとの営業時間）
```sql
id           serial primary key
day_of_week  int not null check (day_of_week between 0 and 6)  -- 0=日, 1=月, ..., 6=土
open_time    time not null    -- '10:00:00'
close_time   time not null    -- '21:00:00'
is_open      boolean not null default true
```

```
-- 初期データ例
-- 平日（月〜金）10:00〜21:00
(1, '10:00', '21:00', true)  -- 月
(2, '10:00', '21:00', true)  -- 火
(3, '10:00', '21:00', true)  -- 水
(4, '10:00', '21:00', true)  -- 木
(5, '10:00', '21:00', true)  -- 金
-- 土日祝 9:00〜20:00（祝日は closed_dates で個別管理）
(6, '09:00', '20:00', true)  -- 土
(0, '09:00', '20:00', true)  -- 日
```

#### `closed_dates`（不定休・臨時休業の管理）
```sql
id         serial primary key
date       date not null unique   -- 休業日
reason     text                    -- '研修', '臨時休業' など（管理画面表示用）
created_at timestamptz default now()
```

```
-- 使用例
('2025-08-13', 'お盆休み')
('2025-11-03', '臨時休業')
```

#### `blocked_slots`（ホットペッパー併用期間中の枠ブロック）
```sql
id         serial primary key
date       date not null
start_time time not null
end_time   time not null
reason     text    -- 'ホットペッパー予約', '外出' など
created_at timestamptz default now()
```

### データ例

```
-- カテゴリ
('set',       'クーポン・セット', 'Coupon & Set',     1)
('cut',       'カット',           'Cut',               2)
('color',     'カラー',           'Color',             3)
('perm',      'パーマ・縮毛矯正', 'Perm & Straight',  4)
('treatment', 'TR・ヘッドスパ',   'Treatment & Spa',  5)
('other',     'その他',           'Other',             6)

-- メニュー（抜粋）
(cat=set,       'デザインカット＋フルカラー＋髪質改善TR', target='新規', is_coupon=true, price_new=14300, price_label='¥14,300')
(cat=set,       'カット＋スキャルプヘッドスパ',          target='全員', is_coupon=true, price_return=6600, price_label='¥6,600')
(cat=set,       'メンテナンスカット＋カラーリタッチ',    target='再来', is_coupon=true, price_return=7700, price_label='¥7,700')
(cat=cut,       'デザインカット',    target='全員', price_return=4400, price_label='¥4,400〜')
(cat=cut,       'メンズカット',      target='全員', price_return=4000, price_label='¥4,000〜')
(cat=cut,       'キッズ・ジュニア',  target='全員', price_return=2500, price_label='¥2,500〜')
(cat=color,     'フルカラー',        target='全員', price_return=6600, price_label='¥6,600〜')
(cat=color,     'リタッチカラー',    target='全員', price_return=4400, price_label='¥4,400〜')
(cat=color,     'ダブルカラー',      target='全員', price_return=12100, price_label='¥12,100〜')
(cat=color,     '白髪染め',          target='全員', price_return=5500, price_label='¥5,500〜')
(cat=treatment, 'クイックTR',        target='全員', price_return=2200, price_label='¥2,200〜')
(cat=treatment, 'プレミアム髪質改善TR', target='全員', price_return=5500, price_label='¥5,500〜')
(cat=treatment, '極上アロマヘッドスパ', target='全員', price_return=3300, price_label='¥3,300〜')
(cat=other,     '眉カット',          target='全員', price_return=1100, is_web_reservable=false)
(cat=other,     'シャンプー・ブロー', target='全員', price_return=2750, price_label='¥2,750')
```

## 予約システム仕様（モック）
- 5ステップフォーム（日程 → 時間 → メニュー → 情報入力 → 確認 → 完了）
- 営業時間：平日10:00〜21:00 / 土日祝9:00〜20:00 / 30分単位
- バックエンドなし（UIのみ）

## 予約システム仕様（Phase 2以降）
- リアルタイム空き表示・自動予約確定
- キャンセル期限：来店24時間前まで
- 予約完了・前日リマインド・キャンセルの自動メール送信
- 会員登録不要・予約URLで変更・キャンセル可能
- ホットペッパー併用期間中はブロック枠機能で管理

## 管理画面仕様（Phase 2以降）
- Supabase Auth によるログイン
- 予約管理（カレンダー表示・ステータス管理・ブロック枠）
- メニュー管理（CRUD・並び替え・表示切替）
- 営業設定（営業時間・休業日・予約枠単位）
- ギャラリー管理（アップロード・並び替え）
- 顧客管理（来店履歴・メモ）
- お知らせ管理

## DB設計（主要テーブル）
- menus / categories / reservations / reservation_menus
- customers / blocked_slots / business_hours / closed_dates
- gallery_images / news / settings

## フェーズ
- ✅ Phase 0：デザインモック（HTML）＋仮予約フォーム
- 🔜 Phase 1：Next.js移行・公開サイト（静的コンテンツ）
- Phase 2：管理画面＋リアルタイム予約システム（Supabase連携）
- Phase 3：LINE通知・顧客管理強化・脱ホットペッパー支援
- Phase 4：ホットペッパー完全脱却

## 環境変数（.env.local）
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
RESEND_API_KEY=
NEXT_PUBLIC_SITE_URL=
```

## コーディング規約（Next.js移行後）
- TypeScriptの型は必ずつける（any禁止）
- コンポーネントはFunction Componentで統一
- CSSはTailwind CSS のみ使用（インラインスタイル禁止）
- fetchはSupabaseクライアント経由で行う
- エラーハンドリングは必ず実装する
- コメントは日本語でOK

## リポジトリ情報
- GitHub Organization：hair-salon-oosaka
- リポジトリ：web-site-mock
- Cloudflare Pages にデプロイ済み