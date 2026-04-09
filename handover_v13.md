# 引継書 — 飲食事業部 教育管理アプリ
作成日：2026年4月9日（v13）

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
- 昇級申請機能（grade_up_requests）を実装
- 部署管理機能を実装（departments テーブル・3階層表示・追加・削除）
- 役割を整理（manager_admin→全権管理者・dept_admin→部署管理者を追加）
- user_profiles に department_id カラムを追加
- dept_admin フィルター完全実装（manager_dashboard・admin 両画面）
- カテゴリ・テスト管理を部署×等級構造に再設計
- カテゴリの部署ごと独立管理に変更（ユニーク制約解除）
- カテゴリ複製機能の追加（admin / manager_admin のみ）
- トグル開閉タブの追加（カテゴリ管理・テスト管理）
- 項目追加モーダルにステップ同時追加機能を追加
- ダッシュボードのエリア・店舗行クリックでメンバー一覧展開
- boss → manager 全体リネーム（ファイル名・ロール値・コード）

### v13 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| スマホ対応 | member_top.html | `@media (max-width:640px)` でサイドバーを横スクロールタブに変換 |
| スマホ対応 | test_page.html | ボタン・余白をタッチ操作向けに最適化 |
| CSVエクスポート | admin.html | メンバー管理ページに「📥 CSVエクスポート」ボタンを追加 |
| メンバー一覧に部署列 | admin.html | 社員番号と店舗の間に「部署」列を追加・プルダウンで変更可 |
| updateDept() | admin.html | 部署プルダウン変更時に `user_profiles.department_id` を更新 |
| PDF出力 | member_detail.html | 「📄 PDFで出力」ボタン＋`@media print` で印刷用レイアウト |
| 組織管理トグル | admin.html | 事業部・エリアカードにトグル開閉を追加 |
| トグル状態維持 | admin.html | データ追加後の再描画でも開閉状態を復元 |
| 事業部カウント表示 | admin.html | 事業部ヘッダーに「Nエリア・N店舗」を表示 |
| 店舗移動機能 | admin.html | 店舗行に「移動」ボタン＋移動先エリア選択モーダルを追加 |
| テーブルデザイン修正 | admin.html | メンバー一覧を横スクロール対応・select幅を制限 |
| ダミーデータ削除 | Supabase | カテゴリ・テスト・エリア・店舗・メンバーをクリーンアップ |

---

## 2. 現在取り組み中の作業

**GitHub Pages での公開**

---

## 3. GitHub Pages 公開手順（次のステップ）

### 概要
GitHub Pages を使うと、HTMLファイルをそのままWebサイトとして無料で公開できる。
Supabase との接続も維持されるため、既存のコードはほぼそのまま使える。

### 手順
1. GitHub アカウントを作成（未作成の場合）
2. 新しいリポジトリを作成（例：`education-app`）
3. education-app/ フォルダ内のファイルをすべてアップロード
4. Settings → Pages → Branch を `main`・フォルダを `/ (root)` に設定
5. 公開URLが発行される（例：`https://ユーザー名.github.io/education-app/login.html`）

### 注意点
- Supabase の anon key・service role key がコードに含まれているが、GitHub Pages は静的ホスティングのため現状は許容範囲
- 将来的にセキュリティを強化する場合は環境変数化を検討
- `login.html` を起点にするため、トップページURLを共有する際は `/login.html` まで含めること

---

## 4. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成
├── member_top.html         ✅ 完成（ステップ式チェック・昇級申請・スマホ対応）
├── test_page.html          ✅ 完成（再試験機能・スマホ対応）
├── manager_dashboard.html  ✅ 完成（dept_adminフィルター・エリア/店舗展開）
├── member_detail.html      ✅ 完成（コメント・PDF出力）
├── admin.html              ✅ 完成（全管理機能・CSV出力・店舗移動・トグル）
├── spec_doc v13.md         📄 仕様書（最新）
└── handover_v13.md         📄 引継書（最新）
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
| user_profiles | メンバー情報・役割・等級・department_id（全ロールで使用） |
| check_progress | チェック状況（step_idで管理） |
| check_items | チェック項目 |
| check_steps | チェックステップ（聞いた・やってみた等） |
| test_results | テスト結果（4択・○×） |
| text_answers | 記述式回答・採点 |
| test_sets | 問題集（合格基準点） |
| test_questions | テスト問題（配点） |
| test_choices | 4択の選択肢（test_questions より先に削除すること） |
| comments | 上司コメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
| stores | 店舗（area_idでエリアに紐づく・移動機能あり） |
| categories | チェックカテゴリ（nameのユニーク制約なし・部署ごとに独立） |
| category_departments | カテゴリ×部署の中間テーブル |
| retest_permissions | 再試験許可 |
| grade_up_requests | 昇級申請 |

---

## 8. 重要な実装メモ

### 役割一覧
| role（DB値） | 表示名 | 権限 |
|---|---|---|
| member | メンバー | 自分の学習画面のみ |
| manager | 上司 | 全部署のダッシュボード閲覧 |
| admin | 管理者 | 全管理機能 |
| manager_admin | 全権管理者 | 全部署閲覧＋全管理機能 |
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理 |

### dept_adminのフィルター方針
```javascript
var currentRole      = null;  // ログインユーザーの役割
var currentDeptId    = null;  // dept_admin の department_id
var allowedMemberIds = null;  // null=全員 / 配列=自部署メンバーIDのみ
```

### メンバー一覧の部署プルダウン
```javascript
// loadMembers() 内で departments を取得して select を生成
var { data: depts } = await client.from('departments').select('id, name').order('id');

// updateDept() で department_id を更新
async function updateDept(userId, deptId) {
  var val = deptId ? parseInt(deptId) : null;
  await client.from('user_profiles').update({ department_id: val }).eq('id', userId);
}
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
  .print-header { display: flex; } /* 印刷専用ヘッダー */
  * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }
}
```

### 組織管理のトグル状態維持
```javascript
async function loadOrg() {
  // 再描画前に開いているIDを保存
  var openIds = [];
  list.querySelectorAll('[id^="org-dept-"], [id^="org-area-"]').forEach(function(el) {
    if (el.style.display !== 'none') openIds.push(el.id);
  });
  // ... 再描画 ...
  // 再描画後に復元
  openIds.forEach(function(id) {
    var el = document.getElementById(id);
    if (el) { el.style.display = ''; document.getElementById('arr-' + id)?.classList.remove('closed'); }
  });
}
```

### 店舗移動機能
```javascript
// stores.area_id を更新するだけでOK
async function moveStore() {
  var areaId = parseInt(document.getElementById('move-store-area').value);
  await client.from('stores').update({ area_id: areaId }).eq('id', moveStoreId);
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
添付の仕様書（spec_doc v13.md）と引継書（handover_v13.md）をもとに、
飲食事業部 教育管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
