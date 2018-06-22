# Hashの話

---

## hash関数
入力(key)を変換してhash値を得る関数

```bash
$ echo a | md5sum
```

- hash値は特定範囲に収まる
- 一般に任意の長さの入力ができる
- 一般に一方向関数(元の値の類推が困難

---

### 用途1: 改竄検知
所謂checksum。データの変更を検知する

1bit書き変わるだけで大きく値が変更されるので検知しやすい

---

### 用途2: Hashテーブル
巨大なテーブルでも原理上O(1)でデータ検索可能

```ruby
class Table
  def initialize(size)
    @size = size
    @array = Array.new(size)
  end
  def calc_hash(key)
    key.hash % @size
  end
  def get(key)
    [key, @array[calc_hash(key)]]
  end
  def set(key, value)
    @array[calc_hash(key)] = value
  end
end
```

---

#### 補足
hashは衝突することがあるので値を直接読み出すのではなくLinked Listなどを使う

#### Hashテーブルの欠点
- 順序がランダム
- そこそこメモリ食う
- 頻繁に衝突すると性能が落ちる(後段のデータ構造に依存)
- hash関数が定義されていないと使えない
- 「良い」hash関数が定義されていないと性能が落ちる
- サイズ拡大に合わせてrehashが必要
- hash衝突攻撃によるDDoSが可能

---

#### 誕生日のパラドックス
「何人集まれば同じ誕生日の人がいる確率が50%を越えるか」(A.23人)

直感以上にhash値が衝突する可能性は高いと言える

---

### 用途3: パスワードの保存
元の値が類推しづらい特性はデータの安全な保存に有用

ただし暗号学的ハッシュ関数が必要

- 同じhash値を取るkeyの探索が困難(衝突困難性)
- 既知のkeyとhash値に対して同じhash値を取るkeyの探索が困難(第2原像計算困難性)
- 未知のkeyのhash値に対して同じhash値を取るkeyの探索が困難(原像計算困難性)

上ほど満たすのは難しく、下ほど破られたときのダメージが大きい

---

### レインボーテーブル攻撃
予め全てのhash値を計算したテーブルを用意して逆引きに使うことで高速にhash値からkeyを求める攻撃手法

回避策

- keyを長くする(salt)
- 何回もhash関数を食わせて計算量を増やす

一般にhash関数は計算量が少ないほど良いが、暗号学的hash関数では逆になる

---

### Hash関数の種類

- CRC(暗号学的でない)
- MD5(128bit)
- SHA-1(160bit)
- SHA-2(224/256/384/512bit)
- SHA-3/Keccak
- Blowfish(BCrypt)
