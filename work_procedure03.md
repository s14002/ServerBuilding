# Section 3 Ansibleによる自動化テスト

## 3-0 Ansibleのインストール
[公式サイト](http://docs.ansible.com/ansible/intro_installation.html#latest-releases-via-apt-ubuntu)

	$sudo apt-get install spftware-properties-common
	$sudo apt-add-repository ppa:ansible/ansible
	$sudo apt-get update
	$sudo apt-get isntall ansible

## 3-1 AnsibleでWordpressを動かす

2-1でWordpressをNginx + PHP + MariaDBでインストールした手順をAnsibleのPlaybookで実行するように記述し、動かしてみてください。

VagrantfileからAnsibleを実行することもできますが、最初は普通に使用することをおすすめします。

echoを使ってhostsファイルに自分のサーバーのIPアドレスを記述する。

	$ echo IPアドレス > hosts

pingで確認。

	ansible IPアドレス -i hosts -m ping --private-key ~/.vagrant.d/insecure_private_key -u vagrant -k

playbook.ymlを書く。
playbook.ymlと同じ場所にnginx, php-fpm, wordpressディレクトリを作成。

	$mkdir nginx php-fpm wordpress

それぞれのディレクトリにWordpressの設定ファイルを作って記述。
Syntaxを確認

	$ ansible-playbook -i hosts playbook.yml --syntax-check

playbookを実行する。
	$ ansible-playbook playbook.yml -i hosts --private-key ~/.vagrant.d/insecure_private_key -u vagrant -k

エラーの場合

	$ ansible-playbook playbook.yml -i hosts --private-key ~/.vagrant.d/insecure_private_key -u vagrant -k -vv

3-1-2? VagrantfileからAnsibleを呼び出す

ggr(WIP)
