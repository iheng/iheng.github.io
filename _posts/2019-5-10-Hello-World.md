---
layout: post
title: Ruby symbol vs string!
published: true
---

Ruby has this unique datatype called symbol. it's very simlar to string. but there is one important difference it is immutable. The second thing is it share its memory with other symbol if they have same name. for Example: if you define :username. later you define again this symbol :username. it's the same object id ,which means it share the same memory with the symbol we defined early.

