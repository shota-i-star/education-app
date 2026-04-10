# 引継書 — AZism 教育進捗管理
作成日：2026年4月9日（v14）

---

## ▼ 引継書の使い方
新しい会話を始めるときは、この引継書と仕様書を**両方**冒頭に添付してから作業を開始してください。

---

## 1. 完了したこと

### フェーズ1〜5（完了）
- 全6画面の作成・Supabase連携・ログイン機能・集計・グラフ
- 管理画面の全機能・コメント機能・ステップ式チェック・再試験・昇級申請
- 部署管理機能（3階層表示）・dept_admin フィルター・カテゴリ複製
- スマホ対応（member_top / test_page）

### フェーズ6（GitHub Pages 公開・進行中）
- GitHub Pages 公開済み（静的ホスティング）
- ダミーデータをクリーンアップ（3名のみ残す）

### v14 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| アプリ名変更 | 全ファイル | 「飲食事業部 教育管理アプリ」→「AZism 教育進捗管理」 |
| ロール表記変更 | admin / dashboard / detail | 「上司」→「MG」 |
| adminロール廃止 | admin / login / dashboard | `admin` を削除、`manager_admin` に統合（SQL移行済み） |
| ロール付与制限 | admin.html | `manager_admin` ロールは全権管理者のみ付与可能 |
| 管理画面リンクバグ修正 | manager_dashboard.html | manager_admin がダッシュボードから管理画面に行けないバグを修正 |
| 社員番号変更 | admin.html | 全権管理者のみ：user_profiles + auth email を両方更新 |
| パスワードリセット | admin.html | 全権管理者のみ：adminClient で任意パスワードに変更 |
| メンバー一覧PC表示修正 | admin.html | table-layout: fixed で横スクロールなし（スマホは維持） |
| メンバー追加の部署必須化 | admin.html | 全ロールで部署選択が必須。部署管理者は自部署を自動セット |
| 管理画面ボタンデザイン変更 | manager_dashboard.html | ⚙️アイコン付きボタンに変更（昇級申請ボタンと同スタイル） |
| dept_adminフィルター修正 | admin.html | 管理画面は `department_id` で直接フィルタ（MGも表示対象に） |

---

## 2. 現在取り組み中の作業

GitHub Pages での継続的な機能改善・運用

---

## 3. GitHubへのアップロード手順

1. GitHub リポジトリページを開く
2. 更新したファイルを選択してアップロード（上書き）
3. 「Commit changes」をクリック
4. 数秒〜1分で GitHub Pages に反映される

**今日変更したファイル（アップロード対象）：**
- `admin.html`
- `manager_dashboard.html`

---

## 4. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成
├── member_top.html         ✅ 完成（ステップ式チェック・昇級申請・スマホ対応）
├── test_page.html          ✅ 完成（再試験機能・スマホ対応）
├── manager_dashboard.html  ✅ 完成（dept_adminフィルター・管理画面ボタン・エリア/店舗展開）
├── member_detail.html      ✅ 完成（コメント・PDF出力）
├── admin.html              ✅ 完成（全管理機能・CSV・社員番号変更・PW変更・部署必須）
├── spec_doc v14.md         📄 仕様書（最新）
└── handover_v14.md         📄 引継書（最新）
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
| user_profiles | メンバー情報・役割・等級・department_id（全ロールで必須） |
| check_progress | チェック状況（step_idで管理） |
| check_items | チェック項目 |
| check_steps | チェックステップ（聞いた・やってみた等） |
| test_results | テスト結果（4択・○×） |
| text_answers | 記述式回答・採点 |
| test_sets | 問題集（合格基準点） |
| test_questions | テスト問題（配点） |
| test_choices | 4択の選択肢（test_questions より先に削除すること） |
| comments | MGコメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
| stores | 店舗（area_idでエリアに紐づく・移動機能あり） |
| categories | チェックカテゴリ（nameのユニーク制約なし・部署ごとに独立） |
| category_departments | カテゴリ×部署の中間テーブル |
| retest_permissions | 再試験許可 |
| grade_up_requests | 昇級申請 |

---

## 8. 重要な実装メモ

### 役割一覧（v14時点）
| role（DB値） | 表示名 | 権限 |
|---|---|---|
| member | メンバー | 自分の学習画面のみ |
| manager | MG | 全部署のダッシュボード閲覧 |
| manager_admin | 全権管理者 | 全部署閲覧＋全管理機能＋社員番号変更・PW変更 |
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理 |

※ `admin` ロールは廃止済み（SQL: `UPDATE user_profiles SET role='manager_admin' WHERE role='admin'`）

### ログイン後の遷移先
```javascript
if (role === 'member') → member_top.html
if (role === 'manager' || role === 'dept_admin') → manager_dashboard.html
if (role === 'manager_admin') → admin.html  // ← 直接管理画面へ
```

### dept_adminのフィルター差異
```javascript
// ダッシュボード：store経由（role=member のみ）
var membersQuery = client.from('user_profiles').select(...).eq('role', 'member');

// 管理画面：department_id で直接（member/manager/dept_admin）
var { data: deptMembers } = await client
  .from('user_profiles').select('id')
  .eq('department_id', currentDeptId)
  .in('role', ['member', 'manager', 'dept_admin']);
```

### メンバー追加モーダルの部署設定
```javascript
async function openAddModal() {
  var { data: depts } = await client.from('departments').select('id, name').order('id');
  var deptSelect = document.getElementById('add-dept-id');

  if (currentRole === 'manager_admin') {
    // 全権管理者：全部署から選択（必須）
    deptSelect.innerHTML = '<option value="">選択してください</option>' + ...;
    deptSelect.disabled = false;
  } else if (currentRole === 'dept_admin') {
    // 部署管理者：自部署を固定
    deptSelect.innerHTML = '<option value="' + myDept.id + '">' + myDept.name + '</option>';
    deptSelect.disabled = true;
  }
}
// バリデーション
var deptId = parseInt(document.getElementById('add-dept-id').value) || null;
if (!deptId) { alert('所属部署を選択してください'); return; }
```

### 社員番号変更・PW変更
```javascript
// 社員番号変更：user_profiles と auth email を両方更新
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });

// パスワードリセット
await adminClient.auth.admin.updateUserById(userId, { password: newPw });
```

### CSVエクスポートのポイント
```javascript
// BOM付きでExcel日本語対応
var blob = new Blob([csvRows.join('\r\n')], { type: 'text/csv;charset=utf-8;' });
// 列順：名前, 社員番号, 部署, 等級, 店舗, エリア, 進捗率(%), テスト点数, 合否
```

### PDF出力（@media print）
```css
@media print {
  header, .breadcrumb, .btn-pdf, .comment-form { display: none; }
  .print-header { display: flex; }
  * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
}
```

### データ削除の安全な順序（FK制約）
```sql
DELETE FROM text_answers;
DELETE FROM test_results;
DELETE FROM check_progress;
DELETE FROM comments;
DELETE FROM retest_permissions;
DELETE FROM grade_up_requests;
DELETE FROM test_choices;    -- ← test_questions より必ず先
DELETE FROM test_questions;
DELETE FROM test_sets;
DELETE FROM check_steps;
DELETE FROM check_items;
DELETE FROM category_departments;
DELETE FROM categories;
DELETE FROM stores;
DELETE FROM areas;
DELETE FROM user_profiles WHERE name NOT IN ('残すユーザー名');
```

---

## 9. 次の会話での引継ぎ指示

```
添付の仕様書（spec_doc v14.md）と引継書（handover_v14.md）をもとに、
AZism 教育進捗管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
