# 引継書 — AZism 教育進捗管理
作成日：2026年4月17日（v19）

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

### フェーズ6（GitHub Pages 公開・継続）
- GitHub Pages 公開済み（静的ホスティング）
- ダミーデータをクリーンアップ（3名のみ残す）

### v15〜v17 で追加・修正した機能
（詳細は handover_v17.md を参照）

### v18 で追加・修正した機能
（詳細は handover_v18.md を参照）

### v19 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| カテゴリ並び順の統一 | member_top.html | メンバー画面のカテゴリ表示順を管理画面の sort_order 順に統一 |
| 進捗バー sticky 化 | member_top.html | 全体進捗率・現等級進捗率のバーをヘッダー直下に固定表示（スクロール追従） |
| カテゴリヘッダー sticky 化 | member_top.html | スクロール中、現在のカテゴリヘッダーが進捗バー下に貼り付き、次のカテゴリに到達すると自動切り替え |
| テストボタン判定修正 | member_top.html | 等級単位 → カテゴリ×等級単位に変更。該当テスト未作成なら非表示 |
| カテゴリ トグル開閉 | member_top.html | ▼/▶ でカテゴリを開閉可能。チェック後の再描画でも開閉状態を保持 |
| 項目 D&D をモーダル内に移設 | admin.html | 項目行の D&D を廃止し、カテゴリ編集モーダル内で並べ替え可能に変更 |
| 項目削除の不具合修正 | admin.html | FK制約を考慮し check_progress → check_steps → check_items の順に削除。adminClient 使用 |
| sort_order カラム追加 | check_items テーブル | 項目の並び順を永続保存する `sort_order int` カラムを追加 |

---

## 2. 現在取り組み中の作業

GitHub Pages での継続的な機能改善・運用

---

## 3. ⚠️ 次回作業前に必要なSupabase設定

**v19 で追加したSQLがまだ未実行の場合は、Supabase SQL Editor で以下を実行してください：**

```sql
-- v18 で追加（カテゴリ並べ替え用）
ALTER TABLE categories ADD COLUMN sort_order int DEFAULT null;

-- v19 で追加（項目並べ替え用）
ALTER TABLE check_items ADD COLUMN sort_order int DEFAULT null;
```

いずれも既に追加済みの場合は実行不要です。

---

## 4. GitHubへのアップロード手順

1. GitHub リポジトリページを開く
2. 更新したファイルを選択してアップロード（上書き）
3. 「Commit changes」をクリック
4. 数秒〜1分で GitHub Pages に反映される

**v19で変更したファイル（アップロード対象）：**
- `member_top.html`（カテゴリ sort_order 順・sticky 進捗バー・sticky カテゴリヘッダー・テスト判定修正・トグル開閉）
- `admin.html`（項目 D&D をモーダル内に移設・項目削除バグ修正）
- `spec_doc/spec_doc v19.md`（仕様書更新）
- `handover/handover_v19.md`（引継書更新）

---

## 5. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成（PWA対応・ロゴ追加）
├── member_top.html         ✅ 完成（sticky進捗バー・stickyカテゴリヘッダー・トグル開閉・sort_order順・v19〜）
├── test_page.html          ✅ 完成（スマホ対応・PWA対応）
├── manager_dashboard.html  ✅ 完成（2軸進捗・等級別タブ・全体進捗サマリー・PWA対応）
├── member_detail.html      ✅ 完成（実データ・2軸進捗・階層トグル・コメント・PDF出力）
├── admin.html              ✅ 完成（全管理機能・カテゴリD&D・項目D&Dモーダル・項目削除修正・v19〜）
├── manifest.json           📱 PWAマニフェスト
├── AZ-GROUP.jpg            🖼 旧ロゴ原画
├── AZ icon.png             🖼 新ロゴ原画（v18〜）
├── image/
│   ├── favicon.ico             🖼 ファビコン
│   ├── favicon.png             🖼 ファビコン（PNG）
│   ├── apple-touch-icon.png    🖼 iOS ホーム画面アイコン（180×180）
│   ├── icon-192.png            🖼 Androidアイコン（192×192）
│   ├── icon-512.png            🖼 Androidアイコン（512×512）
│   └── logo-white.png          🖼 ヘッダー用白ロゴ（透過PNG）
├── csv/
│   ├── csv_template_members.csv
│   ├── csv_template_categories.csv
│   ├── csv_template_stores.csv
│   └── csv_template_items.csv
├── spec_doc/
│   └── spec_doc v19.md     📄 仕様書（最新）
└── handover/
    └── handover_v19.md     📄 引継書（最新）
