---
layout: page
permalink: /resume/
title: Resume
category: base
published: true
description: "Curriculum Vitae / Resume"
metatags:
  - cv
  - resume
  - "foo boo"
comments: false
imagesummary: foo.png
modified: "2016-02-13"
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    lastmod: 2016-02-13
style: |
  .container {
        max-width: 48rem;
    }
---

<span class="social-icons">
<a href="https://twitter.com/share?text=Curriculum Vitae - {{ site.owner.name }}&amp;url={{ site.url }}/cv&amp;via={{ site.owner.twitter }}"  class="social-icons" target="_blank" title="Share on twitter"> <i class="fa fa-twitter meta"></i></a>
<a href="{{ site.owner.linkedin }}" class="social-icons" title="LinkedIn profile"><i class="fa fa-linkedin"></i></a>
<a href="{{ site.homeurl }}/files/resume.pdf" class="social-icons" title="Printer friendly format"><i class="fa fa-print"></i></a>
</span>

{:.text-center}

<!-- Alternately, user html5 embed tag -->
<iframe src="{{ site.homeurl }}/files/resume.pdf" width="100%" style="height: 100vh;"></iframe>
