# みんなでつかう - ベアリポジトリとクローン,リモートリポジトリ

さて、前回までで、ひとりで使うぶんには基本的な使い方ができるようになりましたね。では、これからはみんなで Git を使って行きましょう。ここからは学習のためにひとり何役もこなしてもらいますよ！

# まずは準備

まずは毎度恒例、準備から行いましょう。そして、今からあなたが演じるのは「たかし」です。

たかしは、今からあるプロジェクトを始めます。まずはそのためのディレクトリとリポジトリを作成しましょう。ディレクトリの名前は「takashis_workspace」にしましょうか。ではそこにリポジトリを作ってください。

できましたか？

そして、あなたはこのディレクトリ内にいるときはたかしくんです。なので、たかしくんとして振る舞うための設定をしておきましょう。

    $ cd path/to/takashis_workspace
    $ git config user.name TAKASHI
    $ git config user.email takashi@example.com

これで、このディレクトリ内ではあなたの名前は TAKASHI になりますし、メールアドレスも takashi@example.com となります。

まずは、なにかコミットしましょう。いつものやつにしますか。

* cat_lover_said.txt

```
猫は動物の一種である。

犬とならび、ペットとして飼われることの多い動物である。

猫は人間のことを下僕かなにかだと思っており、また人間も
人間で猫に使役されることを至上の喜びと思う傾向にある。

猫の魅力に取り付かれた人間は二度と猫の魅力から離れることが
できないとも言われており、猫ってほんとうにかわいいですね。

もうほんとうに可愛い、猫。
猫。
猫が本当にかわいい。

猫好きじゃない人間とか頭がおかしいとしか思えない。
```

上のような内容のテキストを作成し、コミットしてください。コミットメッセージは「猫好きの話を追加」でいいですね。

さて、準備はいいですね。では、まずは今回のブランチの運用のポリシーを決めましょう。

まず、mainブランチですが、main ブランチにコミットするものは「リリースするもの」だけ、ということにしましょう。開発途中のものをコミットするのは、「development」というブランチにします。そして、作業は development からトピックブランチを切って行います。

言い方を変えると、なにか作業をするときには development ブランチから topic ブランチを切って行います。そして、その作業が終わったら、develop ブランチでそのトピックブランチを merge します。これを繰り返して、development ブランチの状態が「リリースできるもの」になったら、main ブランチから development ブランチを merge します。

こうすることによって、

* main が指してるコミットは、必ず 「最新のリリース」 である
* development が指しているコミットは、「最新の開発版」である
* 各トピックブランチが指しているコミットは、「作業途中の状態」である

ということが保証されます。なんか便利な気がしますね。ディレクターから「今の最新版チェックしたいから見せて」と言われたら、「自分で development ブランチ見ろや」って言えますし、ユーザーから不具合報告が来たら、main ブランチの内容を精査すればいいのです。

それともうひとつ、すでにリリースしてしまったものに不具合が見つかったときにそれを緊急で修正しなければならないときには、main ブランチから hotfix ブランチを切って、ここで修正した内容を main ブランチに merge することにしましょう。これで、開発中のものを急いでリリースせずに、緊急の対応だけは先にリリース版に組み込みことができますね。

そして、この緊急対応が無事にリリースされたなら、hotfix が取り込まれた main ブランチを development にマージすることにしましょう。このように運用することで、開発版にも、リリース版での修正を反映することができます。

では、このような開発を進めるために、たかしくんであるあなたは、まずは development ブランチを作っておきましょう。

    $ git branch development

はい、いいですね。

## ゆうすけがプロジェクトに参戦！

さて、ここで、このプロジェクトにあたらしくゆうすけが参戦することになりました。嬉しいですね！今回は簡単のため、たかしもゆうすけも同じマシン上で開発を行っているという設定にしましょう。ほんらいならば、おそらくネットワーク越しに協調作業を行うことになるのでしょうが、その場合もやることはほとんど変わりません。

さて、たかしであるあなたは、「ゆうすけとどうやって作業を一緒にやろうかな」と考えています。"ゆうすけのリポジトリ" と "たかしのリポジトリ" の間で変更内容をやりとりしてもいいのですが、このあと、さらに三人目のメンバーがプロジェクトに参戦した場合、三人で互いに変更内容をやりとりするのは想像するだけで大変ですね。

なので、ふたりが共通でさわるリポジトリを作ってしまいましょう。それぞれ、自分のリポジトリで行った変更をこの共通のリポジトリに反映させたり、相手が行った変更をこの共通のリポジトリから取ってきたりする、という運用でいきます。

<img src="https://raw.github.com/Shinpeim/introduction-to-git/master/images/09_00.png" alt="みんなで触るリポジトリーのイメージ" width="400">

