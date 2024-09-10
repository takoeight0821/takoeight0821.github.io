---
title: Rust向け組み込みスクリプト言語Rhaiについて調べる
date: 2024-07-09
---

[Rhai – Embedded Scripting for Rust](https://rhai.rs/)
> A small, fast, easy-to-use scripting language and evaluation engine that integrates tightly with Rust. Builds for most common targets including no-std and WASM.

元々[ChaiScript]という、C++向けの組み込み言語がある。
RhaiはChaiScriptのRust向けポートから始まった。

[Rhai Playground](https://rhai.rs/playground/stable/)

```rhai
fn run(a) {
    let b = a + 1;
    print("Hello world! a = " + a);
}
run(10);
```

Cargoのfeaturesフラグを使って、言語機能のopt-in/opt-outができる。
例えば、以下のようなコンフィグであれば、全ての値が`Send + Sync`になり、安全性チェックが無効化され、整数は`i32`に限定、浮動小数点数はなく、外部モジュールのロード、関数定義の機能が無効化される。
```toml
rhai = { version = "1.18.0", features = [ "sync", "unchecked", "only_i32", "no_float", "no_module", "no_function" ] }
```
