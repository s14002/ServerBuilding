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

>$sudo vi /etc/sysconfig/network-script/ifcfg-enp0s3

で中身のBOOTPROTO=dhcpのdhcpをnoneに変えた。そして、ONBOOT=noのnoをyesに変えた。

>$sudo vi /etc/sysconfig/network-script/ifcfg-enp0s8

で中身のONBOOT=noのnoをyesに変えた。

>$ip addr

でIPアドレスを確認してメモ。

#### SSH接続の確認
* Ubuntuからsshで仮想マシンに接続

>$ssh 学籍番号@メモしたIPアドレス

####インストール後の設定
* dconf-editerでProxyを設定
* /etc/yum.confにProxy=http://IPアドレス:8888を追加

>$sudo vi /etc/yum.conf

設定してyum updateやyum install wget したけど"ネットワークに届きません"ってエラー出たり、yumのmirrorsiteでエラーって出たから/etc/yum.repos.d/Centos-base.repoをイジイジしてsudo yum clean allしたりしてた。

>$sudo vi /etc/yum.repos.d/CentOS-Base.repo

>$reboot

* rebootかけたら最初からいけたかもしれないorz

####アップデート

>$sudo yum update

>$sudo yum install wget

### Wordpressを動かす(1)

Wordpressを動作させるためには下記のソフトウェアが必要になります。

* Apache HTTP Server
* MySQL
* PHP

* Apache HTTP Serverのインストール

>$sudo yum install httpd

* MySQLのインストール
Mysqlあったら消す
* PHPのインストール

* Wordpressのインストール
[wordpressの公式サイト](https://wordpress.org/)からUbuntuに入れて展開したディレクトリをscpコマンドで仮想マシンに移動する。

>$scp -r コピー元のディレクトリ ホームディレクトリ@IPアドレス(またはホスト名):~/(コピー先ディレクトリ)

