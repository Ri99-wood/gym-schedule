# トレーニング予約調整システム

GitHub Pages + Firebase Firestore で動く無料のスケジュール調整 Web アプリです。

---

## セットアップ手順

### 1. Firebase プロジェクトを作成

1. [Firebase Console](https://console.firebase.google.com/) にアクセス
2. **「プロジェクトを追加」** → プロジェクト名を入力（例：gym-schedule）
3. 左メニュー **「Firestore Database」** → **「データベースの作成」**
4. **「テストモードで開始」** を選択 → ロケーションを選んで作成

### 2. Firebase 設定をコピー

1. プロジェクト設定（歯車アイコン）→ **「マイアプリ」** → **「＋アプリを追加」** → ウェブ `</>`
2. アプリのニックネームを入力 → **「アプリを登録」**
3. 表示される `firebaseConfig` の内容をコピー

### 3. index.html を書き換える

`index.html` の以下の部分を書き換えます：

```js
const FIREBASE_CONFIG = {
  apiKey: "実際のキーに変更",
  authDomain: "xxx.firebaseapp.com",
  projectId: "xxx",
  storageBucket: "xxx.appspot.com",
  messagingSenderId: "123456",
  appId: "1:123456:web:abc"
};
```

PIN を変更したい場合はその下の行を編集：

```js
const PINS = { client: '1234', trainer: '5678' };
```

### 4. GitHub Pages で公開

```bash
git add index.html
git commit -m "Add gym schedule app"
git push
```

1. GitHub リポジトリ → **Settings** → **Pages**
2. **Source** → `main` ブランチ、`/ (root)` を選択 → **Save**
3. 数分後に表示される URL がアプリの URL です

---

## 使い方

### クライアント（PIN: 1234）
- カレンダーの日付をタップ → 希望時間帯を追加
- トレーナーから代替提案が来たら **承諾** または **辞退**

### トレーナー（PIN: 5678）
- クライアントの希望を確認 → **確定する** or **代替を提案**
- 代替提案時は新しい日時とメモを入力

### ステータス
| 色 | 状態 |
|----|------|
| 🟣 紫 | 希望提出済み（未確定） |
| 🟢 緑 | 確定 |
| 🟠 橙 | トレーナーから代替提案あり |

---

## 特徴

- **リアルタイム同期** — トレーナーが操作するとクライアント画面に即反映
- **無料** — Firebase 無料枠で十分運用可能
- **スマホ対応** — モバイルファーストのレスポンシブ UI
