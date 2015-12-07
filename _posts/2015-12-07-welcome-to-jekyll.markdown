---
layout: post
title:  "The Quest for Cross-Lingual Systems"
date:   2015-12-07 11:43:56 +0100
categories: jekyll update
---

At Proxem, our clients ask us to extract information from e-mails, social medias, press articles, and basically any type of text you can imagine. In the standard case, the text to process is written in various languages. To establish systems that support a wide scale of languages and formats is one of the mission of our Research team.

Another goal of ours is to develop cross-lingual algorithms, that is algorithms which take as input texts in different languages and output an information computed on all those texts. For example on a task called sentiment analysis, which consists in detecting the "polarity" of a document ("is this document rather positive or negative?"), we want to implement a unique algorithm that would take as input sentences in English, Chinese, Spanish, etc and would compute a score. There are multiple reasons for us to aim at this. One is for simplicity sake. Indeed, we do not want to implement as many algorithms as languages we may have to handle. Another reason for that choice is that we want to leverage the important amount of available data for some languages to improve the accuracy on languages where data is rare.

A first step in this direction was to build a first representation layer that embeds words in a multidimensional space, which would then feed our algorithms. If you are familiar with word embeddings and especially its most famous implementation Word2Vec, well you must have realized that it is what we want :). Those "word vectors" have super nice properties:

+ the Euclidian distance works on them like a "semantic distance" in the sense that e.g "beautiful" is closer to "pretty" than to "algorithm", which make them far more interesting than traditional bag-of-words

+ they seem to capture semantic analogies such as "*a* is to *b* what *c* is to *d*": \\[ \vec{king} - \vec{man} \approx \vec{queen} - \vec{woman} \\]

+ they are learned in a completely unsupervised way.

<br>

---

<br>

**From \\( \vec{king} - \vec{man} \approx \vec{queen} - \vec{woman} \\) to \\( \vec{rey}\_{es} - \vec{Mann}\_{de} \approx \vec{regina}\_{it} - \vec{femme}\_{fr} \\)**

So word embeddings are a very good start. The problem is, with current implementations, you only get a set of different word embeddings for each language. What if we had just one set of word embeddings for all language? We wouldn't have to even worry about language detection. One property we would also like our word embeddings to have is that they conserve semantic distance throughout languages. For example, we want "pretty" to be close to "schön" in German and to "joli" in French.

In a recent paper that we presented at EMNLP'15 in Lisboa, we introduced a simple and scalable method to achieve this goal called [Trans-Gram][article] in reference to the Skip-Gram implementation from T. Mikolov. With this method we have aligned 21 languages in 2 and a half hours on CPU for 40-dimensional vectors and about 10 hours for 300-dimensional vectors.

The core idea is to extend the Skip-Gram model to a multilingual setting. By analogy with Skip-Gram, the target word vectors are fitted to maximise their probability given their context, but here the source sentence is considered as the target word's context (and vice versa -- target sentence is a source word's context). Since the whole foreign sentence is considered as a context, there is no need in word-aligned data, any sentence-aligned parallel corpus can be used for training.

![Minion](/images/graph.png)

To illustrate our word vectors, we printed the top ten words closest to the word "innovation" in French:

![Minion](/images/innovation.png)

Here is also a tSNE that illustrate the alignement between words throughtout languages:

![Minion](/images/tsne.png)

**Interesting properties**

We also demonstrate some useful properties of the cross-lingual word embeddings: their ability to discriminate between different word senses by combining vectors from different languages, and their ability to align the linguistic features across languages.

We call the first task “cross-lingual disambiguation”. The goal of this task is to find a suitable representation for each sense of a given polysemous word. The idea of our method is to look for a language in which the undesired senses are represented by unambiguous words and then to perform some arithmetic operation on their corresponding embeddings. 

Let’s illustrate the process with a concrete example: consider the French word “train”. The three closest Polish words to $\vec{train}_{fr}$ translate in English into “now”, “a train” and “when”. 

This seems like a poor matching. Indeed, "train" in French is polysemous. It can refer to a line of railroad cars, but it can also be used to form progressive tenses. The French sentence “Il est *en train de* manger” translates into “he is *eating*”, or in Italian “*sta* mangiando”. As the Italian word “sta” is used to form progressive tenses, it’s a good candidate to disambiguate "train" in French. Let’s introduce the vector 
\\( \vec{v} \approx \vec{train}\_{fr} - \vec{sta}\_{it} \\).

Now the three Polish words closest to \\(\vec{v} \\)  translate in English into “a train”, “a train” and “railroad”. Therefore \\(\vec{v} \\) is a better representation for the railroad sense of "train":

![Minion](/images/lingtransfer.png)

Another interesting property of the vectors generated by Trans-Gram is the transfer of linguistic features through a pivot language that does not possess these features. Let’s illustrate this by focusing on Latin languages, which possess some features that English does not, like rich conjugations. For example, in French and Italian the infinitives of “eat” are "manger" in French and "mangiare" in Italian, and the first plural persons are "mangeons" in French and "mangiamo" in Italian. Actually in our models we observe the following alignments: \\( \vec{manger}\_{fr} \approx \vec{mangiare}\_{it} \\) and \\( \vec{mangeons}\_{fr} \approx \vec{mangiamo}\_{it} \\). It is thus remarkable to see that features not present in English match in languages aligned through English as the only pivot language. We also found similar transfers for the genders of adjectives and are currently studying other similar properties captured by Trans-gram.

**Byte-level Processing: The Shape of Things to Come?**

We use those word embeddings for our classification and sequence-to-sequence labelling tasks such as Named Entity Recognitions, Part-of-Speech Tagging, etc. This allows us to transfer the information we have on certain languages to other languages.

Now, words are a first, pragmatic step. This is not yet the most fundamental step to achieve cross-lingual systems “from scratch”, which is what we aim for. Indeed, feature engineering in NLP is similar to open Pandora’s Box: you don’t know if there will ever be an end to it. Think about it: multiply the number of specificities of every language with the specificities of every language register and with the specificities of every type of text (resumes, tweets, e-mails, legal articles, etc.). Now imagine how fast words appear and disappear in common language. You will end up soon giving up any hope of having a consistent set of rules for feature engineering that could scale. With aligned word embeddings, you can at least avoid the problem of spelling mistakes and ever expanding vocabularies throughout languages.

Yet, a limitation of word embeddings is that in order to extract global information about a sentence, you may need to have information about morphemes or even the characters that form a word.While we showed evidence that word embeddings seem to capture those informations, we feel that we need a more fundamental level of processing in order to do NLP completely from scratch, to quote [Collobert][collobert]’s and [Zhang][zhang]’s words. Hence, we are also interested in character-level embeddings and even byte-level approaches, in order to capture very specific characters. Our intuition about the interest to go deeper into byte-level approaches was in part satisfied by the reading of a recent paper: [Multilingual Processing from Bytes][bytes].

Of course, you would still need to expand from a good representation of a byte to a good representation of a word, then to a good representation of a sentence, then to a good representation of a paragraph, then to a document. But that is yet another story :)

[article]: http://www.aclweb.org/anthology/D/D15/D15-1131.pdf
[collobert]: http://arxiv.org/pdf/1103.0398
[zhang]: http://arxiv.org/abs/1502.01710
[bytes]: http://arxiv.org/abs/1512.00103
