---
layout: post
title:  "Introduction to Data Science: Custom Word Clouds"

description: Create your own Word Clouds with Python and Twitter

image_header: /assets/img/bg-img/13.jpg

pagination: 
  enabled: true
---


![All data](/assets/img/blog/custom_word_cloud/nike.png)
<center><i style="color:#a6a6a6">Example Nike Word Cloud</i></center>

<p>&nbsp;</p>

<p>I first got into data science almost out of coincidence because of a <u><a href="http://blog.yhat.com/posts/rodeo-presidential-tweets.html">side project</a></u>. My friend and I originally came from computer science backgrounds, but we were fascinated at how much attention the presidential candidate tweets were getting; so we stumbled upon Twitter APIs, the difficulty of cleaning text, and data visualizations — except we didn’t know how to make visualizations without the drag and drop functionality of Tableau. Revisiting the project, I realized how making a word cloud is a fun introductory tutorial for those wanting to see the entire process of collecting, cleaning, and visualizing data in pure Python. So with this code, you too can create a word cloud in the shape of your favorite logo/image!</p>

<p>&nbsp;</p>


<p><strong> Step 1: Import the necessary packages </strong></p>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
import fitbit
import gather_keys_oauth2 as Oauth2
import pandas as pd 
import datetime

CLIENT_ID = '22CPDQ'
CLIENT_SECRET = '56662aa8bf31823e4137dfbf48a1b5f1'
</code></pre>
</p>

<p>&nbsp;</p>


<p><strong> Step 2: Collect the data </strong></p>

<p>
The first step is to gain access to Twitter’s API by registering for an app to obtain the token keys for the API at <u><a href="https://dev.twitter.com/">https://dev.twitter.com/</a></u> and then creating a new app at <u><a href="https://apps.twitter.com/">https://apps.twitter.com/</a></u>. Once the app has been created, head over to “Keys and Access Tokens” to get the API Key and API Secret.
</p>

<p>&nbsp;</p>


![Keys and Secret](/assets/img/blog/custom_word_cloud/wc1.jpeg)
<center><i style="color:#a6a6a6">Figure 1: API Key and Secret for Twitter</i></center>

<p>&nbsp;</p>

<p>
Once the application has been created and approved, our next step is to connect to the Twitter API with the Twython package in order to extract the raw Tweets from our lucky, chosen user handle.
</p>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
#Connect to Twitter
APP_KEY = "Dgh9WnBxXeLUcBYhXeohInazm"
APP_SECRET = "6fTM5hIYVbMGwVWoIoII7smrE3BexV6x8E9Xj7Ye6EJgw2LGA3"
twitter = Twython(APP_KEY, APP_SECRET)
</code></pre>
</p>

<p>&nbsp;</p>


<p>
From there, we can now call the function on a user timeline. Note that Twitter limits how far one can go back on a user’s timeline, which is 3200 at this point in time for free tier. While not as simple as the other parts of creating the word cloud, the following block of code checks for the most recent id and uses that as a placeholder to get the next batch of 200 tweets.
</p>

<p>&nbsp;</p>


<p>
<pre class="my-pre"><code>
#Get timeline 
user_timeline=twitter.get_user_timeline(screen_name='Nike',count=1) #get most recent id
last_id = user_timeline[0]['id']-1
for i in range(16):
    batch = twitter.get_user_timeline(screen_name='Nike',count=200, max_id=last_id)
    user_timeline.extend(batch)
    last_id = user_timeline[-1]['id'] - 1
</code></pre>
</p>

<p>&nbsp;</p>


![Output](/assets/img/blog/custom_word_cloud/wc2.jpeg)
<center><i style="color:#a6a6a6">Figure 2: Our Output in Python</i></center>

<p>&nbsp;</p>

<p>
While this output is confusing, fortunately, there are resources to help break down the JSON tweet format.
</p>

![JSON](/assets/img/blog/custom_word_cloud/wc3.png)
<center><i style="color:#a6a6a6">Figure 3: Full Tweet JSON Format (Source: Raffi Krikorian)</i></center>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
#Extract textfields from tweets
raw_tweets = []for tweets in user_timeline:
    raw_tweets.append(tweets['text'])
</code></pre>
</p>

<p>&nbsp;</p>


![Raw Tweet](/assets/img/blog/custom_word_cloud/wc4.jpeg)
<center><i style="color:#a6a6a6">Figure 4: Raw Tweet Fields</i></center>

<p>&nbsp;</p>

