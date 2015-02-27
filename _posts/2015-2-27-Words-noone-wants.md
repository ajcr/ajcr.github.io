---
layout: post
title: The words nobody wants for websites
---

There are [over 130 million](http://www.verisigninc.com/en_US/channel-resources/domain-registry-products/zone-file-information/index.xhtml) .com and .net domain names. Is every single English word being used?

To find out, I split every domain name back into distinct words - *buycheapcars.com* becomes *buy cheap cars*, *dropbox.com* becomes *drop box* - and then counted how many times each individual word appeared. 

Splitting is not a trivial problem. There might be many different ways to break up the domain into recognisable words. After some initial investigation, I found that splitting to the fewest words possible gave the most accurate-looking results. I wrote a short program based on [this function](https://github.com/ajcr/string-splitter/blob/master/splitter.py) to read a list of English words and spit out the least number words needed for a given domain name.

My computer burns through the 130 million domains in about four hours, only failing to split 18 million gobbledegook domains. A bit of cleaning up and I have a long list of 356,580,878 words. The computer whirs for a few more minutes while it counts them.

So if you want to be the proud owner of domain name with a never-before-used word, what are your options?

It turns out that a lot of words don't show up very often. My program could recognise 375,298 words, names and places, but found only about 64% of those were in use. In fact, the 1,000 most common words account for just over half the words in all domains. It seems that people and businesses rely on a core set of words for their websites.

This means that you could the first person to register a domain containing:

- *pewful* - enough to fill a church pew
- *caufle* - a gang of slaves
- *oozoid* - an individual formed from an egg that buds asexually to produce sexually reproducing forms
- *spiccato* - a technique of bowing in which the bow is made to bounce slightly from the string
- *tawtie* - matted or tangled (Scottish)
- *acidology* - the science of surgical appliances
- *tappoon* -  a piece of wood/sheet metal fitted into a ditch to dam the water
- *cenosity* - filthiness

...and many, many more. The shortest English word that did not appear was the five-letter *whewl*, which means to cry and complain. 

Reading through a list of words that appeared only once, I saw *seqed* and learned that it is an ancient Egyptian unit of measurement. I smiled at *winced*, *gobbed*, *busked* and *plebby*. I was surprised that *meatiest* is so woefully underused.

As you might expect, longer words are used very infrequently. That well-known 29-letter word *floccinaucinihilipilification* makes an appearance in five domains. There are two other 29-letter words, each appearing once only: *trinitrophenylmethylnitramine* and *cyclotrimethylenetrinitramine*. They're both powerful explosives.

However vast the Internet may be, there's still a long way to go before it exhausts the English language.
