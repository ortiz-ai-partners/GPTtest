# ぬくもり在庫管理: 最小構成提案

## 1. ゴール
- うれしかった記憶を「登録」「一覧管理」「ランダム1件表示」できる、スマホ優先のWebアプリを最小構成で作る。
- 将来的に300円前後で販売できる形（iOS/Android配布）へ拡張しやすい設計にする。

## 2. MVPで実装する機能
1. 記録の追加
   - 項目: 日付 / 対象名 / 出来事 / 一言メモ / カテゴリ / お気に入り
2. 一覧表示
   - 一覧で各記録を表示
   - 削除
   - 「温もり温度」(3段階: 低・中・高)の設定・更新
3. ランダム表示システム
   - 「1件ちょうだい」ボタンでランダムに1件表示

## 3. 技術選定（最小構成）
### 推奨スタック
- フロントエンド: Next.js (App Router) + TypeScript
- UI: Tailwind CSS
- DB: SQLite（Prisma経由）
- 認証: MVPではなし（ローカルで使う前提）
- 配布:
  - まずはVercelでWeb公開
  - アプリ化はCapacitorでラップ（iOS/Android）

### この構成を推す理由
- 1つのリポジトリでフロントとAPIを完結できる。
- SQLiteでサーバーコストをほぼゼロに抑えられる（PoC向け）。
- 将来、PostgreSQLへ移行しやすい（Prismaを維持したまま差し替え可能）。
- Capacitor対応がしやすく、アプリストア販売の道がある。

## 4. データモデル（最小）
```prisma
model Memory {
  id          Int      @id @default(autoincrement())
  date        DateTime
  targetName  String
  event       String
  memo        String?
  category    String
  favorite    Boolean  @default(false)
  warmthLevel Int      @default(2) // 1=低,2=中,3=高
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt
}
```

## 5. 画面設計（スマホ優先）
1. ホーム
   - 上部に「1件ちょうだい」ボタン
   - 押下でランダム表示カード（対象名・出来事・一言メモ）
2. 登録画面
   - フォームは縦並び、入力しやすい大きめボタン
   - カテゴリはセレクト、お気に入りはトグル
3. 一覧画面
   - カード型リスト
   - 各カードに「削除」「温もり温度(3段階)」を配置

## 6. API（Next.js Route Handler）
- `POST /api/memories` 追加
- `GET /api/memories` 一覧
- `DELETE /api/memories/:id` 削除
- `PATCH /api/memories/:id/warmth` 温度更新
- `GET /api/memories/random` ランダム1件取得

## 7. 開発ステップ（短期）
- Day 1: プロジェクト初期化、DBモデル作成、追加API
- Day 2: 一覧/削除/温度更新APIとUI
- Day 3: ランダム表示UI、スマホ最適化、軽いテスト
- Day 4: Vercelデプロイ、フィードバック反映

## 8. アプリストア300円販売について
- Webアプリ単体はApp Store/Google Playで「有料アプリ」としてそのまま販売できない。
- 販売するなら次のどちらか:
  1. Capacitorでネイティブラップして有料アプリとして申請
  2. 無料配布 + アプリ内課金で「買い切り機能解除」
- 追加で必要なもの:
  - Apple Developer Program（年額）
  - Google Play Console登録料
  - プライバシーポリシー、サポートページ

## 9. 最小コスト運用案
- 初期: Vercel無料 + SQLite（小規模利用）
- ストア審査前: Capacitor化
- 利用者増加時: SQLite → PostgreSQLへ移行

## 10. まず決めるべき3点
1. 初期カテゴリ候補（例: 仕事 / 家族 / 友人 / 趣味）
2. 温もり温度の表示方法（色・アイコン）
3. ストア販売開始時期（Web先行か、最初からラップするか）
