# IMAPメール運用 on Thunderbird

## POP？ IMAP？

### POP (Post Office Protocol)

> メールのコピーをパソコンに持ってきて見る方式

[参考](https://wa3.i-3-i.info/word12686.html)

### IMAP (Internet Message Access Protocol)

> メールサーバにお邪魔して、箱に入っているメールを直接見る方式

[参考](https://wa3.i-3-i.info/word1136.html)

### IMAP による同期

簡単な話、**IMAP 方式にすると手元の Thunderbird を Mailtap のクライアントとして使えるようになる**。すなわち、

+ 既読状態
+ フォルダ分類
+ フラグ（スター）
+ 削除

などを同期させられるようになる。

欠点としてはサーバー（≒Mailtap）を見に行っているだけなので、サーバー側の状態に依存してしまう。すなわち、**過去にさかのぼれるメールはサーバー容量の範囲内のみ**に限定されてしまう。

そこで、サーバーがいっぱいになってしまわないように古いメールを定期的にサーバーから削除しつつ、届いたメールを会社PC内に溜めていく（バックアップする）ようにする。

こうすれば過去のメールはそのバックアップ内で検索できるし、サーバー容量も圧迫しない（とはいえこの方法にも欠点があり、バックアップを作成しない自宅PCなどでは古いメールを見られなくなってしまう。これは自宅PCでもバックアップすれば解決するものの、容量を圧迫することになるのでそこはトレードオフ……）。

## 具体的な設定

+ POP 方式で運用していたものを IMAP に移行する場合で考える。
+ 基本的にどの設定も会社PCと自宅PCの両方で行う必要あり。

### IMAP 方式への移行

#### POP アカウントの停止

`サーバー設定` でチェックを外し、新規メールを自動で受信しないようにする。

![img](./attach/Pasted%20image%2020220511192955.png)

#### IMAP 専用アカウントの作成

`アカウント操作` からメールアカウントを追加。

![img](./attach/Pasted%20image%2020220511193125.png)

#### サーバーの手動設定

`既存のメールアドレスのセットアップ` の画面が出るので、

+ アカウント名
+ アドレス
+ パスワード

を入力したら左下の `手動設定` をクリック。下記を会社指定の内容で埋める。

+ ホスト名（＝サーバー名）
+ ポート番号
+ 接続の保護
+ 認証方式
+ ユーザー名（＝アドレス）


左下の `再テスト` をクリックして下記のような表示が出たらOK。

![img](./attach/Pasted%20image%2020220511193545.png)

これでひとまずメールの送受信は可能に。

#### 送信控え

メール送信時に自分を BCC に入れることで送信控えも受信メールと同じフォルダで管理できるようにする。

送信トレイを利用することもできるが、IMAP 方式だと送信トレイもサーバー容量を圧迫することになるので、受信メールと同じフォルダにおいてまとめて端末にバックアップするほうが吉。

![img](./attach/Pasted%20image%2020220512175943.png)

`送信控えと特別なフォルダ` のメニューから `次のメールアドレスを Bcc に追加する` のチェックボックスを有効化。

![img](./attach/Pasted%20image%2020220512180032.png)

#### サーバーから古いメールを削除する

![img](./attach/Pasted%20image%2020220512180324.png)

`同期とディスク領域` のメニューから何日以上古いメールを削除するか設定できる。ここでは仮に60日に。

![img](./attach/Pasted%20image%2020220512180343.png)

### メッセージフィルタの設定

これまでの内容で下記内容は設定できている（はず）。

+ IMAP 方式でメールを送受信する
+ 送信したメールの控えを自分宛てに Bcc で送信する
+ 古いメールをサーバーから自動的に削除する

あとは、受信したメールを端末に保存（バックアップ）して、自分宛ての Bcc は自動的に開封済みになるようにする。なお、**これらの設定は会社のPCでのみ行えばOK**。

#### メールのバックアップ

バックアップ先として、Thunderbird にデフォルトで用意されている「ローカルフォルダ」を使う。

左側のフォルダペイン下部の `ローカルフォルダ` を右クリックして `新しいフォルダー` を選択。ここではフォルダ名を `local_backup` として新規作成。

![img](./attach/Pasted%20image%2020220512181837.png)

IMAP で受信したメールをこのフォルダにバックアップにはメッセージフィルタ機能を使う。

![img](./attach/Pasted%20image%2020220512181954.png)

`ツール` の `メッセージフィルタ` から新規作成し、下図のように設定する。これで、新規にメールを受信するたびにその内容が `local_backup` にコピーされるようになる。なお、ここで `メッセージを移動する` にするとサーバー上からもメールが消えてしまうようなので要注意。

![img](./attach/Pasted%20image%2020220512182321.png)


#### 自分宛て Bcc を自動で開封する


同じくメッセージフィルタを使用する。

![img](./attach/Pasted%20image%2020220512182809.png)

`〓〓〓〓〓〓〓〓` の箇所には自分のメールアドレスを指定。これで自分宛てのメールは受信時に既読になる。

フィルタは上から順に実行されるので、ここで設定したフィルタを一番上にしておくと、まず既読になってからバックアップされるようになる。

------------------------------

2022年5月時点、ローカルフォルダに溜まっていくバックアップを自動で既読にする方法はない模様…。
（メッセージフィルタでローカルフォルダ上での定期実行を指定しても、実行対象のフォルダを指定することができず失敗してしまう）