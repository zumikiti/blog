---
title: "Git_fetch_p"
date: 2019-12-20T08:18:11+09:00
tags: ['git']
---

GitHub などで master ブランチにマージされて、元々のブランチは削除された。

{{< figure src="/img/git-fetch-p/github-branch.png" title="リモートブランチの状態" >}}

しかし、手元で `git fetch` しても `git branch --all` には消されたはずのブランチが残ったまま。
{{< figure src="/img/git-fetch-p/git-branch-all-1.png" title="消したはずのブランチが残っている状態" >}}

## 解決方法
`git fetch -p` を実行する。

{{< figure src="/img/git-fetch-p/git-branch-all-2.png" title="消したはずのブランチが残っている状態" >}}

なので、`alias fetch='git fetch -p'` と設定すればOK！

## -p の公式の記載
> -p  
> --prune  
>     Before fetching, remove any remote-tracking references that no longer exist on the remote. Tags are not subject to pruning if they are fetched only because of the default tag auto-following or due to a --tags option. However, if tags are fetched due to an explicit refspec (either on the command line or in the remote configuration, for example if the remote was cloned with the --mirror option), then they are also subject to pruning. Supplying --prune-tags is a shorthand for providing the tag refspec.  
>     See the PRUNING section below for more details.  
>
> https://git-scm.com/docs/git-fetch#Documentation/git-fetch.txt--p
