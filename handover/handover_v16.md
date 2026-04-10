# 引継書 — AZism 教育進捗管理
作成日：2026年4月10日（v16）

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

### v15 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| メンバー一覧PC表示修正 | admin.html | `table-layout: fixed` で横スクロールなし（スマホは維持） |
| メンバー追加の部署必須化 | admin.html | 全ロールで部署選択が必須。部署管理者は自部署を自動セット |
| 管理画面ボタンデザイン変更 | manager_dashboard.html | ⚙️アイコン付きボタンに変更（昇級申請ボタンと同スタイル） |
| dept_adminフィルター修正 | admin.html | 管理画面は `department_id` で直接フィルタ（MG/dept_adminも表示対象に） |
| カテゴリ追加バグ修正 | admin.html | `department_id` をINSERTに含める＋RLS対応のフォールバックSELECT |
| カテゴリ表示構造変更 | admin.html | 「部署→等級→カテゴリ」→「部署→カテゴリ→等級」に変更。空カテゴリも表示 |
| 項目No.表示 | admin.html | 項目名の前にIDベースの昇順No.を表示 |
| 項目名編集 | admin.html | 鉛筆アイコン「編集」ボタンからモーダルで項目名を変更 |
| 項目複製 | admin.html | 「複製」ボタンで項目＋全ステップをコピー（名前に「（コピー）」付加） |
| メンバー名前編集 | admin.html | 全メンバー行に「名前編集」ボタン。dept_admin・manager_admin が使用可 |
| カテゴリ名編集 | admin.html | カテゴリカードヘッダーの「編集」ボタンでカテゴリ名を変更 |
| トグル状態保持 | admin.html | 項目追加・編集後もカテゴリ管理の折りたたみ状態を維持 |
| 項目テーブルスクロール対応 | admin.html | 項目名が長い時に横スクロール可能（`overflow-x:auto` + `min-width:560px`） |
| 事業部名編集 | admin.html | 部署エリア店舗管理で事業部名を編集（全権管理者のみ） |
| エリア名編集 | admin.html | 部署エリア店舗管理でエリア名を編集（全権管理者のみ） |

### v16 で追加・修正した機能

| 機能 | 対象ファイル | 概要 |
|------|-------------|------|
| ログアウトボタンサイズ統一 | admin.html / manager_dashboard.html | 管理画面と同じサイズに統一（`padding: 7px 16px; font-size: 13px`） |
| 部署/エリア/店舗ヘッダー編集ボタン整列 | admin.html | 右端に flex 配置で縦位置ズレ解消 |
| チェックボックス白フラッシュ解消 | member_top.html | `loadContent(grade, silent=true)` で再描画時にチラつき防止 |
| メンバー編集UIを統合 | admin.html | 編集ボタン4つ→2つ（編集/削除）、名前・社員番号・PW変更を1モーダルに統合 |
| CSVメンバー一括追加 | admin.html | テンプレートCSV読み込みでメンバーを一括追加（Shift-JIS/UTF-8自動判別） |
| CSVカテゴリ一括追加 | admin.html | カテゴリページにCSV一括追加ボタン（テンプレートあり） |
| CSV店舗一括追加 | admin.html | 部署エリア店舗管理画面にCSV一括追加ボタン（テンプレートあり） |
| CSV項目一括追加 | admin.html | 各カテゴリカードにCSV一括追加ボタン（テンプレートあり） |
| CSVロール正規化 | admin.html | `toLowerCase().replace(/\s+/g,'_')` でロール値の表記揺れに対応 |
| ボタン位置移動（カテゴリ・テスト・組織） | admin.html | 各ページの追加ボタンをリストより上に配置 |
| CSV文字コード対応 | admin.html | `readAsArrayBuffer` + BOM検出でShift-JIS（Excel保存）を正しく読み込み |
| 2軸進捗バー | manager_dashboard.html | 個人別・エリア別・店舗別・等級別すべてに全体進捗率＋現等級進捗率の2本バー表示 |
| 全体進捗率の追加 | manager_dashboard.html | チェック済みステップ÷部署全ステップ×100 を `progressAll` として計算 |
| サマリーカード拡張 | manager_dashboard.html | 5列グリッドに変更し「平均進捗率（全体）」カードを追加 |
| 進捗率セクション改名 | manager_dashboard.html | 「メンバー一覧」→「進捗率」に変更 |
| 等級別タブ追加 | manager_dashboard.html | エリア別・店舗別・**等級別**・個人別の4タブ構成に。等級別は0〜3等級を展開表示 |
| 部署列の幅調整 | admin.html | メンバー一覧の部署列を13%→17%に拡大、選択タブとの重なりを解消 |
| テストボタン非表示 | member_top.html | `test_sets` に対象等級のテストがない場合はボタンを表示しない |
| PWA対応 | 全ページ | `manifest.json` 追加・ホーム画面追加対応・スタンドアロン表示 |
| ファビコン・アイコン設定 | 全ページ | AZ-GROUP.jpg から `favicon.png`/`icon-192.png`/`icon-512.png`/`apple-touch-icon.png` を生成 |
| ヘッダーにロゴ追加 | 全ページ | `logo-white.png`（白反転）を「AZism 教育進捗管理」テキスト左に配置 |

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
- `member_top.html`
- `manifest.json`（新規）
- `favicon.ico`（新規）
- `favicon.png`（新規）
- `apple-touch-icon.png`（新規）
- `icon-192.png`（新規）
- `icon-512.png`（新規）
- `logo-white.png`（新規）
- `AZ-GROUP.jpg`（新規）
- `csv_template_members.csv`（新規）
- `csv_template_categories.csv`（新規）
- `csv_template_stores.csv`（新規）
- `csv_template_items.csv`（新規）
- `spec_doc v16.md`（仕様書更新）
- `handover_v16.md`（引継書更新）

