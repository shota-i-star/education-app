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
- コメント機能のDB実装
- チェックボックスのステップ式チェック実装
- 再試験許可機能の実装
- 記述式採点画面にメンバー名を表示
- 再試験管理で合格済みメンバーを除外

---

## 2. 機能追加メモ（未実装）

| # | 内容 |
|---|------|
| 5 | 進捗100%で上司に通知・承認で次の等級へ進む機能 |
| 6 | 組織階層に「部署」を追加（部署→エリア→店舗） |
| 7 | 部署ごとに管理者権限を付与（部署内データのみ閲覧可能） |

---

## 3. 現在のファイル構成

```
デスクトップ/education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成（ステップ式チェック対応）
├── test_page.html      ✅ 完成（再試験機能対応）
├── boss_dashboard.html ✅ 完成
├── member_detail.html  ✅ 完成（コメント機能含む）
└── admin.html          ✅ 完成（全管理機能・再試験管理含む）
```

---

## 4. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |

---

## 5. Supabase情報

| 項目 | 値 |
|------|------|
| Project URL | `https://rkclfgpewmjdluwufjng.supabase.co` |
| anon key | コード内の `SUPABASE_ANON_KEY` に記載 |
| service role key | コード内の `SUPABASE_SERVICE_KEY` に記載（admin.htmlのみ） |
| SDKバージョン | `@2.39.3`（固定） |

### 初期化の書き方
```javascript
// 通常画面
var client = supabase.createClient(SUPABASE_URL, SUPABASE_ANON_KEY, {
  auth: { persistSession: true }
});
// 管理者画面のみ
var adminClient = supabase.createClient(SUPABASE_URL, SUPABASE_SERVICE_KEY, {
  auth: { persistSession: false }
});
```

---

## 6. データベース一覧

| テーブル名 | 内容 |
|-----------|------|
| user_profiles | メンバー情報・役割・等級 |
| check_progress | チェック状況（step_id紐づけ） |
| check_items | チェック項目 |
| check_steps | チェックステップ（聞いた・やってみた等） |
| test_results | テスト結果（4択・○×） |
| text_answers | 記述式回答・採点 |
| test_sets | 問題集（合格基準点） |
| test_questions | テスト問題（配点） |
| test_choices | 4択の選択肢 |
| comments | 上司コメント |
| areas | エリア |
| stores | 店舗 |
| categories | チェックカテゴリ |
| retest_permissions | 再試験許可 |

---

## 7. 登録済みユーザー

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

## 8. 重要な実装メモ

### ログインの仕組み
社員番号 → `社員番号@education-app.com` に変換してSupabase Authで認証。
`login.html` 内の `emailMap` で管理。新しいユーザー追加時は `emailMap` にも追記が必要。

### チェックステップの仕組み
```
check_items（項目）
　└ check_steps（ステップ）order_noで順番管理
check_progress で step_id を保存（item_idは旧形式・使わない）
前のステップがチェック済みでないと次は押せない（ロック式）
```

### 進捗率の計算方法（重要・ロールバック禁止）
```javascript
// ✅ 正しい計算方法（step_idベース）
var { data: allSteps } = await client.from('check_steps').select('id, item_id');
var { data: checks } = await client.from('check_progress')
  .select('step_id').eq('user_id', userId).not('step_id', 'is', null);
var checkedStepIds = checks.map(function(c) { return c.step_id; });
var progress = Math.round(checkedStepIds.length / allSteps.length * 100);

// ❌ 古い計算方法（item_idベース）※使わない
// var checkedIds = checks.map(function(c) { return c.item_id; });
// var progress = Math.round(checkedIds.length / CHECK_ITEMS.length * 100);
```

### テスト構造
```
カテゴリ → 問題集（合格基準点） → 問題（配点）
```

### 再試験の流れ
```
不合格 → 管理者が再試験管理で許可
→ retest_permissions に記録
→ メンバーがテスト画面を開くと通知表示
→ used_at が記録されて再受験可能
→ 合格後は再試験管理から除外される
```

### ダッシュボードのタブ構成
エリア別・店舗別・個人別の3タブ。
それぞれ `renderAreaTable`・`renderStoreTable`・`renderMemberTable` 関数で描画。

---

## 9. 次にやること

- [ ] ⑤ 進捗100%で上司に通知・承認で次の等級へ進む機能
- [ ] ⑥⑦ 部署機能の実装
- [ ] デザイン調整・統一
- [ ] 公開（Netlifyでデプロイ）

---

## 10. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ5（仕上げ）が進行中です。
機能追加メモの⑤（進捗100%で上司に通知・承認で次の等級へ）から進めます。
```
