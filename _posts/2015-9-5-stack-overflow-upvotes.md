---
layout: post
title: Want more upvotes on Stack Overflow? Try playing the slow game
---

Stack Overflow, the hugely-popular programming Q&A website, has often been called an online game. To play, you post correct answers or ask interesting questions. Correct and interesting content is upvoted by other users, netting you precious green reputation points.

The two most common strategies to get a lot of upvotes on Stack Overflow are to:

- answer as many new questions as you can, as quickly as you can.
- choose your target carefully and write interesting, well-written posts that people will read and upvote for years to come.

There's not much to say about the first approach. You wait for someone to ask a question about your favourite programming language and then you answer. You have to be very quick, as there are usually a lot of people waiting to answer.

The second approach is more interesting. Not only do you improve the quality and longevity of the site when you post well-written content, but you have more opportunities to maximise the chances that as many people as possible will upvote you. This post looks at some of these strategies and how playing the slow game can earn you reputation. 

### Answer old questions that get lots of views

You want your answers to be seen by a lot of people because views lead to upvotes. 

However, many questions on Stack Overflow are viewed just a few dozen times when they're new and then not viewed much at all after that. It's a good idea to look for slightly older questions that have a lot of views already. Study the existing answers and add another if you have something new to say: perhaps the existing answers don't provide the best way to do something in the most current release of a language, or maybe a crucial technical consideration has been overlooked. Write well and it's likely that people will upvote your post, too.

How many upvotes can you get by answering old questions? 

