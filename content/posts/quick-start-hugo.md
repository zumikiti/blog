---
title: "HUGO を Netlify へデプロイする"
date: 2019-11-28T19:20:10+09:00
draft: fales
tag: [hugo, netlify, golang]
---

## HUGO をインストール
```sh
▶ brew install hugo
▶ hugo version
```

## プロジェクトを作る

{{< highlight shell >}}
▶ hugo new site blog
Congratulations! Your new Hugo site is created in /Users/teke/Cord/blog.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
{{< /highlight >}}


これで以下ディレクトリが作成される。
```
▶ tree   
.
├── archetypes
│   └── default.md
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes

6 directories, 2 files
```

## テーマを適用する
```sh
▶ cd blog

▶ git init

▶ git submodule add https://github.com/vividvilla/ezhil themes/ezhil
```

`config.toml` に追記する。
```sh
themes = "ink"
```

