---
layout: post
title:  "Adding disqus ( is easy )"
date:   2017-09-13 21:00:00 +0200
tags:
    - jekyll
---

I'll just pretend my last post was last week and proceed as if nothing happened, I'll also spare you the bad excuses why I didn't stick to my self-proclamed new schedule.

This time a little post about how incredibly easy it is to actually add disqus to your default jekyll site with the minima theme.

1. Create a disqus account at [the disqus website][disqus]
2. Confirm your e-mail address
3. create a _layout directory in the root of your jekyll site
4. create and fill posts.html in your newly created directory
5. add your disqus shortname to *_config.yaml* ( you can find your shortname on the [disqus settings page][disqus-settings])

That's it!

posts.html is simply the same as in the gem, except with an added variable called comments in YAML Front Matter. I'm adding it below for easy copy / pasting.

{% highlight html linenos %}{% raw %}
---
layout: default
comments: true
---
<article class="post" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title" itemprop="name headline">{{ page.title | escape }}</h1>
    <p class="post-meta">
      <time datetime="{{ page.date | date_to_xmlschema }}" itemprop="datePublished">
        {% assign date_format = site.minima.date_format | default: "%b %-d, %Y" %}
        {{ page.date | date: date_format }}
      </time>
      {% if page.author %}
        • <span itemprop="author" itemscope itemtype="http://schema.org/Person"><span itemprop="name">{{ page.author }}</span></span>
      {% endif %}</p>
  </header>

  <div class="post-content" itemprop="articleBody">
    {{ content }}
  </div>

  {% if site.disqus.shortname %}
    {% include disqus_comments.html %}
  {% endif %}
</article>

{% endraw %}{% endhighlight %}

A little trick I had to figure out to show the above block without actually rendering some jekyll logic as well is making use of the raw tag like this, put the highlight and raw tags on the same line, otherwise it won't work.

{% highlight html linenos %}{% raw %}
  {% highlight %}{% raw %}
    <html>yourhtmlhere</html>
    <p> the '-' in the next two tags are there to not break the block <p>
  {% end-raw %}{% end-highlight %}
{% endraw %}{% endhighlight %}

[disqus]: https://disqus.com/
[disqus-settings]: https://disruptive-cloud.disqus.com/admin/settings/general

