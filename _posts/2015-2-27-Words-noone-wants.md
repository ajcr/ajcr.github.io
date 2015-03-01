---
layout: post
title: The words nobody wants for websites
---

There are [over 130 million](http://www.verisigninc.com/en_US/channel-resources/domain-registry-products/zone-file-information/index.xhtml) .com and .net domain names. Is every single English word being used?

Not by a very long way. Checking these domains against a list of over 375,000 words, names and places, it turns out that only around 64% of these words are actually in use. What's more, the most popular words (*online*, *group*, *design*, ...) are incredibly popular: just over 1,000 words account for half of all words that appear in registered .com and .net domains. 

This means that an awful lot of words are hardly used at all.

<img src="{{ site.baseurl }}/images/N-most-popular.png" "N most popular words / Percentage of all words" style="width: 800px;"/>

To find all of this out, I took a list of 130 million split every domain name back into distinct words - *buycheapcars.com* becomes *buy cheap cars* while *dropbox.com* becomes *drop box* - and then counted how many times each individual word appeared. 

Splitting is not a trivial problem. There might be many different ways to break up the domain into recognisable words and finding the "correct" way could require some advanced natural language processing. After some experimentation, I found that simply splitting to the fewest words possible gave the most accurate-looking results and also had the advantage of being much faster than finding and comparing all possible splits. I decided that any domain than could not be split cleanly would be discarded. I wrote a short program based on [this function](https://github.com/ajcr/string-splitter/blob/master/splitter.py) to read a list of English words and spit out the words making up a given domain name.

My computer ground through the 130 million domains in about four hours, only failing to split 18 million gobbledegook domains. A bit of cleaning up and I had a long list of 356,580,878 words. The computer whirred for a few more minutes while it counted them.

So if you want to be the proud owner of domain name with a never-before-used word, what are your options? Here a few ideas:

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

As a rule, longer words are used more infrequently. That well-known 29-letter word **_floccinaucinihilipilification_** makes an appearance in five domains. There are two other 29-letter words, each appearing once only: **_trinitrophenylmethylnitramine_** and **_cyclotrimethylenetrinitramine_**. They're both powerful explosives.

Lastly, I checked a list of names. Most are accounted for, but **_Goldarina_** and **_Elladine_** are nowhere to be found. Men, you've no hope of being unique unless you happen to be named **_Giselbert_**, **_Reginauld_** or **_Cecilius_**.

However vast the Internet may be, there's still a long way to go before it exhausts the English language.
