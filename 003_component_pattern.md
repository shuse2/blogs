ゲームプログラミングのパターンとして、
参考になりそうな文章があったので、日本語訳をしてみたいと思います。

引用元:
[http://gameprogrammingpatterns.com/component.html](http://gameprogrammingpatterns.com/component.html)


# Decoupling　Pattern - component pattern
目的

ひとつのクラスを、いろいろな箇所で使用しながら疎結合にしていく

##問題点1
なぜ、Decoupling PatternのComponentパターンが必要か。

例えば、マリオをつくろうとするとき、プレイヤーは、マリオを動かします。
プレイヤーの入力値を受け取りマリオの動きを表現していかないといけません。

その時に、マリオは台にのったという判定があるので、物理演算や衝突判定があります。
その判定をしたのちに、アニメーションや、音も入ってきます。

これは、かなりいろいろな処理が入ってます。
でも、ソフトウェア開発の授業の一番最初にドメインの違うプログラムは、
独立しているべきだと習いましたよね?
例えば、ワープロを作っているとしたら、プリント機能は、ドキュメントの保存やロードの
機能に影響を受けるべきではありません。
ゲームは、ビジネスソフトと同じようなドメインはありませんが、同じルールが適応されます。

AI,物理演算、レンダリング、音楽などのドメインは、なるべくお互いを知るべきではありません。
でも、このままでは、全ての処理がひとつのクラスに入ってしまい、5000行もあるひとつの
ソースファイルが出来てしまいます。
もう、誰にも手をつけることができなくなってしまいます。

##問題点2

ただ、ひとつのクラスが大きくなってしまう以外の問題もあります。
結合の問題です。

```c
if (collidingWithFloor() && (getRenderState() != INVISIBLE))
{
playSound(HIT_FLOOR);
}
```


上のような、ドメインの全く違うものを同じクラスに書く必要性がでてきます。
また、上記のプログラムを改変しようとするとき、変更する人は、
物理エンジン、レンダーとサウンド全てについて知る必要性があります。


このような2つの問題から、マリオクラスは、誰も触れないものになってしまいます。


###解決方法

このマリオクラスをドメインの違いによって切っていくことにより、
解決することが出来ます。

例えば、ユーザからの入力値を扱うものは"InputComponent"クラスに分けてしまいます。
そして、マリオクラスはこのInputComponentクラスを所有することになります。

それを全てのドメインに対して行い、マリオはただ全てを所有する殻のようなクラスに
することにより、問題点1の1クラスが大きくなりすぎる問題を解決することが出来ます。

しかし、解決出来たのはそれだけではありません。
マリオクラスは、PhysicsComponentやGraphicComponentを所持していますが、
Componentはお互いのことを知りません。

つまり、PhysicsComponentを変更する人は、Graphicのことを知らずに変更が出来ますし、
逆もまた然りになります。

しかし、現実的には、それぞれのコンポーネントは、ある程度関係性がある必要があります。
例えば、AIのコンポーネントは、物理エンジンのコンポーネントにマリオがどこに行こうとしているかを教える必要があります。
しかし、これは、コンポーネント同士が密に繋がる必要はなく、話しかける程度の繋がりであるべきです。


###その他の利点
その他の利点として、コンポーネント群が何度でも使えるという点です。
ここまでは、マリオに集中してきましたが、他の要素も考えてみましょう。

例えば、見えているだけでマリオと直接かかわりのない雲、茂みなどがあります。
また、飾りの中でも箱などの触れる物もあります。
さらに、見えないけど触れる物(隠しボックス)などもあります。

もし、コンポーネント方式を使っていない場合は、
以下の様なクラスツリーになります。

簡易クラス図


ゲームオブジェクトでは、共通要素の位置や方向などをもっています。
見えない箱は、それに加えて、衝突判定を持っています。
また、雲は同じように、ゲームオブジェクトを継承しレンダリングの方法を持っています。

箱は、見えない箱を継承し、物理判定を持っていますが、表示の方法は独自で実装しないといけません。
もし、雲の表示機能を使いまわそうとすると、多重継承を使い、雲も継承しないといけませんが、
これは、いろいろと問題が出てきてしまいます。


他にも雲と箱を継承関係にすることも出来ますが、今度は物理判定部分が重複してしまいます。

これを、コンポーネント方式で考えてみましょう。
サブクラスは、全てなくなります。

必要なのは、ゲームオブジェクト、物理コンポーネント、表示コンポーネントになります。
雲は、表示コンポーネントを持ったゲームオブジェクトであり、
見えない箱は物理判定を持ったゲームオブジェクトになります。

こうすることにより、コードの重複や多重継承は必要がなくなります。

コンポーネントは、基本的にプラグインのようなもので、もっているだけで簡単に使えるものになります。


## パターン

最初の全てが入ったクラス(マリオクラス)を機能のドメインごとに分割しコンポーネントクラスとします。
そして、最初の全てが入ったクラス(マリオクラス)はただの容器のようなコンテイナークラスとなります。


## いつ使うべきか

コンポーネントは、ゲームの特徴を表すコアクラスでよく見かけられます。
しかし、以下の様な時に、他の場所にも適応することが出来ます。

* ひとつのクラスが複数の機能ドメインを触るが、それぞれを疎結合にしておきたい場合。
* クラスが大きくなりすぎて、よくわからなくなってきた場合
* さまざまな、機能を持ったオブジェクトを作りたいが、継承では、コードの重複などが起きてしまう。


## 注意
このコンポーネントパターンは、単純に一つのクラスに全てをいれてしまうより、
複雑になってしまいがちです。
また、それぞれの抽象的なオブジェクトは、オブジェクトの集まりになり、インスタンス化、初期化や結合が
正しく行われなければいけません。
また、コンポーネント同士の対話やそれぞれのコンポーネントが占めるメモリーの計算はさらにむずかしい問題
となります。

プロジェクトが大きい場合は、この複雑性は、有効になることが多いですが、
存在しない問題にこの解答を当てるようなことがないように注意してください。


### Sample Code

上記の例で上がっているマリオクラスを例にとってみます。

まずは、全てがひとつに入っているコードを見てみましょう

```c
class Mario
{
public:
  void update(World& world, Graphics& graphics)
  {
    // Apply user input to hero's velocity.
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        velocity_ -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        velocity_ += WALK_ACCELERATION;
        break;
    }

    // Modify position by velocity.
    x_ += velocity_;
    world.resolveCollision(volume_, x_, y_, velocity_);

    // Draw the appropriate sprite.
    Sprite* sprite = &spriteStand_;
    if (velocity_ < 0) sprite = &spriteWalkLeft_;
    else if (velocity_ > 0) sprite = &spriteWalkRight_;

    graphics.draw(*sprite, x_, y_);
  }

private:
  static const int WALK_ACCELERATION = 1;

  int velocity_;
  int x_, y_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

毎フレームごとに呼ばれるudpate()というメソッドがあります。
ここで、ユーザからの入力を取得し、衝突判定やレンダリングをしています。

これを、ドメインごとにきりわけていきたいと思います。

まずはInputから。

```c
class InputComponent
{
public:
  void update(Mario& mario)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        mario.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        mario.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};
```

これを、元のコードに当てはめると

```c
class Mario
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);

    // Modify position by velocity.
    x += velocity;
    world.resolveCollision(volume_, x, y, velocity);

    // Draw the appropriate sprite.
    Sprite* sprite = &spriteStand_;
    if (velocity < 0) sprite = &spriteWalkLeft_;
    else if (velocity > 0) sprite = &spriteWalkRight_;

    graphics.draw(*sprite, x, y);
  }

