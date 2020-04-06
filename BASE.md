# ざっくり

配列の兄弟。functionで宣言するのでややこしいが結局配列である。

# for ofでイテレーターを扱う

for ofでイテレーターを扱う場合、イテレータの中身を全部取り出すまで順番にループする。

# 配列に出来ない事が出来る

無限イテレーターを作れる。つまり、無限配列を作れる。終わりの無い配列、呼び出すと自身で新たに要素を作成し返してくれる配列。

```js
// 例
function* fib() {
  yield 0;
  yield 1;
  let a = 0, b = 1;
  while (true) {
    [a, b] = [b, a + b];
    yield b;
  }
}

const iter = fib();

for (const value of iter) {
  // 止めない限り永遠に値を作っていく
  if (value >= 100) break;
  console.log(value);
}
```