では、なにはともあれ「みんなで触るリポジトリ」が必要ですね。この「みんなで触るリポジトリ」は、「たかしのリポジトリ」から複製してしまいましょう。リポジトリの複製は、`git clone ＜複製元＞ ＜複製される先＞` です。 takashis_workspace ディレクトリから出て、

    $ git clone --bare path/to/takashis_workspace path/to/shared_repo.git

と打ってみましょう。 `--bare` というオプションがついてますが、今は気にしないでおいてください。 path/to/shared_repo.git はそれぞれ自分の環境に読み替えてください。

さて、実際にコマンドを打ってみると、こんな表示が出てくるはずです。

```
Cloning into bare repository 'shared_repo.git'...
done.
```

「bare repository 'shared_repo.git' を作ったよ」だそうです。bare repository？ なんだそれは？

それを知るために、作られたリポジトリの正体を見てみましょう。ファイラーでもなんでもいいです。作られたリポジトリはどんなファイルですか？ そう、ディレクトリですね。そして、その中になんかいろいろごちゃごちゃとファイルが入っているのがわかるでしょう。

ここで、本書の最初で見た「リポジトリの実体」がなんだったか、思い出してみましょう。そうです、作業ディレクトリの中に作られた、「.git」というディレクトリでしたね。リポジトリは、ただのディレクトリです。そして、今回の「bare repository」もディレクトリでした。

勘のいいひとはもうわかったかもしれませんね。実は、 

    $ git clone --bare 複製元 複製される先

という感じで `--bare` を付けてリポジトリを複製すると、作業ディレクトリなしで、リポジトリだけを複製することができるのです。

「みんなで触るリポジトリ」で直接作業を行うことはないので、このリポジトリには作業ディレクトリは要らないですよね。なので、みんなで触るリポジトリを作るときには `--bare` 付きで複製しましょう。みんなで触るリポジトリをこういう「bare repository」で作るのには、実はもうひとつ理由があるのですが、それはまた改めて説明しましょう。ちなみに、bareリポジトリの名前は、最後に「.git」を付けるのが慣習となってます。

さて、これで無事に「みんなで触るリポジトリ」が出来上がりました。

では、今からあなたは「ゆうすけ」です。ゆうすけはこのプロジェクトに参戦するために、このプロジェクトのリポジトリを手元に複製する必要ですがありますね。

というわけで、ゆうすけはこの「みんなで触るリポジトリ」から、自分の手元にリポジトリを複製してきましょう。

    $ git clone path/to/shared_repo.git path/to/yuusukes_workspace

はい、`--bare` なしでcloneしてみました。これで、path/to/yuusukes_workspace に、作業ディレクトリ付きでリポジトリが複製されました。

では、ゆうすけの作業ディレクトリではゆうすけとして振る舞う設定をしておきましょう。

    $ cd path/to/yuusukes_workspace
    $ git config user.name YUUSUKE 
    $ git config user.email yuusuke@example.com

## ゆうすけ、混乱する

さて、ここで、ディレクターは、ゆうすけに例の「文体変更」のお仕事をお願いしました。ゆうすけであるあなたは、この作業を行おうとしています。

作業を行うには、development ブランチからトピックブランチを切るのでしたね。ちゃんと手元に development ブランチが複製されてきてるか、`git branch` で確認しましょう。

    $ git branch
    * main
    
！？ developmentブランチがないぞ！？ グラフも確認しましょう。

    $ git graph
    * 253585d  (HEAD -> main, origin/main, origin/development, origin/HEAD) 2013-05-07 TAKASHI 猫好きの話を追加

なんか知らないのたくさんある！怖い！！

怖いですね。説明しましょう。

## リモートリポジトリとはなにか