<p>
The above image, although created in 2010, breaks down the JSON Tweet format and is an invaluable source for almost any Twitter project — such as time series, natural language processing, and geospatial projects.
</p>

<p>
Using Figure 3 as our guide, we can see that the “text” field of each tweet contains the information we need. Furthermore, it appears that our timeline data is a list of dictionaries, so we can iterate through the list and grab each value with the “text” key.
</p>

<p>&nbsp;</p>


<p><strong> Step 3: Clean all the data </strong></p>

<p>
At this point, we have our raw data - all URL links, special characters, emojis, and extra white spaces. Unfortunately, these types of punctuation and words take the word out of a word cloud; so we need to deal with them on a case by case basis.
</p>

<p>&nbsp;</p>


![Drake Alphabet](/assets/img/blog/custom_word_cloud/wc5.jpeg)
<center><i style="color:#a6a6a6">Drake Alphabet</i></center>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
#Create a string form of our list of textraw_string = ''.join(raw_tweets)
no_links = re.sub(r'http\S+', '', raw_string)
no_unicode = re.sub(r"\\[a-z][a-z]?[0-9]+", '', no_links)
no_special_characters = re.sub('[^A-Za-z ]+', '', no_unicode)
</code></pre>
</p>


<p>&nbsp;</p>


![Cleaned Words](/assets/img/blog/custom_word_cloud/wc6.jpeg)
<center><i style="color:#a6a6a6">Figure 5: Words without special characters</i></center>
<p>&nbsp;</p>


<p>
Beyond removing special characters, we can see filer words like “and”, “the”, and “but” which don’t have much significance given their frequency in all sentences. And so we can get rid of these words, known as “stop words” by keeping them only if they are not part of the predefined words part of the wordcloud package. Additionally, we can also remove any straggler letters that might’ve remained by checking the length of each word and keeping it if it matches a certain length.
</p>


<p>&nbsp;</p>


<p>
<pre class="my-pre"><code>
words = no_special_characters.split(" ")
words = [w for w in words if len(w) > 2]  # ignore a, an, be, ...
words = [w.lower() for w in words]
words = [w for w in words if w not in STOPWORDS]
</code></pre>
</p>

![No Filter](/assets/img/blog/custom_word_cloud/wc7.jpeg)
<center><i style="color:#a6a6a6">Figure 6: Clean words without fillers</i></center>
<p>&nbsp;</p>


<p><strong> Step 4: Visualize all the data </strong></p>

<p>
Pick any of your favorite images to use as a background image! (Pro tip: works better with larger images with a white background)
</p>

![Nike Stencil](/assets/img/blog/custom_word_cloud/wc8.jpeg)
<center><i style="color:#a6a6a6">Figure 7: Nike Stencil</i></center>
<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
mask = np.array(Image.open('/Users/shsu/Downloads/nike.png'))
</code></pre>
</p>


<p>
Create the word cloud instance (note that the documentation requires that the text be in a string format):
</p>


<p>
<pre class="my-pre"><code>
wc = WordCloud(background_color="white", max_words=2000, mask=mask)
clean_string = ','.join(words)
wc.generate(clean_string)
</code></pre>
</p>

<p>And finally show off our new image:</p>

<p>
<pre class="my-pre"><code>
f = plt.figure(figsize=(50,50))
f.add_subplot(1,2, 1)
plt.imshow(mask, cmap=plt.cm.gray, interpolation='bilinear')
plt.title('Original Stencil', size=40)
plt.axis("off")f.add_subplot(1,2, 2)
plt.imshow(wc, interpolation='bilinear')
plt.title('Twitter Generated Cloud', size=40)
plt.axis("off")
plt.show()
</code></pre>
</p>

![Stencil v. Cloud](/assets/img/blog/custom_word_cloud/wc8.jpeg)
<center><i style="color:#a6a6a6">Stencil versus Cloud</i></center>

<p>&nbsp;</p>

<p>
Ta-da! We’ve now got our own word cloud image made from Twitter data with 100% pure Python code! You’re one project closer to Veni, Vedi, Vici-ing the data science world!
</p>

<p>References</p>

<p> 1. <a href = "https://twitter.com/login?redirect_after_login=https%3A%2F%2Fdeveloper.twitter.com%2Fapps"><u> Twitter App </u></a> </p>
<p> 2. <a href = "https://twython.readthedocs.io/en/latest/"><u> Twython Documentation </u></a> </p>
<p> 3. <a href = "https://github.com/amueller/word_cloud"><u> Andreas Mueller Word Cloud Mask </u></a></p>