# 引継書 — 飲食事業部 教育管理アプリ
作成日：2026年4月8日

---

## ▼ 引継書の使い方
新しい会話を始めるときは、この引継書と仕様書を**両方**冒頭に貼り付けてから作業を開始してください。

---

## 1. 完了したこと

### フェーズ1〜4（完了）
- 全6画面の作成・Supabase連携・ログイン機能・集計・グラフ

### フェーズ5（進行中）
- 管理者画面の全機能を実データに連携
  - メンバー管理（追加・等級変更・役割変更・削除）
  - エリア・店舗管理（追加・削除）
  - カテゴリ・項目管理（追加・削除）
  - テスト管理（問題集・問題の追加・削除・配点設定）
  - 記述式採点
- コメント機能のDB実装（member_detail.html）
- チェックボックス保存のバグ修正（user_idをUUIDに統一）

---

## 2. 現在のファイル構成

```
デスクトップ/education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成
├── test_page.html      ✅ 完成
├── boss_dashboard.html ✅ 完成
├── member_detail.html  ✅ 完成（コメント機能含む）
└── admin.html          ✅ 完成（全管理機能実装済み）
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
| anon key | コード内の `SUPABASE_ANON_KEY` に記載 |
| service role key | コード内の `SUPABASE_SERVICE_KEY` に記載（admin.htmlのみ） |
| SDKバージョン | `@2.39.3`（固定） |

---

## 5. 登録済みユーザー

| 名前 | 社員番号 | パスワード | 役割 |
|------|---------|-----------|------|
| 山田 太郎 | 10001 | yamada1234 | member |
| 佐藤 花子 | 10002 | sato1234 | member |
| 田中 健二 | 10003 | tanaka1234 | member |
| 木村 あかり | 10004 | kimura1234 | member |
| 中村 誠 | 10005 | nakamura1234 | member |
| 小林 めぐみ | 10006 | kobayashi1234 | member |
| 鈴木 部長 | 00001 | suzuki1234 | boss_admin |

---

## 6. 重要な実装メモ

### ログインの仕組み
社員番号 → `社員番号@education-app.com` に変換してSupabase Authで認証。
`login.html` 内の `emailMap` で管理。新しいユーザー追加時は `emailMap` にも追記が必要。

### テスト構造
```
カテゴリ → 問題集（合格基準点） → 問題（配点）
```

### チェック項目のitem_id
```
grade1_training_phone / grade1_training_claim
grade1_azism_team / grade1_azism_hospitality
```
新しい項目追加時は `member_top.html` と `member_detail.html` の `CHECK_ITEMS` / `ALL_ITEMS` にも追記が必要。

---

## 7. 機能追加メモ（未実装）

| # | 内容 |
|---|------|
| 1 | メンバー管理から所属店舗を変更できるようにする |
| 2 | カテゴリ・項目管理から対象等級を修正できるようにする |
| 3 | チェックボックスの種類を項目ごとに設定できるようにする |
| 4 | 不合格メンバーに対して管理者から再試験を許可する機能 |

---

## 8. 次にやること

- [ ] 機能追加メモの実装
- [ ] デザイン調整・統一
- [ ] 公開（Netlifyでデプロイ）

---

## 9. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ5（仕上げ）が進行中です。
機能追加メモの実装から進めます。
```
