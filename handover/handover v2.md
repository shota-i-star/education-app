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
| 機能 | 画面 | 状態 |
|------|------|------|
| チェックボックスの保存・読み込み | member_top.html | ✅ |
| 進捗率のリアルタイム計算 | member_top.html | ✅ |
| テストの自動採点（4択・○×） | test_page.html | ✅ |
| 選択肢の回答を保存・復元 | test_page.html | ✅ |
| 記述式テストの提出・保存 | test_page.html | ✅ |
| 管理者による記述式採点 | admin.html | ✅ |
| 記述式点数の合計への加算 | test_page.html | ✅ |

---

## 2. 現在のファイル構成

```
デスクトップ/education-app/
├── member_top.html   ✅ Supabase連携済み
├── test_page.html    ✅ Supabase連携済み
├── admin.html        ✅ 記述式採点機能連携済み
├── login.html        見た目のみ
├── boss_dashboard.html  見た目のみ
└── member_detail.html   見た目のみ
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
  auth: { persistSession: false }
});
```

---

## 5. 重要な実装メモ

### CURRENT_USERについて
現在 `'yamada_taro'` で固定。フェーズ3のログイン実装時に置き換える。

### テストの正解設定
`test_page.html` 内の `TEST_CONFIG` で管理。
```javascript
var TEST_CONFIG = {
  pass_score: 80,
  q1_correct: 1,        // 0始まりの選択肢番号
  q2_correct: 'maru'    // 'maru'=○ / 'batu'=×
};
```

### 点数の計算方法
```
合計点 = test_results.score + text_answers.score
```
記述式が採点待ちの場合は「〇点（記述式採点待ち）」と表示。

---

## 6. 次にやること（フェーズ3）

### フェーズ3の目標
Supabase Authを使ってログイン機能を実装し、役割に応じて画面を切り替える。

### 具体的にやること
- [ ] Supabase Authでメンバーアカウントを作成
- [ ] ログイン画面を実際に動かす
- [ ] ログイン後に役割（メンバー／上司／管理者）を判定する
- [ ] 役割に応じて表示画面を切り替える
- [ ] `CURRENT_USER` を実際のログインユーザーに置き換える

---

## 7. 次の会話での引継ぎ指示

```
添付の仕様書と引継書をもとに、
飲食事業部 教育管理アプリの開発を再開します。
フェーズ2が完了しており、フェーズ3（ログイン機能）を進めます。
まずフェーズ3の進め方を説明してください。
```

---

## 8. 積み残し・未決定事項

| 項目 | 内容 |
|------|------|
| テストの再受験ルール | 未定 |
| メンバーへのコメント通知 | 未定 |
| 公開範囲 | 社内限定 or インターネット公開 |
| GitHubでのファイル管理 | フェーズ3前に導入を検討 |
