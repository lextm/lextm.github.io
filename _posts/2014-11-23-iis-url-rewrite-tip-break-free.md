---
layout: post
title: "IIS URL Rewrite Tip: Break Free"
tags: IIS
permalink: /iis-url-rewrite-tip-break-free-adccdb4b9f2f
excerpt_separator: <!--more-->
---
Some IIS URL Rewrite beginners usually wonder how to write the rules properly. Except learning regular expression, which is the key of rewrite rules, you should also pay enough attention to the rule engine and the special settings the engine provides.
<!--more-->

### Rule Engine Basic Flow
The rules are executed in a cascaded way, so that every rule is hit by default, each might modify the URL a little bit.
``` text
Incoming Requests
       |
       |-> Rule 1
             |
             |-> Rule 2
                   |
                   |-> Rule 3
                         |
                         |-> Handler
```

### The Challenge
OK, now let's try one complex case. Assume I have a site for cars. The following are the URLs I want to rewrite,

* `/benz`
* `/porsche`
* `/lincoln`
* `/general-motors`
* `/(Other car brands)`

I would like to redirect all visitors to the following pages instead,

* `/cars/benz`
* `/cars/porsche`
* `/cars/lincoln`
* `/cars/general-motors`
* `/cars/(other car brands)`

Well, I finished the rule in just a few minutes,

``` xml
<rewrite>
    <globalRules>
        <rule name="cars">
            <match url="(.*)" />
            <action type="Rewrite" url="cars/{R:0}" />
        </rule>
    </globalRules>
</rewrite>
```

However, due to a deal with General Motors I have to leave `/general-motors` untouched. This is where most beginners get puzzled. How can we achieve that goal?

### The Solution
Someone uses the below version,

``` xml
<rewrite>
    <globalRules>
        <rule name="cars">
            <match url="(.*)" />
            <action type="Rewrite" url="cars/{R:0}" />
        </rule>
        <rule name="GM">
            <match url="general-motors" />
            <action type="None" />
        </rule>
    </globalRules>
</rewrite>
```

Of course, it does not help at all. OK, another guy suggest that we should move the second rule to top.

``` xml
<rewrite>
    <globalRules>
        <rule name="GM">
            <match url="general-motors" />
            <action type="None" />
        </rule>
        <rule name="cars">
            <match url="(.*)" />
            <action type="Rewrite" url="cars/{R:0}" />
        </rule>
    </globalRules>
</rewrite>
```

Does it work? Nope. Then what's up?

Alright, in fact we just need the final bit to be added, like

``` xml
<rewrite>
    <globalRules>
        <rule name="GM" stopProcessing="true">
            <match url="general-motors" />
            <action type="None" />
        </rule>
        <rule name="cars">
            <match url="(.*)" />
            <action type="Rewrite" url="cars/{R:0}" />
        </rule>
    </globalRules>
</rewrite>
```

Isn't it simple? By setting "stopProcessing" to true we can ask the engine to break after executing this rule. To make it even clearer, let's draw a new flow diagram,

``` text
Incoming Requests
       |
       |-> Rule 1----------------------|
             |                         |
             |-> Rule 2                |
                   |                   |
                   |-> Rule 3          |
                         |             |
                         |-> Handler <-|
```

So unless a rule is the last in the list, you can feel free to stop processing from there.

By utilizing this feature, you should be able to handle complex scenarios with confidence.

Good luck.
