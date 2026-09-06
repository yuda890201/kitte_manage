# kitte_manage

切手・収入印紙・葉書などの在庫管理と、不足分の請求書画像を生成するシングルページアプリ（`index.html` 単体）です。
データの保存先は Firebase（Firestore）です。

## Firebaseのセットアップ

1. [Firebase コンソール](https://console.firebase.google.com/)で新規プロジェクトを作成する。
2. 「Firestore Database」を作成する。
3. プロジェクトの設定 > 全般 > マイアプリ で「ウェブアプリを追加」し、表示された `firebaseConfig` の値をコピーする。
4. `index.html` 内の `firebaseConfig`(`YOUR_API_KEY` などのプレースホルダー)を、コピーした値に書き換える。
5. Authentication → Sign-in method で「メール/パスワード」を有効化する。
6. Authentication → Users → 「ユーザーを追加」で、以下の内容で1件だけ共通アカウントを登録する。
   - メールアドレス: `staff@kitte-manage.local`(`index.html` 内の `SHARED_LOGIN_EMAIL` と同じ値)
   - パスワード: 運用したいPINコード(Firebaseの制約で **6文字以上** が必要)
7. Firestore Security Rules(`firestore.rules`)は `main` ブランチへのpush時にGitHub Actionsで自動デプロイされます。デプロイには、リポジトリシークレット `FIREBASE_SERVICE_ACCOUNT`(サービスアカウントJSON)の登録、サービスアカウントへの `Service Usage Consumer` / `Firebase Rules Admin` ロールの付与、`.firebaserc` の `default` プロジェクトIDの設定が必要です。まだ設定できていない場合は、Firebase ConsoleのFirestore Database→ルールタブから `firestore.rules` の内容を直接貼り付けて公開してください。

## 認証について(重要)

以前は認証なし(`allow read, write: if true`)で、URLさえ分かれば誰でもデータを読み書きできる状態でした。現在はログイン画面(共通PINコード)を追加し、Firestoreルールも認証済みユーザーのみに制限しています。**上記5〜7の設定が完了するまでは、アプリにログインできません。**

なお、店舗ごとのデータはFirestore上で分離されておらず、全店舗分のデータが1つのドキュメントにまとまっています。そのため今回のPINは店舗ごとではなく、アプリ全体で共通の1つです。店舗ごとにアクセスを分離したい場合は、データ構造自体の見直し(店舗ごとのサブコレクション化)が別途必要です。

## 旧構成について

以前は Google Apps Script（スプレッドシート連携）をバックエンドにしていましたが、
Firebase（Firestore）への移行が完了したため廃止しました。
