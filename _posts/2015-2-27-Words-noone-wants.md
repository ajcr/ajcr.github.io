---
layout: post
title: The words nobody wants for websites
---

There are [over 130 million](http://www.verisigninc.com/en_US/channel-resources/domain-registry-products/zone-file-information/index.xhtml) .com and .net domain names. Is every single English word being used?

Not by a long way! Checking the domains against a list of over 375,000 words, names and places, it turns out that only around 64% of these words are in use. What's more, the most popular words (*online*, *group*, *design*, ...) are *incredibly* popular: just over 1,000 words account for half of all the words used in all registered .com and .net domains. As the chart below indicates, there are many thousands of words that are hardly used at all.

<img src="{{ site.baseurl }}/images/N-most-popular.png" alt="N most popular words / Percentage of all words" style="width: 800px;"/>

It seems that people just don't need that many words for their websites.

To find this out, I took a list of 130 million split every domain name back into distinct words - *buycheapcars.com* becomes *buy cheap cars*, while *dropbox.com* becomes *drop box* - and then counted how many times each individual word appeared. 

Splitting is not a trivial problem. There might be many different ways to break up the domain into recognisable words. After some initial investigation, I found that splitting to the fewest words possible gave the most accurate-looking results and also had the advantage of being relatively fast. I wrote a short program based on [this function](https://github.com/ajcr/string-splitter/blob/master/splitter.py) to read a list of English words and spit out the least number words needed for a given domain name.

My computer burned through the 130 million domains in about four hours, only failing to split 18 million gobbledegook domains. A bit of cleaning up and I had a long list of 356,580,878 words. The computer whirred for a few more minutes while it counted them.

So if you want to be the proud owner of domain name with a never-before-used word, what are your options? Here a few ideas...

- *pewful* - enough to fill a church pew
- *caufle* - a gang of slaves
- *oozoid* - an individual formed from an egg that buds asexually to produce sexually reproducing forms
- *spiccato* - a technique of bowing in which the bow is made to bounce slightly from the string
- *tawtie* - matted or tangled (Scottish)
- *acidology* - the science of surgical appliances
- *tappoon* -  a piece of wood/sheet metal fitted into a ditch to dam the water
- *cenosity* - filthiness

...and many, many more. The shortest English word that did not appear in any domain was the five-letter *whewl*, which means to cry and complain. 

Reading through a list of words that appeared only once, I saw *seqed* and learned that it is an ancient Egyptian unit of measurement. I smiled at *winced*, *gobbed*, *busked* and *plebby*. I was surprised that *meatiest* is so underused.

As you might expect, longer words are used very infrequently. That well-known 29-letter word *floccinaucinihilipilification* makes an appearance in five domains. There are two other 29-letter words, each appearing once only: *trinitrophenylmethylnitramine* and *cyclotrimethylenetrinitramine*. They're both powerful explosives.

However vast the Internet may be, there's still a long way to go before it exhausts the English language.
