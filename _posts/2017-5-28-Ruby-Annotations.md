---
layout: post
author: Xuanrui
title: Ruby annotations in Markdown
location: Somerville, MA
---

<ruby><rb>早上好</rb><rp>(</rp><rt>zǎo shàng hǎo</rt><rp>)</rp></ruby>！Everyone knows it's a nightmare to write ruby annotations,
especially when you need to write a lot of them. It's necessary to have some
"preprocessing" plugin if you're going to write a lot of them.

The Japanese Language Stack Exchange made a great attempt to this, and they
actually [hacked](https://gist.github.com/cyphr/6536814) Stack Exchange's
Markdown engine quite a bit so as to support ruby annotations. Unfortunately,
the code isn't well documented, and reading it makes my head hurt. Anyway, I
can't hack GitHub's Jekyll distribution after all.

The only solution was thus a simple script that compiles Markdown to, well,
GitHub-flavored Markdown; to be specific, it transforms some shorthand to
HTML ruby annotations.

The obvious way to do this is RegEx matching and substitution. A RegEx to match
my shorthand wasn't difficult - after all, it's my design and I could change it
any time to make it RegEx friendly. The main problem is that regex substitutions
are natively context (i.e., original text)-sensitive in neither Ruby nor Python
(my two scripting languages), so I had to hack it.

Fortunately, the hack was not too difficult. The hack was to generate a list of
"original texts" *a priori*, and then loop through the text with the information
at hand, substituting one at a time. Well, it's not efficient, but as I'm not going
to write a one hundred page book with Markdown it's OK.

The bigger problem is, though, that Ruby annotations have to be fixed. Their
syntax is way too writer-unfriendly, and should not have been meant to be
handwritten. But after all, HTML is meant for the browser, so there isn't really
anything to complain.  
