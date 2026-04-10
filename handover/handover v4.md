# 引継書 — 飲食事業部 教育管理アプリ
作成日：2026年4月8日

---

## ▼ 引継書の使い方
新しい会話を始めるときは、この引継書と仕様書を**両方**冒頭に貼り付けてから作業を開始してください。

---

## 1. 完了したこと

### フェーズ1（完了）
- 全6画面の見た目を作成

### フェーズ2（完了）
- チェックボックスの保存・読み込み・進捗率計算
- テストの自動採点（4択・○×）・選択肢の復元
- 記述式テストの提出・保存・管理者採点・合計点反映

### フェーズ3（完了）
- Supabase Authでログイン機能を実装
- 役割に応じた画面切り替え
- 全画面にログインチェック・権限チェック・ログアウトボタンを追加

### フェーズ4（完了）
- ダッシュボードのメンバー一覧を実データで表示
- 進捗率・テスト点数・合否を実データで集計
- エリア別・店舗別グラフを実データで描画
- メンバー詳細画面を実データに連携
- 詳細ボタンからURLパラメータでメンバーIDを渡す仕組みを実装
- テスト用メンバー6名をSupabaseに登録

---

## 2. 現在のファイル構成

```
デスクトップ/education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成
├── test_page.html      ✅ 完成
├── boss_dashboard.html ✅ 実データ連携済み
├── member_detail.html  ✅ 実データ連携済み
└── admin.html          ✅ 完成
```

---

## 3. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |

---

## 4. Supabase情報

| 項目 | 値 |
|------|------|
| Project URL | `https://rkclfgpewmjdluwufjng.supabase.co` |
| anon key | コード内の `SUPABASE_KEY` 変数に記載 |
| SDKバージョン | `@2.39.3`（固定） |

### 初期化の書き方（必ずこの形式を使う）
```javascript
var client = supabase.createClient(SUPABASE_URL, SUPABASE_KEY, {
  auth: { persistSession: true }
});
```

---

## 5. 登録済みユーザー

| 名前 | 社員番号 | パスワード | 役割 | 店舗 | エリア |
|------|---------|-----------|------|------|------|
| 山田 太郎 | 10001 | yamada1234 | member | 渋谷店 | 東京エリア |
| 佐藤 花子 | 10002 | sato1234 | member | 渋谷店 | 東京エリア |
| 田中 健二 | 10003 | tanaka1234 | member | 新宿店 | 東京エリア |
| 木村 あかり | 10004 | kimura1234 | member | 新宿店 | 東京エリア |
| 中村 誠 | 10005 | nakamura1234 | member | 梅田店 | 大阪エリア |
| 小林 めぐみ | 10006 | kobayashi1234 | member | 難波店 | 大阪エリア |
| 鈴木 部長 | 00001 | suzuki1234 | boss_admin | － | 東京エリア |

---

## 6. 重要な実装メモ

### ログインの仕組み
社員番号 → メールアドレスに変換してSupabase Authで認証。
`login.html` 内の `emailMap` で管理。新しいユーザー追加時は `emailMap` にも追記が必要。

### メンバー詳細へのURL遷移
```javascript
// ダッシュボードから詳細へ
window.location.href = 'member_detail.html?id=' + userId;

// 詳細画面でIDを受け取る
var params = new URLSearchParams(window.location.search);
TARGET_USER_ID = params.get('id');
```

### チェック項目の定義（member_top.html・member_detail.html共通）
```javascript
var ALL_ITEMS = [
  'grade1_training_phone',
  'grade1_training_claim',
  'grade1_azism_team',
  'grade1_azism_hospitality'
];
```
新しい項目を追加するときはここにも追記が必要。

---

## 7. 次にやること（フェーズ5）

### フェーズ5の目標
デザイン調整・残機能の実装・公開（デプロイ）

### 具体的にやること
- [ ] デザインの調整・統一
- [ ] 管理者画面のメンバー追加・編集機能を実際に動かす
- [ ] コメント機能のDB実装
- [ ] 新しいユーザー追加時のlogin.html emailMap自動化
- [ ] Netlify等でデプロイして実際のURLで使えるようにする

---

## 8. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ4が完了しており、フェーズ5（仕上げ・公開）を進めます。
まずフェーズ5の進め方を説明してください。
```

---

## 9. 積み残し・未決定事項

| 項目 | 内容 |
|------|------|
| テストの再受験ルール | 未定 |
| メンバーへのコメント通知 | 未定 |
| 公開範囲 | 社内限定 or インターネット公開 |
| コメント機能のDB実装 | フェーズ5で実装予定 |
| デザイン調整 | フェーズ5で実施予定 |
