---
layout: post
title: The Cost of Open Source Solutions, A Case Study on Red Hat and Docker
tags: Linux Docker Red-Hat
permalink: /the-cost-of-open-source-solutions-a-case-study-on-red-hat-and-docker-27f8cdad5969
excerpt_separator: <!--more-->
---
{% include image.html
src="winter-trees-2.jpg" caption="Copyright © Lex Li. Winter trees, Montreal." width="512px" %}

Open source movement changes how we produce software. Companies like Red Hat and Docker do make their source code publicly available, but using products from them is not free, and not even cheap. So if you do want to host an application on their platforms, learn how to calculate the cost is very important.
<!--more-->

### Red Hat

In the past decades, Red Hat has established its hierarchy of products. Individuals might use Fedora or CentOS which come without significant technical support, but enterprises might use Red Hat Enterprise Linux (RHEL).

RHEL users might purchase [subscriptions](https://www.redhat.com/en/about/subscription-model-faq), so as to enable access to the relevant RPM repos for patches and applications. Even access to the relevant documentation requires the same subscriptions.

So depending on your situation, you should calculate how many subscriptions are needed and then buy them before moving on.

For developers who want to evaluate RHEL, Red Hat is generous enough to grant you a subscription via [the developer program](https://www.redhat.com/en/about/press-releases/red-hat-expands-red-hat-developer-program-no-cost-red-hat-enterprise-linux-developer-subscription).

> I started to use this developer subscription in the past few days and finally learned many new things on RHEL.

### Docker

It is natural today to enable Docker on a Linux server. However, the cost of Docker engine is still a misery.

RHEL 7 does ship with a version of Docker, if you enable its Extras channel. The version number is 1.13.1, and Red Hat provides support according to [its Extras Product Life Cycle](https://access.redhat.com/support/policy/updates/extras/).

However, you should notice this is a very old version of Docker EE, as Docker Inc. has [the following releases](https://success.docker.com/article/maintenance-lifecycle),

* EE 18.09
* EE 18.03
* EE 17.06
* EE 17.03 (EOL 2018–03–01)
* CSE 1.13 (EOL 2018–02–07)

> Docker engine 1.13.1 matches CSE 1.13, so it is really old.

There does not seem to be a way to use newer Docker EE releases on RHEL from Red Hat documentation. Docker Inc. sells [its own subscription](https://docs.docker.com/install/linux/docker-ee/rhel/) for Docker EE, so that you can install EE 17.06/18.03/18.09 on RHEL 7.

You might wonder why not Docker CE (Community Edition), but the fact is that Docker Inc. explicitly states that

> Docker Community Edition (Docker CE) is not supported on Red Hat Enterprise Linux.

### Conclusion

Once we know the costs of RHEL and Docker EE, planning the budget should be easier.
