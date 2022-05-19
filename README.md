# vagrant-kayac-isucon-2022

## Overview

[kayac-isucon-2022](https://github.com/kayac/kayac-isucon-2022)とほぼ同じ環境を構築するためのVagrantfileです。

## Requirements

* [Vagrant](https://www.vagrantup.com/)実行環境

## Usage

* このリポジトリ内の `Vagrantfile` を手元に用意します
  ```sh
  git clone --depth 1 1https://github.com/matsuu/vagrant-kayac-isucon-2022.git
  cd vagrant-kayac-isucon-2022
  ```
* Vagrantfileがあるディレクトリで`vagrant up`を実行します
  * サーバが1台起動
* プロビジョニングが完了したら`vagrant ssh`を実行します
  ```sh
   vagrant ssh
  ```

## Bench

* 構築が終わったらベンチマークを実行します
  ```sh
  sudo -i -u isucon
  cd bench
  ./bench
  ```

## 本来の設定と異なるところ

* ベンチマークをコンパイルするためgolangなど依存するソフトウェアをインストールしています

## FAQ

### 途中でエラーが発生した

スリープモードなどの影響で失敗した場合は以下で再試行ができます。

```sh
vagrant provision
```

### プログラムの動かし方がわからない

以下をご確認ください。

* [kayac-isucon-2022レギュレーション＆当日マニュアル](https://github.com/kayac/kayac-isucon-2022/blob/main/docs/README.md)
* [kayac-isucon-2022リポジトリ](https://github.com/kayac/kayac-isucon-2022)

## 動作確認

macOS + VirtualBox 6.1.26 + Vagrant 2.2.18で動作確認済です。

## FAQ

### `vagrant up` でBoxのダウンロードが404 Not Foundのエラーになる

Vagrantのバージョンが古いためと思われます。
可能であれば最新バージョンのVagrantをご利用ください。
諸事情により古いVagrantしか使えない場合はVagrantfileの先頭に以下を設定してください。

```
Vagrant::DEFAULT_SERVER_URL.replace('https://vagrantcloud.com')
```

参考 https://github.com/matsuu/vagrant-isucon/pull/24

### virtualboxで以下のようなエラーメッセージが表示される

> The provider 'virtualbox' that was requested to back the machine
> 'default' is reporting that it isn't usable on this system. The
> reason is shown below:
>
> Vagrant has detected that you have a version of VirtualBox installed
> that is not supported. Please install one of the supported versions
> listed below to use Vagrant:
>
> 4.0, 4.1, 4.2, 4.3

Vagrantのバージョンが古い可能性があります。最新のVagrantを使用してください。

### vagrant upを実行するとvboxsfのエラーが表示される

> Failed to mount folders in Linux guest. This is usually because
> the "vboxsf" file system is not available. Please verify that
> the guest additions are properly installed in the guest and
> can work properly. The command attempted was:
>
> mount -t vboxsf -o uid=`id -u vagrant`,gid=`getent group vagrant | cut -d: -f3` vagrant /vagrant
> mount -t vboxsf -o uid=`id -u vagrant`,gid=`id -g vagrant` vagrant /vagrant
>
> The error output from the last command was:
>
> /sbin/mount.vboxsf: mounting failed with the error: No such device

[これと同じ現象](http://qiita.com/hapicky/items/a7f9d56588f96d005fad)と思われます。気にせず`vagrant provision`を実行してください。

### ブラウザで動作確認ができない

Vagrantfileのネットワーク設定がデフォルトのままなので、サーバに割り当てられたIPアドレスにブラウザでアクセスしてみてください。
よくわからない場合は`# config.vm.network "private_network", ip: "192.168.33.10"`のコメントを外してブラウザから192.168.33.10にアクセスしてみてください。
