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
| カット | デザインカット ¥4,400 / 学生 ¥4,000 / キッズ ¥3,500 |
| カラー | フル ¥6,600 / リタッチ ¥4,800 / ブリーチ ¥7,700 / Wカラー ¥13,500〜 |
| パーマ | パーマ ¥7,700〜 / 前髪パーマ ¥3,300〜 |
| 縮毛矯正 | ¥11,000 / 部分 ¥4,400〜 |
| トリートメント | AMASIA ¥4,400 / TOKIOインカラミ ¥5,500 |
| ヘッドスパ | 炭酸スパ20分＋TR ¥4,400 |
| Popular No.1 | カット＋カラー＋超音波TR ¥9,900 |

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