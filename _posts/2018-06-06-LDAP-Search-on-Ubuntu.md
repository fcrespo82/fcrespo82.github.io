---
layout: post
title:  "Search LDAP on Ubuntu"
tags: ldap english
---

This is a quick one to show how to search a LDAP server for a specific user.

Today I needed to gather some e-mails from my coworkers to configure an internal tool. My memory fails me with this kind of stuff and I needed a way to quickly find their e-mails (which are based on their uid on th LDAP server).

<!--more-->

Linux has a package named **ldap-utils** in which it has some useful tools to work with LDAP. The one I was interested was **ldapsearch**.

One quick look into its linux man page and I got this info from it (I removed the parts that are not interesting for this post):


`ldapsearch [-h ldaphost] [-x] filter [attrs...]`

- **[-h ldaphost]**: The LDAP server to look at;
- **-x**: Use simple authentication instead of SASL;
- **filter**: What to look for;
- **[attrs...]**: Which attributes you want to return.

```sh
ldapsearch -h server -x fullname="Fernando*Crespo" uid
```

The output of that command is very straight forward.

    # extended LDIF
    #
    # LDAPv3
    # base <> (default) with scope subtree
    # filter: fullname=Fernando*Crespo
    # requesting: uid 
    #

    # fernando, AREA, ORGANIZATION
    dn: cn=fernando,ou=AREA,o=ORGANIZATION
    uid: fernando

    # search result
    search: 2
    result: 0 Success

    # numResponses: 2
    # numEntries: 1


As I said in my last post, this blog will serve as my personal wiki for me to remember things as my memory is not that good. Hope I can bring some of my daily knowledge to some of you.
