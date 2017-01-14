# 【rust】rustをインストールして実行する

<http://qiita.com/_likr/items/daf46d6f66bc31cc4810>様の記事を実行してみる。  

## env

* max os X El Capitan (10.11)
* zsh

## installation rustup

<https://users.rust-lang.org/t/compiling-to-the-web-with-rust-and-emscripten/7627>  
<https://www.rust-lang.org/en-US/install.html>

```
curl https://sh.rustup.rs -sSf | sh -s -- --default-toolchain nightly
```

コマンドにしました。後ろについてるtoolchainはのちのち`nightly`にするので、取得する段階で指定しちゃう

## パス通す

`.zshprofile`などに、

```
export PATH="$HOME/.cargo/bin:$PATH"
```

を追加。

## 確認

```
$ rustc --version
rustc 1.14.0 (e8a012324 2016-12-16)
```

### 補完スクリプト

`$ rustup help completions` を見ると、completion scriptsをつくってあげるよ、bashとfishとzsh対応してるからさ！  って言われる。待て私はビビっている。

でも言われた通りにしちゃう

```
$ mkdir ~/.zfunc
$ rustup completions zsh > ~/.zfunc/_rustup
```

これで自動的にcompletion scriptsを作ってくれるので、自分の`.zshrc`に追加する。`compinit`の直前に。

```
`fpath+=~/.zfunc`
```

そしてzshの再起動。

結果

```
% rustup
completions          -- Generate completion scripts for your shell
component            -- Modify a toolchain's installed components
default              -- Set the default toolchain
doc                  -- Open the documentation for the current toolchain
help                 -- Prints this message or the help of the given subcommand(s)
install      update  -- Update Rust toolchains
man                  -- View the man page for a given command
override             -- Modify directory toolchain overrides
run                  -- Run a command with an environment configured for a given toolchain
self                 -- Modify the rustup installation
set                  -- Alter rustup settings
show                 -- Show the active and installed toolchains
target               -- Modify a toolchain's supported targets
telemetry            -- rustup telemetry commands
toolchain            -- Modify or query the installed toolchains
which                -- Display which binary will be run for a given command
```

## asmjsとwasm

```
$ rustup target add asmjs-unknown-emscripten
$ rustup target add wasm32-unknown-emscripten
```

## toolchainをnightlyにする

よくわかってないけど、curlやsourceを使う代わりに、nightly toolchainを使うように設定しなよって言わてる。インストール時にやってないと、やることになる

```
$ rustup default nightly
```

### 結果

**before**

```
% rustup show
Default host: x86_64-apple-darwin

installed targets for active toolchain
--------------------------------------

asmjs-unknown-emscripten
wasm32-unknown-emscripten
x86_64-apple-darwin

active toolchain
----------------

stable-x86_64-apple-darwin (default)
rustc 1.14.0 (e8a012324 2016-12-16)
```

**after**

```
% rustup show
Default host: x86_64-apple-darwin

installed toolchains
--------------------

stable-x86_64-apple-darwin
nightly-x86_64-apple-darwin (default)

active toolchain
----------------

nightly-x86_64-apple-darwin (default)
rustc 1.16.0-nightly (2782e8f8f 2017-01-12)
```

## installation Emscripten

```
$ curl -O https://s3.amazonaws.com/mozilla-games/emscripten/releases/emsdk-portable.tar.gz
$ tar -xzf emsdk-portable.tar.gz
$ source emsdk_portable/emsdk_env.sh
$ emsdk update
$ emsdk install sdk-incoming-64bit
$ emsdk activate sdk-incoming-64bit
```

`cmake`がないと`emsdk install`でコケるので、`brew install cmake`しておく。あとpythonとnodeも必要。  sdkのインストールは少し時間かかる

### Emscriptenとは

> EmscriptenはC/C++言語からLLVMを生成し、それをJavaScriptに変換するコンパイラのことです。  
C言語の標準ライブラリやPOSIXの一部もサポートし、OpenGL ES2.0も使えるそうです。  
Emscripten自体は下記にあります。  
<https://github.com/kripken/emscripten>


## Running some code

**hello.rs**

```
fn main() {
  println!("Hello, Emscripten!");
}
```


### asm.js

```
$ rustc --target=asmjs-unknown-emscripten hello.rs
$ node hello.js
```

### wasm

```
$ rustc --target=wasm32-unknown-emscripten hello.rs
$ node hello.js
```

htmlで`<script src="hello.js"></script>` しても確認できる。コンパイルにも結構時間がかかるのと、できあがったファイルの大きさにびっくりしたのは内緒


