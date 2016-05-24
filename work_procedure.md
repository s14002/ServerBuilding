#作業手順

## Section0 講義の前のセットアップ
### VirtualBoxのインストール
* VirtualBox5.0をインストールした

### Vagrantのインストール
* 先生の所にあるリンクが切れてたのでググって [Vagrantの公式ダウンロードサイト](https://www.vagrantup.com/downloads.html)  からDEBIANの64bit版をダウンロードした
* vagrant1.8.1をインストールした

## Section1 基本のサーバー構築
### CentOS7のインストール
#### VirtualBoxへのインストール
* 先生のUSBからCentOS 7 Minimal(x86_64)のISOファイルをダウンロードし、VirtualBox上にインストールした。
* VirtualBoxで作成する仮想マシンのメモリのサイズは1GBにする。ストレージの容量は8GB程度に設定する。
ネットワークアダプター2を設定します。割り当てを「ホストオンリーアダプター」にします。 (ネットワークアダプター1はデフォルト(NAT)で問題ありません)
* インストール中に指示されるパーティションの設定は特に指定しません。

* インストール中、root以外の作業用(管理者)のユーザーを作成。

#### ネットワークアダプター1/2へのIPアドレスの設定とssh接続の確認
* /etc/sysconfig/network-scriptにifcfg-enp0s?というファイルがあるので、 そのファイルを編集してネットワーク接続ができるように設定します。

`$sudo vi /etc/sysconfig/network-script/ifcfg-enp0s3`

で中身のONBOOT=noのnoをyesに変えた。

`$sudo vi /etc/sysconfig/network-script/ifcfg-enp0s8`

で中身のONBOOT=noのnoをyesに変えた。

`$sudo /etc/sysconfig/network-script/ifup enp0s3`

`$sudo /etc/sysconfig/network-script/ifup enp0s8`

`$ip addr`

でIPアドレスを確認してメモ。

#### SSH接続の確認
* Ubuntuからsshで仮想マシンに接続

`$ssh 学籍番号@メモしたIPアドレス`

####インストール後の設定
* dconf-editerでProxyを設定
* /etc/yum.confにProxy=http://IPアドレス:8888を追加

`$sudo vi /etc/yum.conf`

####アップデート

`$sudo yum update`

`$sudo yum install wget`

### Wordpressを動かす(1)

Wordpressを動作させるためには下記のソフトウェアが必要になります。

* Apache HTTP Server
* MySQL
* PHP

* Apache HTTP Serverのインストール

`$sudo yum install httpd`

* MySQLのインストール
MariaDBがあったら消す

`$yum list installed | grep mariai`

あった場合アンインストール

`$yum -y remove mariadb-libs`

* MySQL公式リポジトリファイルをインストール

`$yum -y install http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm`

* MySQLインストール

`$yum -y install mysql`

`$yum -y install mysql-devel`

`$yum -y install mysql-server`

`$yum -y install mysql-utilities`

* mysqlデーモンの起動

`$service mysqld start`

* DB初期設定

`$/usr/bin/mysql_secure_installation`

* PHPのインストール

`$yum install php`

`$yum install php-mbstring`

* Wordpressのインストール

* やり方その1
[wordpressの公式サイト](https://wordpress.org/)からUbuntuに入れて展開したディレクトリをscpコマンドで仮想マシンに移動する。

`$scp -r コピー元のディレクトリ ホームディレクトリ@IPアドレス(またはホスト名):~/(コピー先ディレクトリ)`

* やり方その2
wgetを使って、自分のサーバーへ直接WordPressをダウンロードする。

`$wget http://wordpress.org/latest.tar.gz`

次に、使用するパッケージを展開

`$tar -xzvf latest.tar.gz`

* MySQLクライアントの利用

`$ mysql -u root -p`
`mysql> CREATE DATABASE databasename;`
`mysql> GRANT ALL PRIVILEGES ON databasename.* TO "wordpressusername"@"hostname"`
`->IDENTIFIED BY "password";`
`mysql> FLUSH PRIVILEGES;`
`mysql> EXIT`

* wp-config.phpの設定
展開したWordPressのパッケージへ移動して、`wp-config-sample.php`のファイル名を`wp-config.php`に変更するか、コピーする。
`cp wp-config-sample.php wp-config.php`
`wp-config.php`にデータベースのデータを入力する。
認証用ユニークキーをオンラインジェネレーターで作成し値を入力する。

* /etc/httpd/httpd.confを編集する

* WordPressのインストール
ブラウザのURLに192.168.56.101/wp-admin/install.phpを入力してWordpressをインストールしてユーザー名とパスワードを入力してログイン
