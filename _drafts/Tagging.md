---
layout: post
title: "How to implement proper blog tagging with Jekyll"
comments: true
summary: How to add tagging functionality to Jekyll blog.
tags: [jekyll,blogging]
---

<h2>Goals</h2>

I can't imagine blog without tagging. Unfortunatelly, out-of-the box tagging support in Jekyll is very limited. But Jekyll is very extensible which makes it possible to implement tagging.

My goals for the tagging in blog:

+ To be able to tag any post with any amount of tags which I can invent on-the-fly
+ To have a [list of all tagged posts grouped by tags]({{site.baseurl}}/tags/)
+ To print a list of tags under each tagged post. Every tag shall lead to a page with a list of posts with the same tag (see this page as an example)

<h3>Goal 1: tagging of the posts</h3>

It is really simple. Just add variable //tags// to every post (in the [YAML frontmatter](http://jekyllrb.com/docs/frontmatter/)) you would like to tag: 

{% highlight liquid %}
{% raw %}
tags: [tag1,tag2,tagN]
{% endraw %}
{% endhighlight %}

<h3>Goal 2: List of all tagged posts</h3>
This is also pretty simple. The following template will generate list of tags:

{% highlight liquid %}
{% raw %}
<h2>{{page.title}}</h2>

{% capture tags %}
  {% for tag in site.tags %}
    {{ tag[0] }}
  {% endfor %}
{% endcapture %}
{% assign sortedtags = tags | split:' ' | sort %}

{% for tag in sortedtags %}
  <h3 id="{{ tag }}">{{ tag }}</h3>
  <ul>
  {% for post in site.tags[tag] %}
    <li><a href="{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
  </ul>
{% endfor %}
{% endraw %}
{% endhighlight %}

(Idea is adapted from [stackoverflow answer](http://stackoverflow.com/a/21002505). Thanks, [Christian](http://stackoverflow.com/users/6884/christian-specht)!

[Generated page]({{site.baseurl}}/tags/) can now be added to your site.

<h3> Goal 3: print list of hyperlinked tags under post</h3>

This is the most complicated part.

First step is to follow the guide [Tags in Jekyll](http://charliepark.org/tags-in-jekyll/). At the end you will have a page with tagged articles for every tag.
I modified a little [tag_index.html](https://github.com/vitalyrepin/vrepinblog/blob/master/_layouts/tag_index.html) from Charlie's example.

Now we need to print proper hyperlinks at the end of the post. Add the following code in your [post template](https://github.com/vitalyrepin/vrepinblog/blob/master/_layouts/post.html):

{% highlight liquid %}
{% raw %}
{% comment %}
<!-- Getting and sorting tags alphabetically -->
{% endcomment %}

{% capture mytags %}
  {% for tag in page.tags %}
    {{ tag }}
  {% endfor %}
{% endcapture %}
{% assign sortedtags = mytags | split:' ' | sort %}

{% comment %}
<!-- Tags output: Name + link to the page with list of all the posts with the same tag -->
{% endcomment %}

{% for tag in sortedtags %}
<a href="{{ site.baseurl }}/tag/{{tag}}">#{{tag}}</a>
{% endfor %}
{% endraw %}
{% endhighlight %}

At first stage, tag names are sorted. At the second stage they are printed as hyperlinks.

That's it. Congratulations! We are having working setup with tags.

{% include twitter_plug.html %}