private:
  InputComponent input_;

  Volume volume_;

  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};
```

このようになります。
この調子で、その他のコンポーネント作成すると以下のようになります。

```c
class GraphicsComponent
{
public:
  void update(Mario& mario, Graphics& graphics)
  {
    Sprite* sprite = &spriteStand_;
    if (mario.velocity < 0) sprite = &spriteWalkLeft_;
    else if (mario.velocity > 0) sprite = &spriteWalkRight_;

    graphics.draw(*sprite, mario.x, mario.y);
  }

private:
  Sprite spriteStand_;
  Sprite spriteWalkLeft_;
  Sprite spriteWalkRight_;
};


class PhysicsComponent
{
public:
  void update(Mario& mario, World& world)
  {
    mario.x += mario.velocity;
    world.resolveCollision(volume_, mario.x, mario.y, mario.velocity);
  }

private:
  Volume volume_;
};
```

そうすることにより、マリオクラスは以下のようになります。

```c
class Mario
{
public:
  int velocity;
  int x, y;

  void update(World& world, Graphics& graphics)
  {
    input_.update(*this);
    physics_.update(*this, world);
    graphics_.update(*this, graphics);
  }

private:
  InputComponent    input_;
  PhysicsComponent  physics_;
  GraphicsComponent graphics_;
};
```

こうすることにより、Marioクラスは2つのことをするようになります。

1. コンポーネントの保持
2. 共通変数の保持

さらに共通化へ

この時点では、まだマリオクラスは、自分の行動を知っているコンクリートクラスを知っています。

ですので、インターフェースを使い、さらにここから、分けていきます。
まずはInputから修正

```c
class InputComponent
{
public:
  virtual ~InputComponent() {}
  virtual void update(Mario& mario) = 0;
};



