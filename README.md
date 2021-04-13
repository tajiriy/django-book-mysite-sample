# 「現場で使える Django の教科書」サンプルコード

「現場で使える Django の教科書」のサンプルコードです。
サンプルプロジェクトのテーマは「本のオンラインショップ」です。
なおこのサンプルコードは予告なく変更されることがありますのでご了承ください。

* サンプルコード（Django 2.2 バージョン）
  * https://github.com/tajiriy/django-book-mysite-sample (fork from https://github.com/akiyoko/django-book-mysite-sample/)

サンプルコードでは主に、ログイン画面と商品リスト画面まわりの実装をしています。
画面遷移は次のようになっています。

<img width="700" src="https://user-images.githubusercontent.com/1287113/44252736-26f10c80-a238-11e8-9b30-87ebeff5b7bb.png">


## サンプルプロジェクトを動かすまでの最短ステップ

Windows10 + WSL2 + Docker でサンプルプロジェクトを動かすまでのステップについて説明します。
Docker を利用しますので、事前に「Docker for Windows」（https://www.docker.com/products/docker-desktop ）をインストールしておいてください。


### 1. サンプルコードのダウンロード

まず、サンプルコードを GitHub からダウンロードします。
作業場所はどこでも構いませんが、今回の例では Django のワークスペース「D:\DjangoProjects」を作成して、作業場所にしています。

次の Git コマンドを実行して、main ブランチをダウンロードします。

```
$ cd /d D:\DjangoProjects
$ git clone git@github.com:tajiriy/django-book-mysite-sample.git
```


### 2. サンプルプロジェクト用 Docker イメージの作成

clone したディレクトリに移動して、その直下にある Dockerfile から Docker イメージを作成します。
Docker イメージは Ubuntu 18.04 LTS をベースに、python3、pip3 などの最低限必要となるソフトウェアと、
サンプルプロジェクトの requirements.txt に記載された各種パッケージをインストールしています。

```
$ cd django-book-mysite-sample
$ docker build -t mysite-sample:1.0 .
```


### 3. Docker コンテナの実行

作成したイメージを使って Docker コンテナを実行し、bash を起動します。
コンテナ実行時にデータベース（SQLite）の作成やマイグレーション、superuser の作成、および runserver の起動までをおこなっています。
なお、起動時に script/init_mysite.sh が実行されていますが、改行コードが CRLF だとエラーになるので、LFにしておく必要があります。

```
$ docker run --rm -it -p 8000:8000 -v D:\DjangoProjects\django-book-mysite-sample:/root/mysite --name mysite-sample mysite-sample:1.0 /bin/bash
```

Docker コンテナを停止するときは、次のコマンドを実行してください。

```
$ docker stop mysite-sample
```

コンテナの実行を繰り返すと「django.db.utils.IntegrityError: UNIQUE constraint failed: custom_user.username」
とのエラーが表示されますが、これはデータベースに同じユーザー名の superuser を登録しようとして出るエラーで、特に実害はないので無視して構いません。


### 4. 動作確認

ブラウザから「http://localhost:8000 」あるいは「http://127.0.0.1:8000 」にアクセスします。
次のようなログイン画面が表示されれば OK です。

<img width="500" src="https://user-images.githubusercontent.com/1287113/44251734-71708a00-a234-11e8-8fdb-7fae24cc06ae.png">

ユーザー名「admin」、パスワード「adminpass」の superuser が先の手順で作成されているはずなので、ログインをしてみましょう。

<img width="500" src="https://user-images.githubusercontent.com/1287113/44251735-71708a00-a234-11e8-82f6-aa321c69fff3.png">

本の一覧が表示されるはずが、まだ本が登録されていないので何も表示されません。
出版社、著者、本などのレコードは、管理サイト（http://localhost:8000/admin/ ）から登録することができます。