```

---

## 6. 開発環境

| 項目 | 内容 |
|------|------|
| エディタ | VSCode |
| ローカルサーバー | Live Server拡張（右下「Go Live」で起動） |
| ファイルの場所 | OneDrive/デスクトップ/education-app/ |
| ブラウザ確認URL | http://127.0.0.1:5500/ファイル名.html |
| AIコーディング | Cowork（Claude）にフォルダを接続して直接編集 |

---

## 7. Supabase情報

| 項目 | 値 |
|------|------|
| Project URL | `https://rkclfgpewmjdluwufjng.supabase.co` |
| anon key | コード内の `SUPABASE_ANON_KEY` に記載 |
| service role key | コード内の `SUPABASE_SERVICE_KEY` に記載（admin.htmlのみ） |
| SDKバージョン | `@2.39.3`（固定） |

---

## 8. データベース一覧

| テーブル名 | 内容 |
|-----------|------|
| user_profiles | メンバー情報・役割・等級・department_id（全ロールで必須） |
| check_progress | チェック状況（step_idで管理） |
| check_items | チェック項目（**sort_order カラム追加済み・v19〜**） |
| check_steps | チェックステップ（聞いた・やってみた等） |
| test_results | テスト結果（4択・○×） |
| text_answers | 記述式回答・採点 |
| test_sets | 問題集（合格基準点）← テストボタン表示判定に使用（**カテゴリ×等級で判定・v19〜**） |
| test_questions | テスト問題（配点） |
| test_choices | 4択の選択肢（test_questions より先に削除すること） |
| comments | MGコメント |
| departments | 部署 |
| areas | エリア（department_idで部署に紐づく） |
| stores | 店舗（area_idでエリアに紐づく・移動機能あり） |
| categories | チェックカテゴリ（sort_orderカラム追加済み・v18〜） |
| category_departments | カテゴリ×部署の中間テーブル |
| retest_permissions | 再試験許可 |
| grade_up_requests | 昇級申請 |

---

## 9. 重要な実装メモ

### 役割一覧（v19時点）
| role（DB値） | 表示名 | 権限 |
|---|---|---|
| member | メンバー | 自分の学習画面のみ |
| manager | MG | 全部署のダッシュボード閲覧 |
| manager_admin | 全権管理者 | 全部署閲覧＋全管理機能＋社員番号変更・PW変更・部署/エリア名編集 |
| dept_admin | 部署管理者 | 自部署のみ閲覧・管理（メンバー名前編集可） |

※ `admin` ロールは廃止済み（SQL: `UPDATE user_profiles SET role='manager_admin' WHERE role='admin'`）

### ログイン後の遷移先
```javascript
if (role === 'member') → member_top.html
if (role === 'manager' || role === 'dept_admin') → manager_dashboard.html
if (role === 'manager_admin') → admin.html
```

### メンバー画面の sticky 構造（v19〜）

**HTML構造**
```
<header> (position:sticky; top:0; z-index:10)
<div class="sticky-progress-wrap"> (position:sticky; top:56px; z-index:9)
  └ 全体進捗率・現等級進捗率のバーグラフ
<div class="layout">
  └ 各カテゴリ
      └ <div class="category-header"> (position:sticky; top:var(--cat-sticky-top); z-index:6)
      └ <div class="category-body">   (トグルで開閉)
```

**CSS変数の動的計算（JS）**
```javascript
function updateCatStickyTop() {
  var header = document.querySelector('header');
  var wrap   = document.querySelector('.sticky-progress-wrap');
  if (!header || !wrap) return;
  var total = header.offsetHeight + wrap.offsetHeight;
  document.documentElement.style.setProperty('--cat-sticky-top', total + 'px');
}
window.addEventListener('load',   updateCatStickyTop);
window.addEventListener('resize', updateCatStickyTop);
```

### メンバー画面のカテゴリ トグル開閉（v19〜）

```javascript
function toggleCategory(bodyId) {
  var body = document.getElementById(bodyId);
  var arr  = document.getElementById('arr-' + bodyId);
  if (!body) return;
  var isClosed = body.classList.toggle('closed');
  if (arr) arr.classList.toggle('closed', isClosed);
}
```

