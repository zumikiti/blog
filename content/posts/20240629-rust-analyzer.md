---
title: "coc rust analyzer の設定でちょっとハマった件"
date: 2024-06-29T10:49:42+09:00
---

fish 環境で coc-rust-analyzer を使用しようとしたが、少しハマった件

```
[coc.nvim] Client Rust Analyzer Language Server: connection to server is erroring. Shutting down server.
[coc.nvim] Pending response rejected since connection got disposed
[coc.nvim] rust-analyzer client: couldn't create connection to server.
[coc.nvim] Server languageserver.rust-analyzer failed to start: Error: Pending response rejected since connection got disposed
[coc.nvim] Pending response rejected since connection got disposed
[coc.nvim] Rust Analyzer Language Server client: couldn't create connection to server.
[coc.nvim] Server rust-analyzer failed to start: Error: Pending response rejected since connection got disposed
[coc.nvim]: UnhandledRejection: undefined
[coc.nvim] Pending response rejected since connection got disposed
```

rust-analyzer をインストールした。

```sh
$ mkdir -p ~/.local/bin
$ curl -L https://github.com/rust-lang/rust-analyzer/releases/latest/download/rust-analyzer-x86_64-unknown-linux-gnu.gz | gunzip -c - > ~/.local/bin/rust-analyzer
$ chmod +x ~/.local/bin/rust-analyzer
```

https://rust-analyzer.github.io/manual.html#rust-analyzer-language-server-binary

変わらず。

```sh
$ rust-analyzer --version

error: Unknown binary 'rust-analyzer' in official toolchain 'stable-aarch64-apple-darwin'.
```

パスが通ってないようだ

```sh
$ alias rust-analyzer=~./.local/bin/rust-analyzer


$ rust-analyzer --version
rust-analyzer 0.3.2011-standalone (2fd803cc1 2024-06-22)
```

```sh
$ funcsave rust-analyzer
```

パスを通し、 :CocConfig で設定すると良いようなので、それもしてみた。

```json
{
  "languageserver": {
    "rust-analyzer": {
      "command": "rust-analyzer",
      "filetypes": ["rust"],
      "rootPatterns": ["Cargo.toml"]
    }
  }
}
```

だが、エラー出ている。

```
2024-06-29T10:31:38.336 ERROR (pid:34494) [language-client-client] - Server languageserver.rust-analyzer initialization failed. _ResponseError: Pending response rejected since connection got disposed
```

どうも rust-analyzer が起動できていないようだな。  
直接 rust-analyzer を呼ぶことは想定していないので、 CocConfig で直接指定してみた。

```json
{
  "languageserver": {
    "rust-analyzer": {
      "command": "~/.local/bin/rust-analyzer",
      "filetypes": ["rust"],
      "rootPatterns": ["Cargo.toml"]
    }
  }
}
```

.rs ファイルを開くと、ちゃんと動いているようだった。

![demo img](/img/20240629-rust-analyzer/rust-analyzer-demo.webp)

めでたし、めでたし。
