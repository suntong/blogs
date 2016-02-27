---
title: "GoDoc from branch code"
date: "2016-02-26T00:07:33-05:00"
categories: ["Tech"]
tags: ["go","programming"]
---

<!--
# GoDoc from branch code

[category Tech][tags go,programming]
-->

[GoDoc](https://godoc.org/) does not support documenting branch code, because `go get` Does Not Work That Way<sup>TM</sup>. However, I do need to check the GoDoc result before messing with my git head. Solution?

<!--more-->

I found one from [Sharing Godoc of a WIP Branch](https://npf.io/2015/06/wip-godoc/), but it described in such a abstracted way that makes my first several attempts failed. So I'm documenting (the same thing) again in my own more-helpful but [no-sugar](http://suntong.github.io/blogs/2016/02/20/the-no-sugar-style/) style. 

0. Tag the branch you are working on. Note, to make it works, you have to tag it in the format of vN (e.g. v0, v1, v1.1.1, etc).

           git tag v0

0. Publish your new tag.

           git push origin v0

0. Check your published new tag. For my [`easygen`](https://github.com/suntong/easygen) project, I checked  
https://github.com/suntong/easygen/releases/tag/v0.

0. Visit your new tagged branch from GoDoc via [gopkg.in](https://gopkg.in). Again, for my `easygen` project, with `v0` tag, I visited https://godoc.org/gopkg.in/suntong/easygen.v0. Replace the *GithubUser*,  *repo* and tag names with your for your case of course. 

You will see you branch code (document) rendered properly on GoDoc.

BTW, the tag can easily be removed later (and reused if needed):

	git tag -d v0
	git push origin :refs/tags/v0