**閉じ状態の保持（silent 再描画時）**
```javascript
// 描画前に閉じていたカテゴリIDを記録
var closedBodyIds = [];
if (silent) {
  panelArea.querySelectorAll('.category-body.closed').forEach(function(el) {
    closedBodyIds.push(el.id);
  });
}
// 描画後に復元
closedBodyIds.forEach(function(id) {
  var el = document.getElementById(id);
  if (el) {
    el.classList.add('closed');
    var arr = document.getElementById('arr-' + id);
    if (arr) arr.classList.add('closed');
  }
});
```

**閉じ状態での角丸処理（CSS :has()）**
```css
.category:has(.category-body.closed) .category-header { border-radius: 12px; }
```

### メンバー画面のテストボタン表示判定（v19〜）
```javascript
// カテゴリ×等級ごとに test_sets から判定
var { data: testSets } = await client.from('test_sets').select('id, category_id, grade').eq('grade', grade);
var testCategoryIds = (testSets || []).map(function(t) { return t.category_id; });
// 各カテゴリ描画時:
var catHasTest = !isPast && testCategoryIds.indexOf(cat.id) !== -1;
```

### カテゴリ編集モーダル内の項目D&D（v19〜）

**モーダル HTML**
```html
<div class="modal-overlay" id="edit-category-modal">
  <div class="modal" style="max-width:560px">
    <h3>カテゴリを編集</h3>
    <!-- カテゴリ名入力 -->
    <!-- 項目の並び順（D&D） -->
    <div id="edit-category-items">...</div>
    <div class="modal-actions">保存 / キャンセル</div>
  </div>
</div>
```

**項目描画関数**
```javascript
async function renderEditCategoryItems(catId) {
  var { data: items } = await adminClient
    .from('check_items').select('id, name, grade, sort_order')
    .eq('category_id', catId);
  // sort_order 順でソート → 等級ごとにグループ化 → D&D 可能な div として描画
}
```

**D&D 関数（項目用）**
```javascript
var itemDragSrcEl = null;
function itemDragStart(e) { ... }
function itemDragOver(e)  { /* 同じ cat-id + grade のみ許可 */ }
function itemDrop(e)      { ... saveItemOrder(catId); }
```

**並び順保存**
```javascript
async function saveItemOrder(catId) {
  var rows = document.querySelectorAll('#edit-category-items .item-sortable[data-cat-id="' + catId + '"]');
  for (var i = 0; i < rows.length; i++) {
    await adminClient.from('check_items').update({ sort_order: i }).eq('id', ...);
  }
  await renderEditCategoryItems(catId); // モーダル内No.再描画
  loadContent(); // 背面も最新化
}
```

### 項目削除（FK制約対応・v19〜）
```javascript
async function deleteItem(itemId, name) {
  // 1. check_steps の ID を取得
  var { data: stepRows } = await adminClient.from('check_steps').select('id').eq('item_id', itemId);
  var stepIds = (stepRows || []).map(function(s) { return s.id; });
  // 2. check_progress を削除（step_id 参照分）
  if (stepIds.length > 0) {
    await adminClient.from('check_progress').delete().in('step_id', stepIds);
  }
  // 3. check_steps を削除
  await adminClient.from('check_steps').delete().eq('item_id', itemId);
  // 4. check_items を削除
  await adminClient.from('check_items').delete().eq('id', itemId);
}
```

### カテゴリ ドラッグ＆ドロップ並べ替え（v18〜）

```javascript
// sort_order でソート（null は末尾・同値は id 昇順）
deptCats.sort(function(a, b) {
  var sa = a.sort_order != null ? a.sort_order : 999999;
  var sb = b.sort_order != null ? b.sort_order : 999999;
  return sa !== sb ? sa - sb : a.id - b.id;
});
```
※カテゴリ用D&D関数（catDragStart / catDragOver / catDrop 等）は v18 引継書を参照

### メンバートップの2軸進捗（v17〜）