The histogram below shows the number of answers wich have 100 or more upvotes, and how many days after the original question those answers were posted. We can see that most 'great answers' are posted within 30 days of the question being asked, but a significant number are posted one month or more later. One great answer ([this one](http://stackoverflow.com/a/30810322/3923281)) was posted a staggering 2355 days after the question appeared. Writing a quality answer to a popular old question can be very lucrative.

<img src="{{ site.baseurl }}/images/great-answers.png" "great answers" />

(Data collected using [this SQL query](http://data.stackexchange.com/stackoverflow/query/353836/high-scoring-late-answers).)


### Ask your own question *and* answer it

Asking and answering your own question is encouraged on Stack Overflow. The advantage of doing this is from a points perspective is that you've got all the time in the world to make the posts as perfect as possible. People want to upvote quality answers to meaningful questions and Stack Overflow lets you deliver both simultaneously. Also, because you're posting brand new content, it's likely it'll be viewed a lot it in a short period of time.

Think of it as a blog post or text book chapter: ask how something works or why something is implemented a particular way, then answer the question as comprehensively as possible. Here are three useful ingredients that go towards making an upvote-worthy question/self-answer:

- Pique people's interest with an interesting title. This is what makes them decide whether to click the link to your post. Make it descriptive but keep it snappy; try to make it sound interesting.
- Stick to a specific topic. Stack Overflow is home to all kinds of interesting information, but good posts keep the focus on solving a particular problem or addressing a certain technical challenge. If the scope is too broad, you'll lose people's attention.
- Everyone loves pictures/diagrams/graphs. Explaining coding concepts in words is good, but a well-chosen picture can really hammer home the point you're making.

Finally, think about *when* you're going to post your qustion and answer. Stack Overflow is much quieter at the weekend and so fewer people will see the post while it's new. Also, most of the site's users are based in Western Europe or the US. It probably doesn't make much difference, but posting while more people are awake in those countries seems like a good idea.

There are 17994 self-answered questions on Stack Overflow (counting only those where the question and answer were posted at the same instant), posted by 11367 different users. The mean answer score for a self-answer is 5.27. I could not find the mean answer score across *all* answers, but it's likely to be much lower than that. Looking only at great answers (those with a score of 100 or more), the mean self-answer score is a massive 374.22, compared with 223.46 for all great answers. That's quite some difference.


### Promote your old posts

Questions and answers remain on Stack Overflow indefinitely, as long as they continue to meet the site's quality standards.

This means that what you post today could be viewed several thousand times over the next year. Look back at your old posts to check which ones that are getting a lot of views: if you can improve your post (add more detail, make it more current), edit it. Similarly, see if the question you've answered needs any tweaking. Does it need a better title and tags so that it can be found more easily? Is the spelling good and the code formatted neatly? Quality is key, people won't waste too much effort trying to decipher badly-written questions and answers.

Editing also bumps posts back to the front page where they might be clicked on by people with upvotes to give. A word of warning: don't repeatedly use trivial edits to get your stuff bumped back to the front page. The Stack Overflow community is sensitive to this type of self-promotion and you may invoke the wrath of moderators.

Remember that you can earn badges for promoting links to pages on Stack Overflow. There's nothing dishonest about driving Internet traffic to your own posts, so tweet the links, or put them in your blog post ([here's a link to one of mine](http://stackoverflow.com/q/30100725/3923281)), just make sure you copy the link from the 'share' button on the post, not from the URL bar of your browser.

Lastly - and controversially - you can sometimes harness the power of the "Meta effect". *Meta* is Stack Overflow's help and support forum where users can ask and answer questions about the site. Suppose you've percieved some anomaly in the workings of the site and it has to do with a something you've written. A lot of people read posts on Meta and click on links to questions (or users), either out of curiosity or with the intention of voting. This presents the interesting opportunity to attract users who wouldn't normally view your post to read what you've written (perhaps they're interested in different languages). People generally like to upvote quality posts or correct injustices with votes, so you might get lucky. Some more words of warning: only post to Meta if your question isn't obviously a duplicate and be careful if the post is purely in your self-interest: it bears repeating that the community is (rightfully) averse to blatant self-promotion.

So how does this promotional activity *really* affect upvotes over time?

It's quite tricky to study how a post is voted on because Stack Overflow does not release data that shows when/how a post accumulated votes. This might be because such data could be used to identify patterns in voting behaviour and risk jeopardising voting anonymity. Not even moderators are allowed to see how a user has cast their votes.

That said, you have easy access to all of the dates that your own posts were voted on: just go to the [/reputation](stackoverflow.com/reputation) sub-directory of the site when you're logged in. For each day you been a member of Stack Overflow, you get a summary of your upvotes, downvotes and other reputation changes for that day. It's a massive block of free text, so be prepared to some tedious parsing and tidying to get the vote counts for each day. 

As a case study, I'll look at a [question and answer I posted](http://stackoverflow.com/q/30100725/3923281) a while ago. I'd stumbled upon the intricacies of comparing arbitrarily-large integers and floats in Python and wanted to write things up as best I could. The graph below shows how my question/answer was upvoted each day, from publication in May 2015 to the end of August 2015: 

<img src="{{ site.baseurl }}/images/upvotes-over-time.png" "upvotes-over-time" />


This graph can be divided visually into three distict parts:

1. The initial surge of upvotes. The post is new and getting a lot of views; two days in and the answer has been upvoted nearly 100 times. After that comes the weekend: a couple of votes on Saturday and nothing on Sunday (did I mention weekends are quiet on Stack Overflow?) On Monday, an avalanche of views and upvotes hits and over the next few days the answer has over 250 upvotes with the question not too far behind.

2. The drift into obscurity. The post is no longer brand new and suddenly gets far fewer views and upvotes. In fact, in the two months between the start of June and end of July, the question was upvoted just 9 times and the question only 6 times. As far as I've seen, this fate is inevitable on Stack Overflow; the site is huge and only very few questions sustain an impressive rate of views and upvotes.

3. The Meta effect. In early August, the post is mentioned in a Meta question and large number of users view it out of interest resulting in two-dozen or so upvotes for both the question and answer. The question soon drops off the front page of Meta and the post returns once again to the background of Stack Overflow.

So unless you promote or improve your old posts, they could well be lost amongst the vast amounts of new material that pours in to Stack Overflow each day. 

All things considered, try and play the slow game on Stack Overflow whenever you can. Post quality content, improve posts where needed and promote links to the site. You're highly likely to be repaid with upvotes, although how many depends a little on luck, just like any other game.
