# Section 2 その他のWebサーバー環境

## 2-1 Vagrantを使用したCentOS7のイメージを登録

### Vagrant用CentOS boxをUSBストレージに用意してそこからコピーして登録して使う。

	vagrant box add CentOS7 コピーしたboxファイル --force

### Vagrantの初期設定

まず、作業用ディレクトリを作成

その作業用ディレクトリの中で初期設定をする。

	vagrant init
	というコマンドを実行してVagrantfileを作成。

viでVagrantfileを開き、

	config.vm.box = "base"

の箇所を

	config.vm.box = "CentOS7"

に変える。

### 仮想マシンの起動

[VirtualBoxの公式サイトのダウンロードページ](https://www.virtualbox.org/wiki/Downloads)から

	VirtualBox 5.0.20 Oracle VM VirtualBox Extension Pack  All supported platforms

のAll supported platformsをクリックすると、

Oracle_VM_VirtualBox_Extension_Pack-5.0.20-106931.vbox-extpack

のダウンロードが始まるのでそれをダウンロードしたら、

Virtualboxのファイル>環境変数>機能拡張の設定から

ダウンロードしてきたOracle_VM_VirtualBox_Extension_Packを登録する。

ターミナルに戻って仮想マシンを起動する。

	vagrant up

### 仮想マシンの停止

	vagrant halt

### 仮想マシンの一時停止

	vagrant suspend

### 仮想マシンの破棄

破棄するとCentOSが初期化される。また`vagrant up`すると立ち上がる。

	vagrant destroy

### 仮想マシンへ接続

	vagrant ssh

### ホストオンリーアダプターの設定
動作確認のためIPアドレスを設定します。そのためのNICを追加します。

	vi Vagrantfile  で

	config.vm.box = "CentOS7"`の下に`config.vm.network "private_network", ip:"192.168.56.129"


※　複数台の仮想マシンを立ち上げる時には異なるIPアドレスを割り当てる必要があります。 ### Vagrantfileの反映
Vagrantfileで変更した設定を反映させるには

	vagrant reload

で再起動して反映されるから注意。

# 2-2 WordPressを動かす(2)

yum使えるように設定する。

	sudo vi /etc/yum.conf`のどこかに`proxy=http://Proxyサーバーアドレス:ポート番号

を入力して	sudo yum update
でアップデートする。

	sudo yum -y install epel-release
	でepelリポジトリインストール

	sudo vi /etc/yum.repos.d/epel.repo
で「--enablerepo」で指定しなければ「epel」リポジトリを使えない設定にしておく。

[epel]部分の「enabled=1」を「enabled=0」に変更。

### nginxインストール

	sudo yum install --enablerepo=epel nginx

### phpのインストール

	sudo yum -y install --enablerepo=remi,remi-php70 php php-devel php-mbstring php-pdo php-gd php-mysql php-pear php-fpm php-mcrypt

ついでに、php-fpmもインストール

### /etc/php-fpm.d/www.confの編集

apacheになってるところをnginxに変更


	  user = nginx
  grounp = nginx



### php-fpmの起動をサービス化
起動

	sudo service php-fpm start

サービス化

	sudo chkconfig php-fpm on

### MariaDBのインストール
	sudo yum -y install mariadb mariadb-server`でインストール

	sudo rpm -qa | grep maria  で確認

### MariaDBの有効化と起動

	sudo systemctl enable mariadb-service  で有効にする。

	sudo systemctl start mariadb-service  で起動

### MariaDBの初期設定

	mysql_secure_installation

とりあえずMariaDBを再起動

	sudo systemctl restart mariadb

### MariaDBクライアントの利用

	$ mysql -u root -p

	MariaDB> CREATE DATABASE databasename;

	GRANT ALL PRIVILEGES ON databasename.* TO "username"@"hostname" IDENTIFIED BY "password";

	FLUSH PRIVILEGES;

	exit

# Wordpressのダウンロード

	wget http://wordpress.org/latest.tar.gz  でダウンロードしてくる

	tar xzfv latest.tar.gz  で展開

chownで所有者とグループをnginxに変える

	sudo chown -R nginx:nginx wordpress  で変えたら

wordpress/を/var/www/の下に移動させる

	sudo mv wordpress/ /var/www/

### wp-confing.phpの設定ファイルを編集

	cd /var/www/wordpress

	sudo cp wp-confing.sample.php wp-confing.php  でコピーしたwp-confing.phpを編集。MariaDBで作ったデータベースのデータを入力。

認証用ユニークキーをオンラインジェネレーターで作成し値を入力する。

### default.confの中身を変更

	vi /etc/nginx/conf.d/default.conf

	root /var/www/wordpress  に変更

	fastcgi_param SCRIPT_FILENAME  と	$fastcgi_script_name  の間に	$document_root  を入力。

変更したら	nginx -t  でチェックして

nginxを再起動

	sudo systemctl restart nginx

### Wordpress インストール

ブラウザのURLに192.168.56.129/wp-admin/install.phpを入力してWordpressをインストールする。

## 2-3 Wordpressを動かす(3)

2-2とは別の作業ディレクトリを作る

### Apache HTTP Server 2.2をダウンロード・コンパイル

#### ダウンロード

	wget http://ftp.yz.yamagata-u.ac.jp/pub/network/apache/httpd/httpd-2.2.31.tar.gz

#### 展開

	tar -xvf httpd-2.2.31.tar.gz

#### Makefileを作成

ソースファイルをコンパイルする前に、インストール対象となるシステム特有の機能
情報をチェックし、チェック状況を記述したMakefileを作成する。

	./configure

#### ソースファイルのコンパイル

	make

#### インストール(インストールは一般的にrootユーザーにて行う。)

(make で作成したファイルなどを任意のディレクトリにコピー)を行う。

	sudo make install

### PHP7.0をダウンロード・インストール

[参考サイト](http://docs.hatenablog.jp/entry/php7)

#### ダウンロード

	$ wget http://jp2.php.net/get/php-7.0.0.tar.gz/from/this/mirror -O php-7.0.0.tar.gz

#### 展開

	tar zxvf php-7.0.0.tar.gz

#### Makefileを作成

	$ ./configure

#### ソースファイルのコンパイル

	$ make

#### インストール

	$ sudo make install

### Apacheの起動

	sudo /usr/local/apache2/bin/apachectl start

### MariaDBのインストール
	sudo rmp --import https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
	sudo yum -y install mariadb mariadb-server mariadb-client mariadb-devel
	rpm -qa | grep maria  で確認
	sudo rpm -e --nodeps mysql-libs
	sudo systemctl enable mariadb.service
	sudo systemctl startn mariadb.service

#### MariaDBの初期設定

	mysql_secure_installation  でrootのパスワードを設定する。

#### MariaDBでユーザー作成

	mysql -u root -p
	create database databasename;
	grant all privileges on databasename.* to "username"@"localhost" identified by "password";
	flush privileges;
	exit;

### Wordpressのダウンロード・インストール

#### ダウンロード

	$ wget http://wordpress.org/latest.tar.gz

#### 展開・移動

	$ tar -xvf latest.tar.gz

	$ mv latest.tar.gz /var/www/  に移動

#### wp-confing.phpの設定ファイルを編集

	cd /var/www/wordpress

	sudo cp wp-confing.sample.php wp-confing.php  でコピーしたwp-confing.phpを編集。MariaDBで作ったデータベースのデータを入力。

認証用ユニークキーをオンラインジェネレーターで作成し値を入力する。

### default.confの中身を変更

	vi /etc/nginx/conf.d/default.conf

	root /var/www/wordpress  に変更

	fastcgi_param SCRIPT_FILENAME  と	$fastcgi_script_name  の間に	$document_root  を入力。

#### apacheを再起動

	 $ sudo /usr/local/apache2/bin/apachectl restart

### Wordpress インストール

ブラウザのURLに192.168.56.130/wp-admin/install.phpを入力してWordpressをインストールする。

## 2-4 ベンチマークを取る

### abコマンド
Ubuntuにabコマンドインストール

	$ sudo apt install apache2-utils

	$ sudo apt install netatalk

	$ ab http://IPアドレス/

	This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
	Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
	Licensed to The Apache Software Foundation, http://www.apache.org/

	Benchmarking 192.168.56.129 (be patient).....done


	Server Software:        nginx/1.6.3
	Server Hostname:        192.168.56.129
	Server Port:            80

	Document Path:          /
	Document Length:        10371 bytes

	Concurrency Level:      1
	Time taken for tests:   0.182 seconds
	Complete requests:      1
	Failed requests:        0
	Total transferred:      10603 bytes
	HTML transferred:       10371 bytes
	Requests per second:    5.49 [#/sec] (mean)
	Time per request:       182.143 [ms] (mean)
	Time per request:       182.143 [ms] (mean, across all concurrent requests)
	Transfer rate:          56.85 [Kbytes/sec] received

	Connection Times (ms)
	              min  mean[+/-sd] median   max
		      Connect:        0    0   0.0      0       0
		      Processing:   182  182   0.0    182     182
		      Waiting:      176  176   0.0    176     176
		      Total:        182  182   0.0    182     182


2-2と2-3のWordpressを比較する。

	This is ApacheBench, Version 2.3 <$Revision: 1706008 $>
	Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
	Licensed to The Apache Software Foundation, http://www.apache.org/

	Benchmarking 192.168.56.130 (be patient).....done


	Server Software:        Apache/2.2.31
	Server Hostname:        192.168.56.130
	Server Port:            80

	Document Path:          /
	Document Length:        10643 bytes

	Concurrency Level:      1
	Time taken for tests:   0.168 seconds
	Complete requests:      1
	Failed requests:        0
	Total transferred:      10889 bytes
	HTML transferred:       10643 bytes
	Requests per second:    5.97 [#/sec] (mean)
	Time per request:       167.642 [ms] (mean)
	Time per request:       167.642 [ms] (mean, across all concurrent requests)
	Transfer rate:          63.43 [Kbytes/sec] received

	Connection Times (ms)
	              min  mean[+/-sd] median   max
		      Connect:        0    0   0.0      0       0
		      Processing:   167  167   0.0    167     167
		      Waiting:      159  159   0.0    159     159
		      Total:        168  168   0.0    168     168

### PageSpeed

Google Chromeに[PageSpeed](https://goo.gl/EA11zq)の拡張機能を入れる。

自分のWordpressのページのDeveloper tools>PageSpeed>REFRESH RESULTSをクリックしてベンチマークを取る。

192.168.56.129の結果はPage Speed Score: 81/100 でした。

192.168.56.130の結果はPage Speed Score: 77/100 でした。


## Wordpressの高速化

### WP Super Cache導入

vagrantのwordpressのpluginsに移動。

	cd /usr/local/apache2/wordpress/wp-content/plugins/

wgetでWP Super Cacheを取ってくる

	wget -q http://downloads.wordpress.org/plugin/wp-super-cache.1.4.zip

展開

	unzip wp-super-cache.1.4.zip

ブラウザからWordpressの192.168.56.130の結果は
Page Speed Score: 66/100に変わった。(まさかの下がった)
