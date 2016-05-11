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

'sudo vi /etc/sysconfig/network-script/ifcfg-enp0s3'
中身の

#### SSH接続の確認
