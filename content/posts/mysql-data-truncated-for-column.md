---
title: "Mysql Data Truncated for Column"
date: 2020-05-07T10:45:53+09:00
---

`Data truncated for column 'hoge'` というエラーが `insert` コマンドを実行した際に発生した。

## 結論
`enum` の範囲外の値を設定しようとした場合に出るエラー  
そのため、`values` の値を見直しましょう。
