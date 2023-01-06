---
title: "GoでCSVの値を置き換える"
date: 2023-01-01T10:52:36+09:00
---

以下のように流れ
- インプット用のCSVをプロジェクトルートに置く
- プログラムを実行
- プロジェクトルートに `output.csv` というファイル名で出力される


## 元のCSV
```csv
ID,担当者,優先度,タイトル,...
```

## 出力したいCSV
```csv
課題名,担当者,プロジェクト,...
```

## main.go

```go
package main

import (
	"encoding/csv"
	"fmt"
	"os"
	"strings"

	"golang.org/x/text/encoding/japanese"
	"golang.org/x/text/transform"
)

func main() {
	var fileName string
	fmt.Println("issues.csv 以外のファイル名の場合は、ファイル名を入力してください。")
	fmt.Scanf("%s", &fileName)

	if fileName == "" {
		fileName = "issues.csv"
	}

	file, err := os.Open(fileName)
	if err != nil {
		fmt.Println(err)
	}

	output_file, err := os.Create("output_file.csv")
	if err != nil {
		fmt.Println(err)
	}
	defer output_file.Close()

	reader := csv.NewReader(transform.NewReader(file, japanese.ShiftJIS.NewDecoder()))
	reader.LazyQuotes = true
	records, _ := reader.ReadAll()

	// writer := csv.NewWriter(transform.NewWriter(output_file, japanese.ShiftJIS.NewEncoder()))
	writer := csv.NewWriter(output_file) // utf-8
	writer.UseCRLF = true

	// ヘッダーの設定
	writer.Write([]string{
		"課題名",
		"担当者",
		"プロジェクト",
		// ...
	})

	for i, record := range records {
		if i == 0 {
			continue
		}

		new_recode := make([]string, 17)

		// 固定値があれば設定
		new_recode[2] = "ほげプロジェクト"

		for vi, v := range record {
			// 課題名
			if vi == 2 {
				new_recode[0] = v 
				continue;
			}

			if vi == 1 {
				switch v {
				case "田中 太郎":
					new_recode[1] = "田中A"
				case "田中 次郎":
					new_recode[1] = "田中B"
				}
				continue
			}
		}

		writer.Write(new_recode)
	}

	writer.Flush()
	fmt.Println("output_file.csv に書き出しました。")
	fmt.Println("Fin.")
}
```
