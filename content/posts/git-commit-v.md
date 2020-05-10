---
title: "git commit -v で差分も一緒に表示する"
date: 2020-05-10T10:27:33+09:00
---

`git commit` では、差分ファイル名しか表示されない。

```diff

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch add/git-commit-v
# Changes to be committed:
#	modified:   content/posts/git-commit-v.md
#
```

`git commit -v` することで、差分の内容も表示される。

```diff

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch add/git-commit-v
# Changes to be committed:
#	modified:   content/posts/git-commit-v.md
#
# ------------------------ >8 ------------------------
# Do not modify or remove the line above.
# Everything below it will be ignored.
diff --git a/content/posts/git-commit-v.md b/content/posts/git-commit-v.md
index 93e0931..0920f55 100644
--- a/content/posts/git-commit-v.md
+++ b/content/posts/git-commit-v.md
@@ -5,7 +5,7 @@ date: 2020-05-10T10:27:33+09:00
 
 `git commit` では、差分ファイル名しか表示されない。
 
-```sh
+```diff
 
 # Please enter the commit message for your changes. Lines starting
 # with '#' will be ignored, and an empty message aborts the commit.
@@ -18,7 +18,7 @@ date: 2020-05-10T10:27:33+09:00
 
 `git commit -v` することで、差分の内容も表示される。
 
-```sh
+```diff
 
 # Please enter the commit message for your changes. Lines starting
 # with '#' will be ignored, and an empty message aborts the commit.
```