```javascript
async function loadOverallProgress() {
  var { data: allItems } = await client.from('check_items').select('id');
  var { data: allSteps } = await client.from('check_steps').select('id, item_id');
  var { data: prog }     = await client.from('check_progress').select('step_id')
    .eq('user_id', CURRENT_USER).not('step_id', 'is', null);
  var allItemIds = (allItems || []).map(function(i) { return i.id; });
  var stepIds    = (allSteps || []).filter(function(s) { return allItemIds.indexOf(s.item_id) !== -1; }).map(function(s) { return s.id; });
  var checkedAll = (prog || []).filter(function(p) { return stepIds.indexOf(p.step_id) !== -1; }).length;
  var rate = stepIds.length > 0 ? Math.round(checkedAll / stepIds.length * 100) : 0;
  document.getElementById('progress-rate-all').textContent = rate + '%';
  document.getElementById('progress-bar-all').style.width  = rate + '%';
}
```

### メンバー詳細の2軸進捗計算（v17〜）

```javascript
// 全体進捗率（0等級〜現等級のすべてのステップ）
var memberItemIds = allItems.filter(function(i) { return i.grade <= member.grade; }).map(function(i) { return i.id; });
var memberStepIds = allSteps.filter(function(s) { return memberItemIds.indexOf(s.item_id) !== -1; }).map(function(s) { return s.id; });
var overallProgress = memberStepIds.length > 0
  ? Math.round(memberStepIds.filter(function(id) { return checkedStepIds.indexOf(id) !== -1; }).length / memberStepIds.length * 100)
  : 0;

// 現等級進捗率
var curItemIds = allItems.filter(function(i) { return i.grade === member.grade; }).map(function(i) { return i.id; });
var curStepIds = allSteps.filter(function(s) { return curItemIds.indexOf(s.item_id) !== -1; }).map(function(s) { return s.id; });
var curProgress = curStepIds.length > 0
  ? Math.round(curStepIds.filter(function(id) { return checkedStepIds.indexOf(id) !== -1; }).length / curStepIds.length * 100)
  : 0;
```

### メンバー詳細の階層トグル構造（v17〜）
- 等級ブロック：`id="grade-body-{g}"`、矢印：`id="arr-grade-body-{g}"`
- カテゴリブロック：`id="cat-body-{g}-{catId}"`、矢印：`id="arr-cat-body-{g}-{catId}"`
- `toggleSection(bodyId)` 関数で `display: none ↔ block` 切り替え＋矢印クラス `.closed` 制御

### ヘッダータイトルで更新（v17〜）
```html
<span class="app-name" onclick="location.reload()" style="display:flex;align-items:center;gap:8px;">
  <img src="image/logo-white.png" ...>AZism 教育進捗管理
</span>
```

### dept_adminのフィルター差異（重要）
```javascript
// ダッシュボード：store経由（role=member のみ）
// 管理画面：department_id で直接（member/manager/dept_admin）
var { data: deptMembers } = await client
  .from('user_profiles').select('id')
  .eq('department_id', currentDeptId)
  .in('role', ['member', 'manager', 'dept_admin']);
```

### CSVインポートの文字コード自動判別
```javascript
reader.readAsArrayBuffer(file);
reader.onload = function(e) {
  var buffer = e.target.result;
  var bytes  = new Uint8Array(buffer);
  var text;
  if (bytes[0]===0xEF && bytes[1]===0xBB && bytes[2]===0xBF) {
    text = new TextDecoder('utf-8').decode(buffer).slice(1);
  } else if (bytes[0]===0xFF && bytes[1]===0xFE) {
    text = new TextDecoder('utf-16le').decode(buffer);
  } else {
    try { text = new TextDecoder('shift-jis').decode(buffer); }
    catch(e) { text = new TextDecoder('utf-8').decode(buffer); }
  }
};
```

### カテゴリ追加の実装ポイント（RLS対応）
```javascript
var { data: catData } = await client
  .from('categories').insert({ name: name, department_id: deptId }).select().limit(1);
var catId = (catData && catData.length > 0) ? catData[0].id : null;
if (!catId) {
  var { data: fetched } = await client
    .from('categories').select('id').eq('name', name).eq('department_id', deptId)
    .order('id', { ascending: false }).limit(1);
  catId = fetched && fetched[0] ? fetched[0].id : null;
}
```

### アポストロフィ対策
onclick属性内の名前文字列には `.replace(/'/g,"&#39;")` を適用すること。

### 社員番号変更・PW変更
```javascript
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });
await adminClient.auth.admin.updateUserById(userId, { password: newPw });
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

## 10. 次の会話での引継ぎ指示

```
添付の仕様書（spec_doc v19.md）と引継書（handover_v19.md）をもとに、
AZism 教育進捗管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
