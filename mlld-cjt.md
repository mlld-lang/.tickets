---
id: mlld-cjt
status: open
deps: []
links: []
created: 2025-12-11T09:02:59.292539-08:00
type: task
priority: 2
---
# Consider removing prettyprint default

We prettyprinted all JSON output early on because mlld was simplistic and also completely string based. I think it is probably unnecessary to prettyprint by default, but I'm not sure about it yet.

It's very nice to just have any JSON output be immediately readable which is very hard for humans dealing with compact JSON — but given that most of the content is likely to be read by LLMs it's probably not necessary at all.

We could instead have a `| @pretty` built-in to allow that if the user wants it.


