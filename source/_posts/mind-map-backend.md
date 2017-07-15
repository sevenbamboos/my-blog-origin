---
title: Mind Map of Backend
date: 2017-07-05 11:09:56
categories: Mind Map
---

Backend tends to have a totally different topics than Web (frontend). What's more, before putting too many items into "Mind Map of Web", creating a new mind-map slot for backend is quite reasonable. Additionally, classical topics about algorithm are also appropriate here for the reason of an obvious different focus from frontend. So all these facts bring about a new mind map for backend.  

# Map

Keywords | Comment & Reference
--- | ---
distributed system | Nginx (read as "engine X") [Concepts of Distributed System][1]
dynamic programming | [Comment](#dynamic-programming), [introduction by comic][2]
compile | [compile introduction][3]
bitwise operation | [is-power-of-2][4], [find-odd-number][5]
greatest common divider | [introduction by comic][6]
max difference within array | [introduction by comic][7]
circular linked list detect | Given node length known [introduction by comic][8]
project template | [link][9]
git merge rebase fixup autosquash feature branch | see my post "Feature Branch Workflow"

<!-- more -->

# dynamic programming
Regarding gold mine puzzle, the key is the generate function of `F(n, w) = max(F(n-1, w), F(n-1, w-p[n-1]) + g[n-1])`.
See implementation at [my git](https://github.com/sevenbamboos/alg-js)

[1]: https://mp.weixin.qq.com/s/5wxIHNMN2MbbSQ6WWA65HQ 
[2]: https://mp.weixin.qq.com/s?__biz=MjM5OTA1MDUyMA==&amp;mid=2655438647&amp;idx=1&amp;sn=4634f712fa4d0236aba60b8e8b7cc2cb&amp;chksm=bd730b408a048256f204695598c0e4f74e75c9582f5b9c740057a69747b306de1a4c308d5388&amp;mpshare=1&amp;scene=1&amp;srcid=0702N84baxNAmMFheg6Ck26Z&amp;key=238113c46368 
[3]: http://fullstack.blog/2017/06/24/%E5%A4%A7%E5%89%8D%E7%AB%AF%E5%BC%80%E5%8F%91%E8%80%85%E9%9C%80%E8%A6%81%E4%BA%86%E8%A7%A3%E7%9A%84%E5%9F%BA%E7%A1%80%E7%BC%96%E8%AF%91%E5%8E%9F%E7%90%86%E5%92%8C%E8%AF%AD%E8%A8%80%E7%9F%A5%E8%AF%86/?nsukey=9G4zvSOVCA0b49Ng4rhqNHkw55dE1OsVGQ3exvS12Nfchm8N38y5eU8Aw2VaHY72/yPT0cyMQK2ewKJjU6F0EqDj3rkpTv/EFl10CW6zk/29dY8DlDLjxh0YRyQFZvmUN4xWwW3e16mpXMDjKu4LVje6cwykadEMWU4klPXFOXWYLBZKqc1ocxBZCnTAH7ZF 
[4]: http://blog.jobbole.com/107689/ 
[5]: http://blog.jobbole.com/106521/ 
[6]: http://blog.jobbole.com/106315/
[7]: http://blog.jobbole.com/108594/ 
[8]: http://blog.jobbole.com/106227/
[9]: https://github.com/wearehive/project-guidelines#readme  
