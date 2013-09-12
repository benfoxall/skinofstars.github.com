---
layout: post
title: Ruby On Rails, RSS and Atom feed parsing with Feed Normalizer and subsequent
  storage
categories:
- Programming &amp; Design
tags:
- open source
- rss
- ruby
- rubyonrails
status: publish
type: post
published: true
meta:
  _edit_last: '1'
  aktt_notify_twitter: 'no'
---
I've battled for days on this, but I now finally know how to parse feeds and store them in a database in Ruby On Rails. This won't be of much interest to the casual reader, but if you are scouring the web for an answer (as I was) then you will probably find this very useful:

{% highlight ruby %}
class Feed &lt; ActiveRecord::Base
require_association 'post'
require 'feed-normalizer'
require 'open-uri'
require 'rss/2.0'

belongs_to :user
has_many :posts, :dependent =&gt; :destroy

#put some other stuff here for feed validation etc

def refresh_all
    refresh(Feed.find(:all))
end

def refresh(feeds)
    feeds.each do |feed|
        rss = FeedNormalizer::FeedNormalizer.parse open(feed.uri)
        rss.entries.each  do |item|
            post = Post.new(:feed_id =&gt; feed.id)
            post.link = item.url or raise "post has no link tag"
            post.title = item.title or "no title"
            post.content = item.content or "no text"
            post.created_at = item.date_published if item.date_published
            post.save
        end
    end
end
{% endhighlight %}

end
