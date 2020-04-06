# ジェネレーターとは何か？

配列の親戚。オブジェクトの要素や関数の処理の結果を繰り返し１つずつ取得したい時に使う。相性が良さそうなのは、ツリー構造のデータを１つのデータにするなど。例えば、各メンバーで構成される部署、各部署で構成される会社と言うデータがあるとして、会社のメンバーリストを作りたい場合。YouTubeのコメントのように、コメント蘭があってあるコメントにさらに子のコメントリストを作って行き、子コメントを含め全てのコメントを一覧したいなど。うーん、そんな用途があるのか今のところちょっと…な感じ。

# 例で見る使い方

- whileから関数を呼ぶ代わりの書き方として使う

  - 大吉が出るまでおみくじを引く

- オブジェクトの要素から独自のルールで好きなもののみ抽出する

  - 開発チームの全メンバーを抽出する
    - [Symbol.iterator]の使い方
  - 子コメントのあるコメント覧から全てのコメントを抽出する
    - classのインスタンスから好きなプロパティを抽出する

# whileから関数を呼ぶ代わりの書き方として使う

## 大吉が出るまでおみくじを引く

下記はwhile文でイテレーターを使わ無い書き方も出来る。どっちを選ぶか。

```js
function* lot(){
  const type = ["大吉","吉","中吉","小吉","末吉","凶","大凶"];
  while(true){
    yield type[Math.floor(Math.random() * type.length)];
  }
}

for(const result of lot()){
  console.log(result)
  if(result === "大吉") break;
}
```

# オブジェクトの要素から独自のルールで好きなもののみ抽出する

## 開発チームの全メンバーを抽出する

[Symbol.iterator]は配列にはデフォルトで備わっている。このプロパティを持っていると`for of`で回せる。

```js
const testingTeam = {
  lead: "A男",
  tester: "B男",
  department: "テスト部",
  [Symbol.iterator]: function* (){
    yield this.lead;
    yield this.tester;
  }
}

const engineeringTeam = {
  testingTeam,
  size: 3,
  department: "開発部",
  lead: "A太",
  manager: "B太",
  engineer: "C太",
  [Symbol.iterator]: function* (){
    yield this.lead;
    yield this.manager;
    yield this.engineer;
    yield* this.testingTeam; // デリゲート
  }
}

let names = [];
for(const name of engineeringTeam){
  names.push(name);
}
console.log(names); // (5) ["A太", "B太", "C太", "A男", "B男"]
```

## 子コメントのあるコメント覧から全てのコメントを抽出する

classのメソッドとして`*[Symbol.iterator](){}`を作るとインスタンスを`for of`で回せる。

```js
class Comment {

  constructor(content,children){
    this.content = content;
    this.children = children;
  }

  // インスタンスを作成しそのプロパティをイテレートしたい
  *[Symbol.iterator](){
    yield this.content;
    // ジェネレーターの中で更に配列などを１つずつ取り出したい場合はfor ofが基本
    // mapなどを使うと、関数内でyieldする事になるが関数内でのyieldはエラー
    for(const child of this.children){
      yield* child; // デリゲート
    }
  }
}

const children = [
  new Comment("賛成",[]),
  new Comment("反対",[]),
  new Comment("うーん…",[])
]

const MyComment = new Comment("非常に良い記事だ！",children);

for(const cmnt of MyComment){
  console.log(cmnt);
}
```