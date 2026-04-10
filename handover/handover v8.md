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
- ダッシュボードにエリア別・店舗別・個人別タブを復元
- 進捗率をstep_idベースで計算するよう修正
- 昇級申請機能（grade_up_requests）を実装中

---

## 2. 現在取り組み中の問題（最優先で対応）

### 昇級申請のトーストが表示されない問題
- `requestGradeUp` 関数は正しく呼ばれている（Console確認済み）
- `CURRENT_USER` と `CURRENT_GRADE` も正しい値（1等級）
- Supabaseへの保存が成功しているか未確認

### 次にやること
以下のSQLで申請データが保存されているか確認する：
```sql
SELECT * FROM grade_up_requests 
WHERE member_id = 'e6de7f06-05e1-44da-b212-a00df3d5058f'
ORDER BY requested_at DESC;
```

結果によって対応を分ける：
- データがある → `showToast` の呼び出し方に問題あり
- データがない → Supabaseへの保存でエラーが起きている

---

## 3. 機能追加メモ（未実装）

| # | 内容 |
|---|------|
| 6 | 組織階層に「部署」を追加（部署→エリア→店舗） |
| 7 | 部署ごとに管理者権限を付与（部署内データのみ閲覧可能） |

---

## 4. 現在のファイル構成

```
デスクトップ/education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成（ステップ式チェック・昇級申請機能実装中）
├── test_page.html      ✅ 完成（再試験機能対応）
├── boss_dashboard.html ✅ 完成（昇級申請バッジ・承認機能対応）
├── member_detail.html  ✅ 完成（コメント機能含む）
└── admin.html          ✅ 完成（全管理機能・再試験管理含む）
```

---

## 5. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |

---

## 6. Supabase情報

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

## 7. データベース一覧

| テーブル名 | 内容 |
|-----------|------|
| user_profiles | メンバー情報・役割・等級 |
| check_progress | チェック状況（step_idで管理） |
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
| grade_up_requests | 昇級申請 |

---

## 8. 登録済みユーザー

| 名前 | 社員番号 | パスワード | 役割 | 等級 |
|------|---------|-----------|------|------|
| 山田 太郎 | 10001 | yamada1234 | member | 1等級 |
| 佐藤 花子 | 10002 | sato1234 | member | 1等級 |
| 田中 健二 | 10003 | tanaka1234 | member | 1等級 |
| 木村 あかり | 10004 | kimura1234 | member | 2等級 |
| 中村 誠 | 10005 | nakamura1234 | member | 1等級 |
| 小林 めぐみ | 10006 | kobayashi1234 | member | 0等級 |
| 鈴木 部長 | 00001 | suzuki1234 | boss_admin | － |

---

## 9. 重要な実装メモ

### 進捗率の計算方法（重要）
```javascript
// 現在の等級の項目に紐づくステップのみを対象にする
var currentGradeItemIds = (items || []).map(function(i) { return i.id; });
var allSteps = (steps || []).filter(function(s) {
  return currentGradeItemIds.indexOf(s.item_id) !== -1;
});
var allStepIds = allSteps.map(function(s) { return s.id; });
var checkedCount = allStepIds.filter(function(id) {
  return checkedStepIds.indexOf(id) !== -1;
}).length;
// 分母：現在の等級の項目に紐づくステップ数のみ
// 分子：そのうちチェック済みのもの
```

### 昇級申請の流れ
```
全ステップ完了（進捗100%）
　↓
grade_up_requests に自動登録
　↓
boss_dashboardのヘッダーに🔔バッジ表示
　↓
上司が承認 → user_profilesのgradeを+1
上司が却下 → statusをrejectedに更新
```

### grade_up_requestsテーブル
| カラム名 | 内容 |
|---------|------|
| id | 自動採番 |
| member_id | メンバーのuuid |
| member_name | メンバー名 |
| current_grade | 申請時の等級 |
| status | pending / approved / rejected |
| requested_at | 申請日時 |
| resolved_at | 承認・却下日時 |

### showToastの呼び出し方
```javascript
// member_top.html では引数なし
function showToast() {
  var t = document.getElementById('toast');
  t.classList.add('show');
  setTimeout(function() { t.classList.remove('show'); }, 2000);
}

// boss_dashboard.html では引数あり
function showToast(msg) {
  var t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(function() { t.classList.remove('show'); }, 2500);
}
```

---

## 10. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ5（仕上げ）が進行中です。

現在、昇級申請のトーストが表示されない問題を調査中です。
requestGradeUp関数は正しく呼ばれており、CURRENT_USERとCURRENT_GRADEも正しい値です。
まず以下のSQLでデータが保存されているか確認するところから再開してください。

SELECT * FROM grade_up_requests 
WHERE member_id = 'e6de7f06-05e1-44da-b212-a00df3d5058f'
ORDER BY requested_at DESC;
```
