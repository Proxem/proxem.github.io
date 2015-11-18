---
layout: post
title:  "The Quest for Cross-Lingual Systems"
date:   2015-11-17 11:43:56 +0100
categories: jekyll update
---
At Proxem, our clients ask us to extract information from e-mails, social medias, press articles, and basically any type of text you can imagine. In the standard case, the text to process is written in various languages. To establish systems that support a wide scale of languages and formats is one of the mission of our Research team.

Another goal of ours is to develop cross-lingual algorithms, that is algorithms which take as input texts in different languages and output an information computed on all those texts. For example on a task called sentiment analysis, which consists in detecting the "polarity" of a document ("is this document rather positive or negative?"), we want to implement a unique algorithm that would take as input sentences in English, Chinese, Spanish, etc and would compute a score. There are multiple reasons for us to aim at this. One is for simplicity sake. Indeed, we do not want to implement as many algorithms as languages we may have to handle. Another reason for that choice is that we want to leverage the important amount of available data for some languages to improve the accuracy on languages where data is rare.

A first step in this direction was to build a first representation layer that embeds words in a multidimensional space, which would then feed our algorithms. If you are familiar with word embeddings and especially its most famous implementation Word2Vec, well you must have realized that it is what we want :). Those "word vectors" have super nice properties:

+ the Euclidian distance works on them like a "semantic distance" in the sense that e.g "beautiful" is closer to "pretty" than to "algorithm", which make them far more interesting than tradition bag-of-words

+ they seem to capture semantic analogies such as "a is to b what c is to d": 
<div>$$\vec{king} - \vec{man} \approx \vec{queen} - \vec{woman}$$</div>

+ and so on [TO DO]

So word embeddings are a very good start. The problem is, with the Word2Vec ot the GloVe implementations, you only get a set of different word embeddings for each language. What if we had just one set of word embeddings for all language? We wouldn't have to even worry about language detection. One property we would also like our word embeddings to have is that they conserve semantic distance throughout languages. For example, we want "pretty" to be close to "schön" and to "joli".

In a recent paper that we presented at EMNLP'15 in Lisboa, we introduced a simple and scalable method to achieve this goal called Trans-Gram, in reference to the Skip-Gram implementation from T. Mikolov. With this method we have aligned 21 languages in 2 and a half hours on CPU.

The core idea is to extend the Skip-Gram model to a multilingual setting. By analogy with Skip-Gram, the target word vectors are fitted to maximise their probability given their context, but here the source sentence is considered as the target word's context (and vice versa -- target sentence is a source word's context). Since the whole foreign sentence is considered as a context, there is no need in word-aligned data, any sentence-aligned parallel corpus can be used for training.

We also demonstrate some useful properties of the cross-lingual word embeddings: their ability to discriminate between different word senses by combining vectors from different languages, and their ability to align the linguistic features across languages.


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