---

## 4. 現在のファイル構成

```
education-app/
├── login.html              ✅ 完成（PWA対応・ロゴ追加）
├── member_top.html         ✅ 完成（テストボタン制御・白フラッシュ解消・PWA対応）
├── test_page.html          ✅ 完成（スマホ対応・PWA対応）
├── manager_dashboard.html  ✅ 完成（2軸進捗・等級別タブ・全体進捗サマリー・PWA対応）
├── member_detail.html      ✅ 完成（コメント・PDF出力・PWA対応）
├── admin.html              ✅ 完成（全管理機能・CSV一括追加・編集UI統合・PWA対応）
├── manifest.json           📱 PWAマニフェスト（新規）
├── AZ-GROUP.jpg            🖼 ロゴ原画
├── favicon.ico             🖼 ファビコン
├── favicon.png             🖼 ファビコン（PNG）
├── apple-touch-icon.png    🖼 iOS ホーム画面アイコン（180×180）
├── icon-192.png            🖼 Androidアイコン（192×192）
├── icon-512.png            🖼 Androidアイコン（512×512）
├── logo-white.png          🖼 ヘッダー用白ロゴ（透過PNG）
├── csv_template_members.csv    📋 CSVテンプレート：メンバー
├── csv_template_categories.csv 📋 CSVテンプレート：カテゴリ
├── csv_template_stores.csv     📋 CSVテンプレート：店舗
├── csv_template_items.csv      📋 CSVテンプレート：項目
├── spec_doc v16.md         📄 仕様書（最新）
└── handover_v16.md         📄 引継書（最新）
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
| test_sets | 問題集（合格基準点）← テストボタン表示判定に使用 |
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

### 役割一覧（v16時点）
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
if (role === 'manager_admin') → admin.html  // ← 直接管理画面へ
```

### dept_adminのフィルター差異（重要）
```javascript
// ダッシュボード：store経由（role=member のみ）
var membersQuery = client.from('user_profiles').select(...).eq('role', 'member');
// → 店舗→エリア→部署の階層でフィルタリング

// 管理画面：department_id で直接（member/manager/dept_admin）
var { data: deptMembers } = await client
  .from('user_profiles').select('id')
  .eq('department_id', currentDeptId)
  .in('role', ['member', 'manager', 'dept_admin']);
allowedMemberIds = (deptMembers || []).map(function(m) { return m.id; });
```

### 進捗率の2軸計算（v16〜）

```javascript
// ── 現等級進捗率 ──
var gradeItems   = items.filter(i => i.grade === m.grade);
var gradeStepIds = steps.filter(s => gradeItems.some(i => i.id === s.item_id)).map(s => s.id);
var checkedGrade = checks.filter(c => c.user_id === m.id && gradeStepIds.includes(c.step_id)).length;
var progress = gradeStepIds.length > 0 ? Math.round(checkedGrade / gradeStepIds.length * 100) : 0;

// ── 全体進捗率（部署全カテゴリ・全等級） ──
var deptCatIds    = catDepts.filter(cd => cd.department_id === m.department_id).map(cd => cd.category_id);
var allDeptItems  = items.filter(i => deptCatIds.includes(i.category_id));
var allDeptStepIds = steps.filter(s => allDeptItems.some(i => i.id === s.item_id)).map(s => s.id);
var checkedAll    = checks.filter(c => c.user_id === m.id && allDeptStepIds.includes(c.step_id)).length;
var progressAll   = allDeptStepIds.length > 0 ? Math.round(checkedAll / allDeptStepIds.length * 100) : 0;
```

