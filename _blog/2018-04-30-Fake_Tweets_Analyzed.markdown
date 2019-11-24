---
layout: post
title:  "Russian Fake Tweets Visualized"

description: Visualizing 3000+ Fake Russian Accounts with over 200,000+ Tweets             

image_header: /assets/img/bg-img/13.jpg

pagination: 
  enabled: true
---


![Twitter Word Cloud](/assets/img/blog/tweets/tweet1.png)

<center><i style="color:#a6a6a6">Fake Tweet Cloud</i></center>

<p>Co-Author:&nbsp;David Kes</p>
<p>With the&nbsp;fervor&nbsp;of the Presidential election being skewed by Russian probing&rsquo;s as well as the notorious Facebook / Cambridge Analytics scandal still topping daily&nbsp;domestic&nbsp;headlines, it became clear to us that &ldquo;fake news&rdquo; and Russian users are still prevalent, yet vague concepts. Who are these fake users disguising as? How are&nbsp;these&nbsp;fake users fooling people? How are they influencing people? Therefore, with our backgrounds in natural language processing, data visualization, and interest in the combination of technology and politics, it was only natural to examine the fake Russian user&rsquo;s Tweet data with Python and Plotly. In particular, we analyzed:</p>

<p>Users and Tweets Overview</p>

<p>1. Who these fake users pretend to be &mdash; names and descriptions</p>
<p>2. When the fake accounts were being created</p>
<p>3. When the fake accounts were most active in tweeting</p>
<p>4. What topics the accounts were covering</p>

<p>&nbsp;</p>

<p>Case Study of Most Successful (or Unsuccessful) Users</p>


Tweet Velocity
Tweet Polarity
Tweet Subjectivity


<p><b>Data</b></p>
<p><em>NBC News published the database of more than 200,000 tweets that Twitter has tied to &ldquo;malicious activity&rdquo; from Russia-linked accounts during the 2016 U.S. presidential election. These accounts, working in concert a part of large networked, pushed hundreds of thousands of inflammatory tweets. The data can be found&nbsp;</em><em><a href="https://www.nbcnews.com/tech/social-media/now-available-more-200-000-deleted-russian-troll-tweets-n844731"><u style="color:blue">here</u></a></em><em>.</em></p>
<p>&nbsp;</p>



![User Data](/assets/img/blog/tweets/tweet2.jpeg)
<center><i style="color:#a6a6a6">Figure 1: Joined User and Tweet Data Provided by NBC</i></center>

<p>&nbsp;</p>

![Example Account](/assets/img/blog/tweets/tweet3.jpeg)
<center><i style="color:#a6a6a6">Figure 2: Example of a (non-fake) Twitter Account with Highlighted Description and Tweet</i></center>
<p>&nbsp;</p>
<p>Now that we have the data, let&rsquo;s dive into the analysis:</p>
<p><strong>Who are these fake users?</strong></p>
<p>&nbsp;</p>

<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/11.embed"></iframe>

<center>Figure 3: First and Last &ldquo;Names&rdquo; of the Users</center>
<p>&nbsp;</p>
<p>If we look at the face value of our fake users, we can see that there are three distinct categories of fake account names. The first combination consists of American sounding first names such as &ldquo;Chris,&rdquo; &ldquo;Rick,&rdquo; or &ldquo;Jeremy&rdquo; combined with American sounding last names such as &ldquo;Green,&rdquo; &ldquo;Roberts,&rdquo; or &ldquo;Cox.&rdquo; The second combination consists of formal sounding news sources such as &ldquo;New [York Times]&rdquo; or &ldquo;Atlanta Today.&rdquo; Finally, the third combination consists of purely foreign names. From this, we can see that it&rsquo;s often difficult to tell which accounts are fake based off the name alone as it could be any average Joe, news site, or unrecognizable name.</p>
<p><strong>What are their profile descriptions?</strong></p>


![LDA Topics](/assets/img/blog/tweets/tweet4.jpeg)
<center><i style="color:#a6a6a6">Figure 4: Topics Within the Descriptions of Fake Users</i></center>

<p>&nbsp;</p>
<p>A unique part of creating Twitter accounts is the option to include a short description of who you are. Users often post their summaries and ideologies for others to see. Specifically, the above graph shows one topic in common with the fake accounts &mdash; religion. By using words like &ldquo;God,&rdquo; &ldquo;InGodWeTrust,&rdquo; and &ldquo;GodBlessAmerica,&rdquo; the fake accounts become relatable to a large group of people. Other bios include black lives matters topics, official news sounding descriptions (such as &ldquo;sports,&rdquo; &ldquo;weather,&rdquo; and &ldquo;official&rdquo;), and foreign topics. Therefore, by quickly, if not instantly, relating to this fake user, users are more likely to follow or agree with the fake accounts tweets. More on this similarity attraction phenomenon can be read <a href="https://www.sciencedirect.com/science/article/abs/pii/0092656676900854"><u>here.</u></a> </p>
<p><strong>When were they made?</strong></p>
<p>&nbsp;</p>




