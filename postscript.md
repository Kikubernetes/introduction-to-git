他の端末でもgitを使う場合

本文中に出てきたターミナル設定用のコマンドをまとめました。

 .DS_Store（macの開いた履歴などを記録している）をgitに送るファイルに含めない

    $ git config --global core.excludesfile ~/.gitignore_global
    $ echo ".DS_Store" >> ~/.gitignore_global

 ターミナルでのgitのコマンドを色付きにする（最近は設定済み？）

    $ git config --global color.ui true 

 git graphというgit logをきれいに表示してくれるエイリアスを作る

    $ git config --global alias.graph "log --graph --date-order --all --pretty=format:'%h %Cred%d %Cgreen%ad %Cblue%cn %Creset%s' --date=short"


本文章での説明は全てローカル（手元のPC）内で完結しています。リモートにあたるリポジトリも自分のPC内に作ります。
当初githubをリモートリポジトリにしようとすると、git initでローカルリポジトリから作る方法はどうしてもうまくいかなかったので先にwebページでリポジトリを作り、アドレスをコピー（緑色の「code」⇨https://github.com/
・・・の右端にコピーボタンがあります）し、ターミナル上でディレクトリを置きたい場所で

    $ git clone https://・・・
    $ cd 上記で作ったリポジトリ名（同名のディレクトリができている）
    
これで本文中にある　git init　までが終了します。
（追記）途中でgitのバージョンを上げたらなぜかできるようになりました。masterとmainの食い違いが原因だったかもしれません。

