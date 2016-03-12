---
layout: post
title: Words nobody wants for websites
---

There are [over 130 million](http://www.verisigninc.com/en_US/channel-resources/domain-registry-products/zone-file-information/index.xhtml) .com and .net domain names. Is every single English word being used?

Curious, I hatched a plan and found the answer: **not at all**.

By my reckoning, only around 64% of all recognisable English are actually in use. What's more, the most popular words (*online*, *group*, *design*, etc.) are incredibly common. In fact, just over 1,000 English words account for *half* of all words that appear in registered .com and .net domains. 

The pool of rarely-used words is very large indeed!

<img src="{{ site.baseurl }}/images/N-most-popular.png" "N most popular words / Percentage of all words" style="width: 800px;"/>

---

To find out what this pool contains, I decided to take a list of 130 million domains and split every single one back into distinct words. For example, *buycheapcars.com* splits into three words: *buy cheap cars*. The domain name *dropbox.com* becomes *drop box*. I would then counted how many times each individual word appeared.

This methodology is good, I reasoned, because it prevents common substrings from being counted too frequently and making the counts biased. For example, *sandinmyeyes.com* would contribute just three words (*sand*, *in*, *my*, *eyes*). Subwords such as *and*, *din* and *yey* do not get counted at all. This is good because no straight-thinking person would think the domain owner intended to choose those words.

Splitting strings is not a trivial problem however. There could be many different ways to break up the domain into recognisable words; finding the "correct" way could require some advanced natural language processing. This is difficult to implement, computationally expensive and I wanted more immediate gratification. After some experiments, I found that simply splitting into the *fewest* words possible gave accurate-looking results. This also had the advantage of avoiding the comparison of all possible splits. I decided that any domain than could not be split cleanly would be discarded. My final function was based on [this function](https://github.com/ajcr/string-splitter/blob/master/splitter.py) I wrote some time ago.

Not optimised, but fast enough. My computer churned through the 130 million domains in about four hours, only failing to split 18 million gobbledegook domains. A bit of cleaning up and I had a long list of 356,580,878 words (looks like domains contain an average of 2-3 words). The computer whirred for a few more minutes while it counted them and then was quiet.

---

So if you want to be the proud owner of domain name with a never-before-used word, what are your options? Here are a few ideas:

- **_pewful_** - enough to fill a church pew
- **_caufle_** - a gang of slaves
- **_oozoid_** - an individual formed from an egg that buds asexually
- **_spiccato_** - a technique of bowing in which the bow is made to bounce slightly from the string
- **_tawtie_** - matted or tangled (Scottish origin)
- **_acidology_** - the science of surgical appliances
- **_tappoon_** -  a piece of wood/sheet metal fitted into a ditch to dam the water
- **_cenosity_** - filthiness

The shortest English word that did not appear in any domain was the five-letter **_whewl_**, which means to cry and complain. 

Reading through a list of words that appeared only once, I saw **_seqed_** and learned that it is an ancient Egyptian unit of measurement. I smiled at **_winced_**, **_gobbed_** and **_plebby_**. I was surprised that **_meatiest_** is so underused.

Predictably, longer words are used less frequently than shorter words. That well-known 29-letter word **_floccinaucinihilipilification_** makes an appearance in just five domains. There are two other 29-letter words, each appearing only once: **_trinitrophenylmethylnitramine_** and **_cyclotrimethylenetrinitramine_**. These are both powerful explosives.

Armed with long lists of names, I checked to see if there were any which weren't being used. Most are accounted for, but the women's names **_Goldarina_** and **_Elladine_** are not taken. If you're male, you've no hope of owning a unique domain name unless you happen to be named **_Giselbert_**, **_Reginauld_** or **_Cecilius_**.

However vast the Internet may be, there's still a long way to go before it exhausts the English language.