<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/25.embed"></iframe>
<center><i style="color:#a6a6a6">Figure 5: Russian Fake Accounts Created from 2009 to 2017</i></center>


<p>&nbsp;</p>
<p>&nbsp;</p>
<p>Of the 454 accounts deemed to be fake Russian accounts, we can see the creation of the fake accounts started in 2009 and reached its peak of creations in 2013 before slowly lessening all the way to the start of 2017. Interestingly, this means the majority of fake accounts were created years before the actual 2016 Presidential election, perhaps to cause strife and division amongst US readers ahead of the election.</p>
<p><strong>Where are they from?</strong></p>
<p>We took a look at where the fake users &ldquo;originated&rdquo; from and discovered that of the 454 values, approximately half the values were missing. Of the 287 locations listed, 124 were listed as some form of &ldquo;United States&rdquo;, 68 were listed as a large metropolitan cities in the United States (ie: San Francisco, New York, Atlanta, Los Angeles), and 37 values were in foreign countries, and the remaining 58 values were imaginary like &ldquo;located at the corner of happy and healthy&rdquo; or &ldquo;the block down the street.&rdquo; Therefore, since the data was majority missing and most likely fake, we opted from analyzing the data further.</p>



<p><strong>How influential are they?</strong></p>
<p>&nbsp;</p>
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/23.embed"></iframe>



<center><i style="color:#a6a6a6">Figure 6: Followers vs. Number of Tweets of Fake Accounts</i></center>
<p>&nbsp;</p>
<p>On a high level, we can see the number of Tweets increasing with the number of followers. This makes sense as these fake accounts are leveraging their popularity on social media to reach out and influence more individuals. One such notable fake user is the infamous Jenna Abrams account, whose racist, controversial, and fake tweets were at one point&nbsp;covered in mainstream media. At this point, it&rsquo;d be safe to say these tweets definitely were influential on the popular mass.</p>
<p><strong>When are they posting?</strong></p>


<p>&nbsp;</p>
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/35.embed"></iframe>
<center><i style="color:#a6a6a6">Figure 7: Heat Map of Fake User Tweet Activity</i></center>
<p>&nbsp;</p>


<p>From the above heat map, we can see that the fake users are predominantly posting on Sundays and Tuesdays in the later months of the year (ie: August, September, October, November, and December). Therefore, we can expect this is not by chance; that the fake users understand their content reaches more individuals on weekends rather than weekdays in the later half of the year when the election and other big events occur.</p>
<p><strong>What are they saying?</strong></p>
<p>&nbsp;</p>



![Twitter Word Cloud](/assets/img/blog/tweets/tweet8.jpeg)
<center><i style="color:#a6a6a6">Figure 8: Topical Modeling of Tweet Content</i></center>

<p>&nbsp;</p>
<p>Similar to the descriptions of the fake users, we took a look at the topics covered within the actual tweet content. In the above graph, we can see that the Black Lives Matters and other racial subject matters were one such topic the Russian accounts targeted with words such as &ldquo;police,&rdquo; &ldquo;blacklivesmatter,&rdquo; &ldquo;crime,&rdquo; and references to the&nbsp;shooting in San Bernardino, particularly about the perpetrator being of minority descent. Other topics covered by the users consisted of Hillary Clinton&rsquo;s private email server, ISIS, pro-Trump slogans, slanderings of the election debates, and school shootings. These poignant and popular events were a particularly easy topic the fake accounts could add their propaganda-opinion to, as individuals were already livid and divided on how to feel in reaction to these events.</p>
<p><strong>Case Study &mdash; in depth look at top 20 users</strong></p>
<p>We know now what the prototypical fake account looks like, but what is the anatomy of a successful (or perhaps unsuccessful) Russian fake account? How are they gaining attention? From Figure (scatterplot), we see a positive correlation between number of followers and number of Tweets. Therefore, the following graphs examine their tweeting behavior in terms of velocity, sentiment, and subjectivity over time of the top 20 followed fake Russian accounts.</p>


<p><strong>Tweet Velocity</strong></p>
<p>&nbsp;</p>
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/19.embed"></iframe>



<center><i style="color:#a6a6a6">Figure 9: Number of Tweets Tweeted by Fake Accounts</i></center>
<p>&nbsp;</p>
<p>In terms of pure tweet volume, we can see a trend of the fake accounts being almost nonexistent until around June 2016, at which point the volume of Tweets increases dramatically &mdash; reaching its apex in October 2016. The tweets then tumble down in volume after November 2016 (election month), with one last resurgence around December 2016 before going back to an almost inactive state. This worrying trend shows the opportunistic behavior of the fake accounts, tweeting at the most tense and vital points of the election fervor.</p>
<p><strong>Tweet Sentiment and Subjectivity</strong></p>
<p>The following graph is the average sentiment and subjectivity of the tweets made by the top 20 followed users. In the context of tweets, sentiment is defined as an attitude, thought, or judgement. In particular, tweets can be analyzed by using the Textblob package for their sentiment, with a score of -1 for a negative sentiment tweet of a +1 for positive sentiment tweets. Lastly, subjectivity in the context of tweets is often seen as how opinionated the given tweet is. In Textblob, the score ranges from 0 for very objective and 1 for heavily subjective or opinionated.</p>


