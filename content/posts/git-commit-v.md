---
title: "git commit -v で差分も一緒に表示する"
date: 2020-05-10T10:27:33+09:00
---

`git commit` では、差分ファイル名しか表示されない。

```sh

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch add/git-commit-v
# Changes to be committed:
#	new file:   content/posts/git-commit-v.md
#
```

`git commit -v` することで、差分の内容も表示される。

```sh

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch add/git-commit-v
# Changes to be committed:
#	new file:   content/posts/git-commit-v.md
#
# ------------------------ >8 ------------------------
# Do not modify or remove the line above.
# Everything below it will be ignored.
diff --git a/content/posts/git-commit-v.md b/content/posts/git-commit-v.md
new file mode 100644
index 0000000..10c019f
--- /dev/null
+++ b/content/posts/git-commit-v.md
@@ -0,0 +1,55 @@
+---
+title: "git commit -v で差分も一緒に表示する"
+date: 2020-05-10T10:27:33+09:00
+---
+
+`git commit` では、差分ファイル名しか表示されない。
+
+```sh
+
+# Please enter the commit message for your changes. Lines starting
+# with '#' will be ignored, and an empty message aborts the commit.
+#
+# On branch add/git-commit-v
+# Changes to be committed:
+#	new file:   content/posts/git-commit-v.md
+#
+```
+
+`git commit -v` することで、差分の内容も表示される。
+
+```sh
+
+# Please enter the commit message for your changes. Lines starting
+# with '#' will be ignored, and an empty message aborts the commit.
+#
+# On branch add/git-commit-v
+# Changes to be committed:
+#	new file:   content/posts/git-commit-v.md
+#
+# ------------------------ >8 ------------------------
+# Do not modify or remove the line above.
+# Everything below it will be ignored.
+diff --git a/content/posts/git-commit-v.md b/content/posts/git-commit-v.md
+new file mode 100644
+index 0000000..7a53ed0
+--- /dev/null
++++ b/content/posts/git-commit-v.md
+@@ -0,0 +1,15 @@
++---
++title: "git commit -v で差分も一緒に表示する"
++date: 2020-05-10T10:27:33+09:00
++---
++
++`git commit` では、差分ファイル名しか表示されない。
++
++```sh
++```
++
++`git commit -v` することで、差分の内容も表示される。
++
++```sh
++```
++
+```
+
```

