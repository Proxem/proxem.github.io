---
layout: post
title:  "The Quest for "
date:   2015-11-17 11:43:56 +0100
categories: jekyll update
---
At Proxem, our clients ask us to extract information from e-mails, social medias, press articles, and basically any type of text you can imagine. In the standard case, the text to process is written in various languages. To establish systems that support a wide scale of languages and formats is one of the mission of our Research team.

Another goal of ours is to develop cross-lingual algorithms, that is algorithms which take as input texts in different languages and output an information computed on all those texts. For example on a task called sentiment analysis, which consists in detecting the "polarity" of a document ("is this document rather positive or negative?"), we want to implement a unique algorithm that would take as input sentences in English, Chinese, Spanish, etc and would compute a score. There are multiple reasons for us to aim at this. One is for simplicity sake. Indeed, we do not want to implement as many algorithms as languages we may have to handle. Another reason for that choice is that we want to leverage the important amount of available data for some languages to improve the accuracy on languages where data is rare.

A first step in this direction was to build a first representation layer that embeds words in a multidimensional space, which would then feed our algorithms. If you are familiar with word embeddings and especially its most famous implementation Word2Vec, well you must have realized that it is what we want :). Those "word vectors" have super nice properties:
- the Euclidian distance works on them like a "semantic distance" in the sense that e.g "beautiful" is closer to "pretty" than to "algorithm", which make them far more interesting than tradition bag-of-words;
- they capture semantic analogies: 
<div>$$\vec{king} - \vec{man} = \vec{queen} - \vec{woman}$$</div>

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