<p>&nbsp;</p>
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/21.embed"></iframe>


<center><i style="color:#a6a6a6">Figure 10: Average Sentiment and Subjectivity of Top 20 Followed Users</i></center>
<p>&nbsp;</p>
<p><strong>Time Series Point Analysis</strong></p>
<p>&nbsp;</p>
<iframe width="900" height="800" frameborder="0" scrolling="no" src="//plot.ly/~shsu/33.embed"></iframe>
<center><i style="color:#a6a6a6">Figure 11: Time Series Tweets with Hash Tags</i></center>



<p>&nbsp;</p>
<p>Using the power of hindsight and the&nbsp;Wikipedia of US current events, we can see what some notable spikes are related to:</p>
<p>August 4, 2016: spike of hash tag #obamaswishlist which were posts about fanciful and perceived hypocritical items Obama &ldquo;wanted&rdquo;</p>
<p>August 17, 2016: spike of the hash tag #trumpsfavoriteheadline which are Tweets about sardonic headlines that Donald Trump would endorse</p>
<p>September 28, 2016: #ihavearighttoknow movement by fake accounts to know what Hillary Clinton&rsquo;s emails were</p>
<p>October 5, 2016: #ruinadinnerinonephrase was actually seen as both politically-backed and non-politically-backed with some referencing it to Hillary Clinton while others made memes out of the hashtag</p>
<p>October 17, 2016: #makemehateyouinonephrase, another hash tag movement that was seen as either part of a meme culture or part of the political systems</p>
<p>November 14, 2016: #reallifemagicspells used in reference with black lives matters and Trump&rsquo;s family</p>
<p>December 7, 2016: #idrunforpresidentif &ldquo;I&rsquo;d known I needed literally zero experience&rdquo; and other sardonic comments about the presidential election</p>
<p>Perhaps coincidentally or not, the initial spikes were all related to fake accounts simultaneously using hashtags to mock presidents and presidential candidates. The tweets were clearly politically-based with the name drops to actual candidates. However, as time progressed, the distinguishing factor between these tweets became less obvious, as the fake accounts used actual popular hashtags that were not clearly political. Additionally, the tweets seemed aimed at all candidates rather than one particular candidate until Trump was actually elected &mdash; at which point all attacks were directed at Trump.</p>
<p><strong>Conclusion</strong></p>
<p>From our analysis, we learned that the fake accounts disguise themselves as (1) average Americans, (2) news sites with metropolitan names, or (3) international names that describe themselves with relatable topics such as political and religious beliefs; they achieved their objective of influencing Twitter users by posting subjective and polar tweets at opportunistic times such as the weekends when scandals and large announcements occurred. Finally, they grew sentient of their obvious posts by subtlely joining trending hashtags and injecting propaganda within it.</p>
<p>Thank you for taking the time to read our analysis of Russian Fake Tweets. Leave a like or comment if you can now talk about fake tweets without sounding fake, think there&rsquo;s any other things we should analyze, or have any opinions. Feel free to follow or connect with&nbsp;Stephen&nbsp;and/or&nbsp;David&nbsp;on LinkedIn as we&rsquo;ll be posting more interesting articles and tutorials!</p>
<p><strong>References</strong>:</p>


<p> 1. <a href = "https://chart-studio.plot.ly/feed/"><u> Plotly </u></a> for visualizations </p>
<p> 2. <a href = "https://pyldavis.readthedocs.io/en/latest/"><u> Pyldavis </u></a> for topical modeling </p>
<p> 3. <a href = "https://www.nbcnews.com/tech/social-media/now-available-more-200-000-deleted-russian-troll-tweets-n844731"><u> NBC Russian Fake Tweets </u></a> for dataset </p>
<p> 4. <a href = "https://variety.com/2016/tv/news/cnn-porn-boston-rcn-cable-operator-1201926702/"><u> Jenna Abrams Coverage in Mainstream Media </u></a></p>
<p> 5. <a href = "https://textblob.readthedocs.io/en/dev/"><u> Textblob </u></a> for natural language processing (NLP) </p>
<p> 6. <a href = "https://en.wikipedia.org/wiki/Portal:Current_events"><u> Historical events </u></a> in the United States </p>
<p> 7. <a href = "https://www.sciencedirect.com/science/article/abs/pii/0092656676900854"><u> Psychology of similarity attraction </u></a></p>
<p> 8. <a href = "https://www.cnn.com/2015/12/02/us/san-bernardino-shooting/index.html"><u> San Bernardino Shooting </u></a> </p>
<p> 9. <a href = "https://github.com/stephenjhsu/Fake-Tweets-Visualized"><u> Github of code for graphs and preprocessing </u></a> </p>

<p>&nbsp;</p>