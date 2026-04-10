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
- 昇級申請機能（grade_up_requests）を実装・完了
- showToastを引数ありに統一（チェック時・申請時・申請済み時で異なるメッセージ表示）
- 部署管理機能を実装（departments テーブル・3階層表示・追加・削除）
- 役割を整理（boss_admin→全権管理者・dept_admin→部署管理者を追加）
- user_profiles に department_id カラムを追加
- メンバー追加モーダルに部署選択欄を追加（部署管理者選択時のみ表示）

---

## 2. 現在取り組み中の作業（最優先で対応）

### dept_admin（部署管理者）のフィルター実装
- admin.html・boss_dashboard.html の両画面で、dept_adminでログインした場合に**自分の部署のデータのみ**表示するよう制限する
- `user_profiles` の `department_id` を使って判定する

### 次にやること（ステップ4）
`boss_dashboard.html` を開き、ログイン時に `role` と `department_id` を取得して、`dept_admin` の場合は自部署のエリアに所属するメンバーのみ表示するよう修正する。

具体的な絞り込みの流れ：
```
1. ログインユーザーの department_id を取得する
2. その部署に所属するエリアIDを取得する（areas テーブル）
3. そのエリアに所属する店舗名を取得する（stores テーブル）
4. その店舗に所属するメンバーのみ表示する（user_profiles.store で絞り込む）
```

---

## 3. 機能追加メモ（未実装）

| # | 内容 |
|---|------|
| 7 | dept_adminでログイン時、boss_dashboard・admin両画面を自部署のみ表示にフィルターする |

---

## 4. 現在のファイル構成

```
デスクトップ/education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成（ステップ式チェック・昇級申請・トースト修正済み）
├── test_page.html      ✅ 完成（再試験機能対応）
├── boss_dashboard.html ✅ 完成（昇級申請バッジ・承認機能対応 ／ dept_adminフィルター未実装）
├── member_detail.html  ✅ 完成（コメント機能含む）
└── admin.html          ✅ 完成（部署管理・部署管理者追加対応 ／ dept_adminフィルター未実装）
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

---

## 7. データベース一覧

| テーブル名 | 内容 |
|-----------|------|
| user_profiles | メンバー情報・役割・等級・department_id |
| check_progress | チェック状況（step_idで管理） |
| check_items | チェック項目 |
| check_steps | チェックステップ（聞いた・やってみた等） |
| test_results | テスト結果（4択・○×） |
| text_answers | 記述式回答・採点 |
| test_sets | 問題集（合格基準点） |
| test_questions | テスト問題（配点） |
| test_choices | 4択の選択肢 |
| comments | 上司コメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
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

### 役割一覧
| role（DB値） | 表示名 | 権限 |
|---|---|---|
| member | メンバー | 自分の学習画面のみ |
| boss | 上司 | 全部署のダッシュボード閲覧 |
| admin | 管理者 | 全管理機能 |
| boss_admin | 全権管理者 | 全部署閲覧＋全管理機能 |
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理（実装中） |

### dept_adminのフィルター方針
```javascript
// ログイン時に取得する情報
// role === 'dept_admin' のとき department_id を保持する

// 絞り込みの流れ
// 1. areas テーブルから department_id が一致するエリアを取得
// 2. stores テーブルからそのエリアに属する店舗名を取得
// 3. user_profiles.store がその店舗名に含まれるメンバーのみ表示
```

### showToastの呼び出し方（member_top.html）
```javascript
showToast('保存しました ✓');                          // チェック時
showToast('進捗100%達成！上司に昇級申請を送りました 🎉'); // 初回申請時
showToast('昇級申請は送付済みです 📬');                 // 申請済み時

function showToast(msg) {
  var t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(function() { t.classList.remove('show'); }, 2000);
}
```

### 進捗率の計算方法（重要）
```javascript
var currentGradeItemIds = (items || []).map(function(i) { return i.id; });
var allSteps = (steps || []).filter(function(s) {
  return currentGradeItemIds.indexOf(s.item_id) !== -1;
});
var allStepIds = allSteps.map(function(s) { return s.id; });
var checkedCount = allStepIds.filter(function(id) {
  return checkedStepIds.indexOf(id) !== -1;
}).length;
```

---

## 10. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ5（仕上げ）が進行中です。

次のタスクは「#7 dept_admin（部署管理者）のフィルター実装」です。
boss_dashboard.html のコードを送るので、
dept_adminでログインした場合に自部署のメンバーのみ表示されるよう
修正してください。
```
