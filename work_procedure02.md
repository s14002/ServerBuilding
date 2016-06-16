# Section 2 その他のWebサーバー環境

## 2-1 Vagrantを使用したCentOS7のイメージを登録

### Vagrant用CentOS boxをUSBストレージに用意してそこからコピーして登録して使う。

`vagrant box add CentOS7 コピーしたboxファイル --force`

### Vagrantの初期設定

まず、作業用ディレクトリを作成

その作業用ディレクトリの中で初期設定をする。

`vagrant init`というコマンドを実行してVagrantfileを作成。

viでVagrantfileを開き、

`config.vm.box = "base"`

の箇所を

`config.vm.box = "CentOS7"`

に変える。

### 仮想マシンの起動

[VirtualBoxの公式サイトのダウンロードページ](https://www.virtualbox.org/wiki/Downloads)から

> VirtualBox 5.0.20 Oracle VM VirtualBox Extension Pack  All supported platforms

のAll supported platformsをクリックすると、

Oracle_VM_VirtualBox_Extension_Pack-5.0.20-106931.vbox-extpack

のダウンロードが始まるのでそれをダウンロードしたら、

Virtualboxのファイル>環境変数>機能拡張の設定から

ダウンロードしてきたOracle_VM_VirtualBox_Extension_Packを登録する。

ターミナルに戻って仮想マシンを起動する。

`vagrant up`

### 仮想マシンの停止

`vagrant halt`

### 仮想マシンの一時停止

`vagrant suspend`

### 仮想マシンの破棄

破棄するとCentOSが初期化される。また`vagrant up`すると立ち上がる。

`vagrant destroy`

### 仮想マシンへ接続

`vagrant ssh`

### ホストオンリーアダプターの設定
動作確認のためIPアドレスを設定します。そのためのNICを追加します。

`vi Vagrantfile`で

`config.vm.box = "CentOS7"`の下に`config.vm.network "private_network", ip:"192.168.56.129"`

※　複数台の仮想マシンを立ち上げる時には異なるIPアドレスを割り当てる必要があります。

### Vagrantfileの反映

Vagrantfileで変更した設定を反映させるには

`vagrant reload`

で再起動して反映されるから注意。

# 2-2 WordPressを動かす(2)

yum使えるように設定する。

`sudo vi /etc/yum.conf`のどこかに`proxy=http://Proxyサーバーアドレス:ポート番号`

を入力して`sudo yum update`でアップデートする。

`sudo yum -y install epel-release`でepelリポジトリインストール

`sudo vi /etc/yum.repos.d/epel.repo`で「--enablerepo」で指定しなければ「epel」リポジトリを使えない設定にしておく。

[epel]部分の「enabled=1」を「enabled=0」に変更。

### nginxインストール
`sudo yum install --enablerepo=epel nginx`

### phpのインストール
`sudo yum -y install --enablerepo=remi,remi-php70 php php-devel php-mbstring php-pdo php-gd php-mysql php-pear php-fpm php-mcrypt`

ついでに、php-fpmもインストール

### /etc/php-fpm.d/www.confの編集
apacheになってるところをnginxに変更

'
  user = nginx
  grounp = nginx
'


### php-fpmの起動をサービス化
起動

`sudo service php-fpm start`

サービス化

`sudo chkconfig php-fpm on`

### MariaDBのインストール
`sudo yum -y install mariadb mariadb-server`でインストール

`sudo rpm -qa | grep maria`で確認

### MariaDBの有効化と起動

`sudo systemctl enable mariadb-service`で有効にする。

`sudo systemctl start mariadb-service`で起動

### MariaDBの初期設定
`mysql_secure_installation`

とりあえずMariaDBを再起動

`sudo systemctl restart mariadb`

### MariaDBクライアントの利用

`$ mysql -u root -p`

`MariaDB> CREATE DATABASE databasename;`

`GRANT ALL PRIVILEGES ON databasename.* TO "username"@"hostname" IDENTIFIED BY "password";`

`FLUSH PRIVILEGES;`

`exit`

# Wordpressのダウンロード

`wget http://wordpress.org/latest.tar.gz`でダウンロードしてくる

`tar xzfv latest.tar.gz`で展開

chownで所有者とグループをnginxに変える

`sudo chown -R nginx:nginx wordpress`で変えたら

wordpress/を/var/www/の下に移動させる

`sudo mv wordpress/ /var/www/`

### wp-confing.phpの設定ファイルを編集

`cd /var/www/wordpress`

`sudo cp wp-confing.sample.php wp-confing.php`でコピーしたwp-confing.phpを編集。MariaDBで作ったデータベースのデータを入力。

認証用ユニークキーをオンラインジェネレーターで作成し値を入力する。

### default.confの中身を変更

vi /etc/nginx/conf.d/default.conf'

`root /var/www/wordpress` に変更

`fastcgi_param SCRIPT_FILENAME`と`$fastcgi_script_name`の間に`$document_root`を入力。

変更したら`nginx -t`でチェックして

nginxを再起動

`sudo systemctl restart nginx`

### Wordpress インストール

ブラウザのURLに192.168.56.129/wp-admin/install.phpを入力してWordpressをインストールする。

## 2-3 Wordpressを動かす(3)

2-2とは別の作業ディレクトリを作る

### Apache HTTP Server 2.2をダウンロード・コンパイル

#### ダウンロード

`wget http://ftp.yz.yamagata-u.ac.jp/pub/network/apache/httpd/httpd-2.2.31.tar.gz`

#### ビルド

ソースファイルをコンパイルする前に、インストール対象となるシステム特有の機能
情報をチェックし、チェック状況を記述したMakefileを作成する。

`./configure`

ソースファイルのコンパイル

`make`

#### インストール(インストールは一般的にrootユーザーにて行う。)

(make で作成したファイルなどを任意のディレクトリにコピー)を行う。

`sudo make install`

