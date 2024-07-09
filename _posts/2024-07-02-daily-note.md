---
title: 調べたものとか
date: 2024-07-02
---

## OpenSSHのヤバイ脆弱性「regreSSHion」が発見される。リモートから認証なしのコード実行が可能 | ソフトアンテナ https://softantenna.com/blog/openssh-regre-sshion/

root権限の任意コード実行バグがリグレッションしたらしい。
OpenSSH 8.5p1以降のバージョンはリグレッションしてるので、9.8以降にアップデートすべき。
`/etc/ssh/sshd_config`を編集し、`LoginGraceTime 0`にすると問題を回避できる。

## 時間が倍速で流れる錯覚をした話 - 覚書 https://satoru-takeuchi.hatenablog.com/entry/2024/06/29/081145

不思議なアリス症候群が時間について起きた体験談。

僕も数ヶ月に一回、自分が外界に比べて３倍ぐらい大きくなるような視覚上の問題が起きる。
大体2、3分で収まり、スマホを見たり本を読んだりしているときによく起こる。
僕の場合、向精神薬を飲んでいるので、それが影響しているのだろう。

向精神薬による幻覚でいうと、寝入りばなに幽霊をよく見るようになった。
おそらく疲労に伴い薬の幻覚作用が出てしまっているのだろう。
かなりリアリティのある幻覚で、視覚だけでなく触覚もある。
今のところ、嗅覚や聴覚には影響していない。
このメカニズムを完全に解明すれば、ものすごく高性能なAR技術が確立するはずだ。

## Programming languages resources | Max Bernstein https://bernsteinbear.com/pl-resources/

プログラミング言語処理系を作る際の情報源まとめ。
広範な情報が整理されている。

## June 2024 dev update https://godot-rust.github.io/dev/june-2024-update/

GodotでRustを使えるようにするプロジェクトの最新情報。

Godotは独自のスクリプト言語であるGDScriptの他に、C#とC/C++（GDExtension）もサポートしている。
また、GDExtensionのFFIバインディングとして、コミュニティベースでいくつか言語のサポートが試みられている、という状況らしい。

GDScript（というかGodot）のメモリ管理は参照カウント方式。
ゲームとGCの組み合わせは鬼門で、そういう意味でもRustバインディングは相性が良かったりしそう。

## samber/oops: 🔥 Error handling library with context, assertion, stack trace and source fragments https://github.com/samber/oops

Goのエラーハンドリングライブラリ。
ロガーにエラーのコンテキスト情報を与えたいパターンで、ロギング時にコンテキスト情報を構築するのではなく、エラーに直接アノテーションできるようにする。

Motivating example:

```go
func c(token string) error {
    userID := ...   // <-- How do I transport `userID` and `role` from here...
    role := ...

    // ...

    return fmt.Errorf("an error")
}

func b() error {
    // ...
    return c()
}

func a() {
    err := b()
    if err != nil {
        // print log
        slog.Error(err.Error(),
            slog.String("user.id", "????"),      // <-- ...to here ??
            slog.String("user.role", "????"),    // <-- ...and here ??
            slog.String("stracktrace", generateStacktrace()))  // <-- this won't contain the exact error location 😩
    }
}
```

```go
func d() error {
    return oops.
        Code("iam_missing_permission").
        In("authz").
        Tags("authz").
        Time(time.Now()).
        With("user_id", 1234).
        With("permission", "post.create").
        Hint("Runbook: https://doc.acme.org/doc/abcd.md").
        User("user-123", "firstname", "john", "lastname", "doe").
        Errorf("permission denied")
}

func c() error {
	return d()
}

func b() error {
    // add more context
    return oops.
        In("iam").
        Tags("iam").
        Trace("e76031ee-a0c4-4a80-88cb-17086fdd19c0").
        With("hello", "world").
        Wrapf(c(), "something failed")
}

func a() error {
	return b()
}

func main() {
    logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))

    err := a()
    if err != nil {
        logger.Error(
            err.Error(),
            slog.Any("error", err), // unwraps and flattens error context
        )
    }
}
```
