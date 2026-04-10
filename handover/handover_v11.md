# 引継書 — 飲食事業部 教育管理アプリ
作成日：2026年4月9日

---

## ▼ 引継書の使い方
新しい会話を始めるときは、この引継書と仕様書を**両方**冒頭に添付してから作業を開始してください。

---

## 1. 完了したこと

### フェーズ1〜4（完了）
- 全6画面の作成・Supabase連携・ログイン機能・集計・グラフ

### フェーズ5（完了）
- 管理者画面の全機能を実データに連携
- コメント機能のDB実装
- チェックボックスのステップ式チェック実装
- 再試験許可機能の実装
- 記述式採点画面にメンバー名を表示
- 再試験管理で合格済みメンバーを除外
- ダッシュボードにエリア別・店舗別・個人別タブを復元
- 進捗率をstep_idベースで計算するよう修正
- 昇級申請機能（grade_up_requests）を実装・完了
- showToastを引数ありに統一
- 部署管理機能を実装（departments テーブル・3階層表示・追加・削除）
- 役割を整理（boss_admin→全権管理者・dept_admin→部署管理者を追加）
- user_profiles に department_id カラムを追加
- メンバー追加モーダルに部署選択欄を追加
- **dept_admin フィルター完全実装（下記参照）**
- **カテゴリ・テスト管理を部署×等級構造に再設計（下記参照）**

### フェーズ5で完了した dept_admin 対応
| 対象 | 内容 |
|------|------|
| login.html | dept_admin ログイン後に boss_dashboard.html へ遷移するよう追加 |
| boss_dashboard.html | 自部署メンバーのみ表示・昇級申請も自部署のみ |
| admin.html（メンバー管理） | 自部署メンバーのみ表示 |
| admin.html（記述式採点） | 自部署メンバーの回答のみ表示 |
| admin.html（再試験管理） | 自部署メンバーのみ表示 |
| admin.html（部署・エリア・店舗） | 自部署のみ表示・部署追加削除ボタン非表示 |
| admin.html（カテゴリ・項目） | 自部署に紐づくカテゴリのみ表示 |
| admin.html（テスト管理） | 自部署に紐づく問題集のみ表示 |

### フェーズ5で完了したカテゴリ・テスト再設計
- `categories` テーブルに `department_id` カラム追加
- `category_departments` 中間テーブルを新規作成（1カテゴリ→複数部署対応）
- カテゴリ・項目管理の表示を「部署→等級→カテゴリ→項目」構造に変更
- テスト管理の表示を「部署→等級→問題集→問題」構造に変更
- カテゴリ追加モーダルをチェックボックス式（複数部署同時紐づけ可）に変更

---

## 2. 現在取り組み中の作業

**なし（フェーズ5すべて完了）**

次の開発テーマが決まれば、新しい引継書を作成してください。

---

## 3. 機能追加メモ（未実装）

現時点で未実装の要件はありません。

---

## 4. 現在のファイル構成

```
education-app/
├── login.html          ✅ 完成
├── member_top.html     ✅ 完成（ステップ式チェック・昇級申請・トースト修正済み）
├── test_page.html      ✅ 完成（再試験機能対応）
├── boss_dashboard.html ✅ 完成（dept_adminフィルター対応済み）
├── member_detail.html  ✅ 完成（コメント機能含む）
├── admin.html          ✅ 完成（dept_adminフィルター・部署×等級構造対応済み）
├── spec_doc v11.md     📄 仕様書（最新）
└── handover_v11.md     📄 引継書（最新）
```

---

## 5. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | OneDrive/デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |
| AIコーディング | Cowork（Claude）にフォルダを接続して直接編集 |

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
| categories | チェックカテゴリ（name にユニーク制約あり） |
| category_departments | カテゴリ×部署の中間テーブル（多対多） ★新規 |
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
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理 |

### dept_adminのフィルター方針
```javascript
// boss_dashboard.html / admin.html 共通のグローバル変数
var currentRole      = null;  // ログインユーザーの役割
var currentDeptId    = null;  // dept_admin の department_id
var allowedMemberIds = null;  // null=全員 / 配列=自部署メンバーIDのみ

// initDeptFilter() で事前取得する流れ
// 1. areas テーブルから department_id が一致するエリアIDを取得
// 2. stores テーブルからそのエリアに属する店舗名を取得
// 3. user_profiles.store がその店舗名に含まれるメンバーIDのみ保持
```

### category_departments（カテゴリ×部署の紐づけ）
```javascript
// カテゴリを複数部署に紐づける中間テーブル
// カテゴリ追加時は categories に insert → category_departments に insert
// 同名カテゴリが既存の場合は新規作成せず既存IDに紐づけを追加（upsert）

// 部署ごとのカテゴリ取得パターン
var deptCatIds = catDepts
  .filter(cd => cd.department_id === dept.id)
  .map(cd => cd.category_id);
var deptCats = categories.filter(c => deptCatIds.includes(c.id));
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

### showToastの呼び出し方（member_top.html）
```javascript
showToast('保存しました ✓');
showToast('進捗100%達成！上司に昇級申請を送りました 🎉');
showToast('昇級申請は送付済みです 📬');

function showToast(msg) {
  var t = document.getElementById('toast');
  t.textContent = msg;
  t.classList.add('show');
  setTimeout(function() { t.classList.remove('show'); }, 2000);
}
```

---

## 10. 次の会話での引継ぎ指示

```
添付の仕様書（spec_doc v11.md）と引継書（handover_v11.md）をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ5は完了済みです。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
