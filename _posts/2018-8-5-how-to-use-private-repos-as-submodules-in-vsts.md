---
layout: post
title: How to Use Private Repos as Submodules in VSTS
tags: VSTS
permalink: /how-to-use-private-repos-as-submodules-in-vsts-fd69801e233f
excerpt_separator: <!--more-->
---
{% include image.html
src="buddhist-statue.jpg" caption="Copyright © Lex Li. A smiling Buddhist statue." width="512px" %}

Well, while public repos work perfectly on VSTS, you will find trouble when adding private repos as submodules in your master repo, because passing user credentials in your build pipeline would kill you if you don't pay enough attention. Hope this post helps you out.
<!--more-->

### Private Repo User Credentials

It is very important to learn the nature of user credentials of your private repos before moving on.

* VSTS supports SSH, OAuth token, and Personal Access Token.
* GitHub and other vendors have their own documentation.

In my case, I moved all my private repos to VSTS, so I only need to make a choice on the three types.

To keep things simple, I will use a single authentication type for all private repos (as submodules), and let's move on.

### Authentication via OAuth Token

By default, the agent uses an OAuth token to check out the master repo if it is private (in my case). Then same token can be reused to check out other private repos when they are submodules, if you

* Use HTTPS links for all such submodules.
* Use [the built-in check out submodule](https://docs.microsoft.com/en-us/vsts/pipelines/build/repository?view=vsts#authenticated-submodules) option.

This is probably the simplest way, but with limitations that master and submodules belong to VSTS. If the submodules come from different service providers, then this approach won't work.

### Authentication via SSH

It would be easy to achieve this if you use your own agent, which runs under a dedicate account, because

* You can create new SSH keys without password.
* Add the public key to all services (VSTS/GitHub and so on).
* Save `id_rsa` (private key) and known_hosts (white list) in `.ssh` folder of `%userprofile%` folder for this account.
* Use SSH links for all private submodules in your master repo.

Then the agent is smart enough to pick up the correct credentials and check out the repos.

If you cannot use your own agent, then you can follow the same trick from [this AppVeyor article](https://www.appveyor.com/docs/how-to/private-git-sub-modules/).

1. Store the key information as secrets in Variables.
1. Create tasks at the beginning of your pipeline to create id_rsa and known_hosts in %userprofile%\.ssh folder.
1. Delete the files when the pipeline finishes.

### Authentication via Personal Access Token

It is possible to use this approach but I didn't. So nothing is written here.

This concludes all the posts on my personal adventure migrating all sites to Azure.