### dualProgressCell（2軸バーHTML生成）
```javascript
function dualProgressCell(progressAll, progressGrade) {
  return '<div class="dual-progress">' +
    '<div class="dual-progress-row">' +
      '<span class="dual-label">全体</span>' +
      '<div class="mini-bar-bg"><div class="mini-bar-fill mini-bar-fill-gray" style="width:' + progressAll + '%"></div></div>' +
      '<span class="progress-num">' + progressAll + '%</span>' +
    '</div>' +
    '<div class="dual-progress-row">' +
      '<span class="dual-label">現等級</span>' +
      '<div class="mini-bar-bg"><div class="mini-bar-fill" style="width:' + progressGrade + '%"></div></div>' +
      '<span class="progress-num">' + progressGrade + '%</span>' +
    '</div>' +
  '</div>';
}
```

### テストボタン表示制御（v16〜）
```javascript
// loadContent() 内で test_sets を確認
var { data: testSets } = await client.from('test_sets').select('id').eq('grade', grade).limit(1);
var hasTest = !isPast && testSets && testSets.length > 0;

// テストボタンの表示
if (hasTest) {
  html += '<button class="test-btn" onclick="window.location.href=\'test_page.html\'">テストを受ける</button>';
}
```

### CSVインポートの実装ポイント

**文字コード自動判別（Shift-JIS対応）**
```javascript
reader.readAsArrayBuffer(file);
reader.onload = function(e) {
  var buffer = e.target.result;
  var bytes  = new Uint8Array(buffer);
  var text;
  if (bytes[0]===0xEF && bytes[1]===0xBB && bytes[2]===0xBF) {
    text = new TextDecoder('utf-8').decode(buffer).slice(1); // UTF-8 BOM
  } else if (bytes[0]===0xFF && bytes[1]===0xFE) {
    text = new TextDecoder('utf-16le').decode(buffer);       // UTF-16 LE
  } else {
    try { text = new TextDecoder('shift-jis').decode(buffer); }
    catch(e) { text = new TextDecoder('utf-8').decode(buffer); }
  }
};
```

**ロール正規化（表記揺れ対応）**
```javascript
var rawRole = (r[3]||'').trim();
var role = rawRole.toLowerCase().replace(/\s+/g, '_') || 'member';
var validRoles = ['member', 'manager', 'dept_admin'];
if (!validRoles.includes(role)) { errors.push(...); continue; }
```

### カテゴリ追加の実装ポイント（RLS対応）
```javascript
// department_id を必ず渡す（NOT NULL制約対応）
var { data: catData } = await client
  .from('categories').insert({ name: name, department_id: deptId }).select().limit(1);

// RLS でSELECTが空になる場合のフォールバック
var catId = (catData && catData.length > 0) ? catData[0].id : null;
if (!catId) {
  var { data: fetched } = await client
    .from('categories').select('id').eq('name', name).eq('department_id', deptId)
    .order('id', { ascending: false }).limit(1);
  catId = fetched && fetched[0] ? fetched[0].id : null;
}
```

### トグル状態保持
```javascript
var closedIds = [];
list.querySelectorAll('[id^="content-dept-"], [id^="content-cat-"]').forEach(function(el) {
  if (el.style.display === 'none') closedIds.push(el.id);
});
// 再描画後
closedIds.forEach(function(id) {
  var el = document.getElementById(id);
  if (el) {
    el.style.display = 'none';
    var arr = document.getElementById('arr-' + id);
    if (arr) arr.classList.add('closed');
  }
});
```

### PWA設定（v16〜）
- `manifest.json` を全ページに `<link rel="manifest" href="manifest.json">` で読み込み
- iOSホーム画面追加には `<meta name="apple-mobile-web-app-capable" content="yes">` が必要
- `start_url` は `./member_top.html`（メンバー画面をメインターゲットとして設定）
- ヘッダーロゴは `logo-white.png` に `filter: brightness(0) invert(1)` で白く表示

### アポストロフィ対策
onclick属性内の名前文字列には `.replace(/'/g,"&#39;")` を適用すること。

### 社員番号変更・PW変更
```javascript
// 社員番号変更：user_profiles と auth email を両方更新
await client.from('user_profiles').update({ employee_id: newEid }).eq('id', userId);
await adminClient.auth.admin.updateUserById(userId, { email: newEid + '@education-app.com' });

// パスワードリセット
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

## 9. 次の会話での引継ぎ指示

```
添付の仕様書（spec_doc v16.md）と引継書（handover_v16.md）をもとに、
AZism 教育進捗管理アプリの開発を再開します。

次に取り組みたいことを伝えますので、
仕様書・引継書の内容を把握したうえで対応してください。
```