class PlayerInputComponent : public InputComponent
{
public:
  virtual void update(Mario& mario)
  {
    switch (Controller::getJoystickDirection())
    {
      case DIR_LEFT:
        mario.velocity -= WALK_ACCELERATION;
        break;

      case DIR_RIGHT:
        mario.velocity += WALK_ACCELERATION;
        break;
    }
  }

private:
  static const int WALK_ACCELERATION = 1;
};
```

そして、Marioクラスでは、inputComponentのポインタを保持するようにします。

ここまで来ると、Marioクラスは、Marioというものに関連するものは既にありません。
ですので、MarioクラスをGameObjectとして書き直すと以下の用になります。


```c
class GameObject
{
public:
  int velocity;
  int x, y;

  GameObject(InputComponent* input,
             PhysicsComponent* physics,
             GraphicsComponent* graphics)
  : input_(input),
    physics_(physics),
    graphics_(graphics)
  {}

  void update(World& world, Graphics& graphics)
  {
    input_->update(*this);
    physics_->update(*this, world);
    graphics_->update(*this, graphics);
  }

private:
  InputComponent*    input_;
  PhysicsComponent*  physics_;
  GraphicsComponent* graphics_;
};
```

そして呼び出すときには、それぞれのインターフェースを継承したコンポーネントを
以下のように入力してあげれば解決されます。


```c
GameObject* createMario()
{
  return new GameObject(new PlayerInputComponent(),
                        new BjornPhysicsComponent(),
                        new BjornGraphicsComponent());
}

```
## Design Decisions

もっとも大事な設計段階での質問は、どのようなコンポーネントが必要になるかです。
それは、どのようなゲームを作るかに依存します。
プロジェクトが大きく、複雑になるに従いコンポーネントも正確に分けていく必要があります。

また、コンポーネント別に全てを分けた後に、誰がそのコンポーネントを作成するかを決めなければいけません。

もし、オブジェクト自身が、コンポーネントを作るとしたら。
* オブジェクトが常に必要なコンポーネントを持っていることが保証されます。
必要なコンポーネントを忘れる可能性はありません。

* オブジェクトの改変が難しくなります。
このパターンの利点は、どんなオブジェクトでも、コンポーネントをつなぎ合わせることで作れますが、
その利点をフルに活用出来なくなります。

もし、外でコンポーネントの紐付けをするとしたら、
* オブジェクトはよりフレキシブルになります。
使用するコンポーネントを変更するだけで全く違った動きを実現することが出来ます。

* オブジェクトは、コンクリートクラスから完全に疎結合になることが出来ます。
そうすることにより、オブジェクトはインターフェースの存在しか知らず、
綺麗にカプセル化された設計が出来ることになります。

それぞれのコンポーネントの対話の仕方について

完全に独立された、コンポーネントは理想的ですが、現実にはそうはいきません。
その方法は、いくつかありますが、他のデザインパターンと違い、同時に使われることが多いです。


1. コンテイナーオブジェクトのステータスを変更する方法
* コンポーネントが疎結合のままになる。
* 共通のステータスは、コンテイナーオブジェクトに返還される必要がある。
* 対話が暗黙的にかつ順番に依存する。(表示させる前に物理演算をしないといけない)

ポジションやサイズなど基本的なものを共有することに向いています。

2. 直接お互いを見る
* 簡単かつ早い
* ふたつのコンポーネントが密につながっている(全て同じクラスに入っているよりはいい)

物理演算と衝突判定、アニメーションとレンダリングなど密に関係しているものに向いています。

3. メッセージ送信
例えば全てのコンポーネントの親クラスとして以下の様なコンポーネントクラスを作成しておきます。


```c
class Component
{
public:
  virtual ~Component() {}
  virtual void receive(int message) = 0;
};
```

そしてコンテイナーオブジェクトから以下のようにそれぞれのコンポーネントに対してメッセージを
送信します。


```c
class ContainerObject
{
public:
  void send(int message)
  {
    for (int i = 0; i < MAX_COMPONENTS; i++)
    {
      if (components_[i] != NULL)
      {
        components_[i]->receive(message);
      }
    }
  }

private:
  static const int MAX_COMPONENTS = 10;
  Component* components_[MAX_COMPONENTS];
};
```

* 兄弟コンポーネントは疎結合のままになります。
* コンテイナーオブジェクトが簡単になります。

記憶しておくほど重要でないものに向いています。
衝突判定時のサウンドの放射など


### 参考になりそうなもの

* UnityのGameObjectはコンポーネントベースで作られています。
* OpenソースのDelta3Dエンジンはこのパターンが実装されていて、GameActorとActorComponentという
クラスが存在します
* MicrosoftのXNAはコアクラスGame(FameComponent群を所持している)が同じような目的で実装されています。 
* このパターンは、GoFのStrategyパターンに似ています。



### 最後に

僕が、ゲームを作る上で参考になりそうな
[http://gameprogrammingpatterns.com/component.html](http://gameprogrammingpatterns.com/component.html)
の日本語訳をしてみました。

何か間違い等あれば、知らせて頂けると助かります。
