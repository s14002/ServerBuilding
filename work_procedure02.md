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

がダウンロードが始まるのでそれをダウンロードしたら、

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

1-2