さて、この状態を説明するためには、「リモートリポジトリ」というものを説明しないといけません。「リモートリポジトリ」とはなんでしょうか。その説明のために、今回の登場人物(登場リポジトリ？）の整理から始めましょう。今回の共同作業で登場したリポジトリは、みっつです。

* たかしのリポジトリ
* ゆうすけのリポジトリ
* みんなで触るリポジトリ

そして、ゆうすけは、ゆうすけのリポジトリで変更した内容を「みんなで触るリポジトリ」に反映させなければなりません。あるいは、たかしが行った内容を、「みんなで触るリポジトリ」から取得しなければなりません。

こういう、「あのリポジトリにコミット内容を反映させたい」とか「あのリポジトリの変更内容を取得してきたい」というようなことをするためには、「あのリポジトリ」にあたるリポジトリを、「リモートリポジトリ」として登録しておかなければなりません。なので、今回で言えば、「みんなで触るリポジトリ」を「ゆうすけのリポジトリから見たリモートリポジトリ」として登録する必要がある訳ですね。

逆の言い方をすると、手元のリポジトリのコミット内容を別のリポジトリに反映させたり、別のリポジトリのコミット内容を手元のリポジトリに反映したい場合には、「リモートリポジトリ」として、そのリポジトリを登録しておく必要がある、ということです。

ちなみに、`git clone` でリポジトリを手元に複製してきた場合、Git さんが勝手に clone 元のリポジトリを、「origin」という名前のリモートリポジトリとして登録してくれるのです。これで、cloneしてきたリポジトリからコミット内容を手元に取り込んだり、手元のリポジトリのコミット内容をcloneしてきたリポジトリに反映できますね！Git さんってば気が利く！

では、現在のゆうすけのリモートリポジトリの様子を見てみましょう。あなたはゆうすけです。今ゆうすけの作業ディレクトリにいますね？では、そこで、 `git remote` と打ってみましょう。

    $ git remote
    origin

はい、origin が表示されました。これはつまり、「リモートリポジトリとして origin というリポジトリが登録されているよ」ということです。

ふむふむなるほど。

それでは、それを念頭においてから、先ほどの graph の結果を眺めてみましょう。

    $ git graph
    * 253585d  (HEAD -> main, origin/main, origin/development, origin/HEAD) 2013-05-07 TAKASHI 猫好きの話を追加

origin/main, origin/development, origin/HEAD というみっつのブランチがありますね。これらはそれぞれ、「origin リモートリポジトリ(つまり clone 元)の main ブランチ」「origin リモートリポジトリの development ブランチ」「origin リモートリポジトリのHEAD」を表しています。`git clone` をすると、クローン元に存在するコミットとブランチを、手元に複製してきます。このとき、ブランチは「リモートブランチ」としてコピーされてきます。リモートリポジトリからコピーされてきたブランチなので、「リモートブランチ」です。

今は、手元に main ブランチと、clone 元から複製されてきた三つの「リモートブランチ」が存在している状態です。

これらのリモートブランチは、もともと「自分の物」ではありません。「みんなのもの」です。なので、このリモートブランチを選択して作業をすることはできません。では、リモートリポジトリに内容を反映させたいような変更を手元で行うためには、どうすればいいのでしょうか？

手順は以下の通りです。

* リモートリポジトリの、反映したいブランチを「追跡」するためのブランチを手元のリポジトリに作成する
* そのブランチ上で変更を行い、コミットする
* このコミットを、「追跡」しているリモートブランチとリモートリポジトリに反映する

言い方を変えると、手元に「リモートを追跡するブランチ」を作ることで、そこで行った変更をその追跡元であるリモートブランチに反映したり、逆にリモートブランチに行われている変更は追跡しているブランチに取り込むことができる、ということです。

ちょっと複雑な話になっているので、ここまでをまとめておきましょうか。

* `git clone` すると、複製元のコミットとブランチがすべて手元のリポジトリに複製される
* このとき、複製元のブランチは「リモートブランチ」として複製される
* リモートリポジトリに対して手元で行った変更を反映するためには、このリモートブランチを「追跡」するブランチを手元に作成する必要がある。
  * このブランチに対するコミットは、対応するリモートリポジトリのブランチに反映することができる
  * 対応するリモートリポジトリのブランチで行われているコミットは、このブランチに取り込むことができる

さて、ゆうすけは今から作業を行うために、development ブランチからトピックブランチを切らなければなりません。なので、なにはともあれ手元に development ブランチが必要です。

というわけで、「origin/development」を追跡する development ブランチを、手元に作成しましょう。リモートブランチを追跡するブランチを作成するためには、 `git branch ＜手元のブランチの名前＞ ＜追跡したいリモートブランチの名前＞`です。やってみましょう。

    $ git branch development origin/development
    Branch development set up to track remote branch development from origin.

「development ブランチは、 origin から来た development リモートブランチを追跡するように設定されたよ」と言われましたね。いい感じです。

これで、ゆうすけは手元の development ブランチで行った反映を origin の development ブランチに反映したり、その逆に origin の development ブランチで行われた変更を自分の development ブランチに適用することができるようになりました。

ところで、さきほど `git clone` してきたときに、すでに手元に main ブランチが存在していましたね？ これはどういうことでしょうか。

git clone を行ったとき、まずはクローン元のリポジトリに存在するコミットとブランチが手元に複製されるのは上で見た通りです。じつは、Git さんはそのあと、origin/main ブランチを追跡する main ブランチを勝手に手元に作成してくれて、さらに作業ディレクトリ内にこの main ブランチを checkout するところまで自動でやってくれていたのです。

なるほどねー。

## 一方たかしは？

では、たかしのほうはどうでしょうか。今度はたかしになって、たかしの作業ディレクトリに移動してみてください。

```
$ cd path/to/takashis_workspace
$ git remote
```

なにも表示されませんね。

これは当然で、たかしのリポジトリは、clone で作ったリポジトリではないので、Git さんが勝手にリモートリポジトリを登録してくれたりはしていないのです。じゃあ、たかしのほうからも「みんなで触るリポジトリ」に対して変更を反映できたりそこから変更を取得してきたりできるように、remote リポジトリを設定しておきましょう。remoteリポジトリを追加するためには、`git remote add <リモートリポジトリの名前> <リモートリポジトリの場所>` です。

```
$ git remote add origin path/to/shared_repo.git
```

さて、これでリモートリポジトリが設定されました。確認してみましょう。

```
$ git remote
origin
```

うん、よさそうですね。では、graph も確認しましょう。

```
$ git graph
* a10bcb5  (HEAD -> main, development) 2013-05-07 Shinpei Maruyama 猫好きの話を追加
```

アレッ！？ origin/* ブランチがないですね！？

それもそのはず、今は「リモートリポジトリはここにあるよ」という設定はしましたが、そのリモートリポジトリに存在しているコミットやブランチを手元のリポジトリにコピーしてくるところまではやっていません。ゆうすけの場合、git clone を行うことでリモートリポジトリのコミットやブランチが手元にコピーされてきましたが、たかしはリモートリポジトリをクローンしたわけではないですからね。

では、リモートリポジトリに存在するコミットやブランチを手元に持ってきましょう。そのために使うコマンドが、 `git fetch` です。`git fetch ＜リモートリポジトリの名前＞` で、リモートリポジトリの中身を手元に持って来れます。やってみましょう。

```
$ git fetch origin
From path/to/shared_repo
 * [new branch]      development -> origin/development
 * [new branch]      main     -> origin/main
```

ふたつの新しいブランチが生まれていますね。矢印の左側が「リモートリポジトリでのブランチの名前」、矢印の右側が「手元にコピーされたリモートブランチ」です。

さて、これで準備は OK でしょうか？

じつは、まだ準備は万端ではありません。今、たかしの手元には development ブランチと main ブランチが存在していますが、このブランチがそれぞれ origin/development と origin/main を追跡するようにしないと、手元で行った変更が origin/development や origin/main に反映されませんね。なので、次は手元の main, development がそれぞれ origin/main, origin/development を追跡するように設定しましょう。

そのためには、"git branch --set-upstream-to=＜追跡したいリモートブランチ＞ ＜手元のブランチ＞" というコマンドを利用します。

ではやってみましょう。

    $ git branch --set-upstream-to=origin/main main
    Branch main set up to track remote branch main from origin.
    
    $ git branch --set-upstream-to=origin/development development
    Branch development set up to track remote branch development from origin.

うん、いい感じに設定できましたね。

ようやくこれで、みんなで Git を使う準備が整いました！今回はリポジトリを用意するところから初めて、しかも二人分の環境をひとりで設定したのでなんだか大変でしたが、実際のプロジェクトでは多くの場合「ゆうすけ」のポジションで開発を行うことになるでしょうから、そんなに大変なことはありません。「みんなで触るリポジトリ」を `git clone` してきて、必要に応じて手元で追跡ブランチを作成すればいいだけですね。

では、今回のまとめです。

* 手元のリポジトリでの変更内容を他のリポジトリに反映したり、逆に他のリポジトリでの変更内容を自分のリポジトリに反映するためには、そのリポジトリを「リモートリポジトリ」として登録しておかなくてはいけない
  * `git clone` でリポジトリを複製した場合、Git が勝手にこの複製もとを「origin」という名前でリモートリポジトリに登録してくれる
  * そうでない場合は、 `git remote add <名前> <場所>` でリモートリポジトリを登録できる
* リモートリポジトリに存在するブランチやコミットを手元に複製するためには、 `git fetch ＜リモートブランチの名前＞` を使う
  * `git clone` でリポジトリを複製した場合は、clone の段階ですでに手元のリポジトリに複製されている。
  * リモートリポジトリに存在するブランチは、手元のブランチにコピーしてくるときには「リモートブランチ」という形でコピーされる。
* リモートブランチに直接コミットすることはできないので、このリモートブランチを追跡するためのブランチを手元のリポジトリに作成する必要がある。
  * あるリモートブランチを追跡する新しいブランチを作成したい場合は、`git branch <新しいブランチ名> <リモートブランチ名>`
  * すでに手元に存在するブランチであるリモートブランチを追跡したい場合は `git branch --set-upstream-to=＜追跡したいリモートブランチ＞ ＜手元のブランチ＞`

では、次回は、手元で行ったコミットを「みんなで触るリポジトリ」に反映する方法、「みんなで触るリポジトリ」に反映されたコミットから手元にコミットを持ってくる方法を紹介して、実際にたかしとゆうすけの共同作業をやってみましょう。

[next - みんなでつかう - push pull](10_push_pull.md)
