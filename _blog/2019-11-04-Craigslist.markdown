---
layout: post
title:  "Webscraping Craigslist: Speeding Up the Housing Search"

description: Reduce Search Time from 2+ Hours to 15 Minutes

image_header: /assets/img/bg-img/13.jpg

pagination: 
  enabled: true
---

![Craigslist](/assets/img/blog/craigslist/craiglist.jpeg)

<p>
I was asked a while ago how I was able to find housing in San Francisco. It was one year ago before I found my current sofa-in-a-converted-living-room that I started my search for housing, and it quickly became an overwhelming experience trying to find any kind of rent for less than an arm and a leg. I devoted more and more time to online searching and expanded my search from the Financial District to several regions of San Francisco including the notorious Tenderloins. The search slowly consumed me to the point where I would wake up and spend at least an hour everyday clicking on Craigslist seeing familiar places with a worn out face.
</p>

<p>
I thought to myself, “there must have been some way to expedite the housing search!” As an aspiring data scientist, my solution was to create a web scraper that could collect the information of several regions of San Francisco and present them in a concise way without all the fluff. While it was no guarantee to find a house, at least I could reduce my 1+ hour searches to 10 minutes.
</p>

<p>
TLDR: I made a script to scrape Craigslist with locations and pricing as parameters. The code is located at the bottom, but otherwise continue reading to see the entire web-scraping process!
</p>

![SF Map](/assets/img/blog/craigslist/craigslist1.jpeg)
<center><i style="color:#a6a6a6">One of the most helpful pictures I had in finding housing</i></center>

<p>&nbsp;</p>

<p><strong>Step 0: Know your enemy</strong></p>

![Interface](/assets/img/blog/craigslist/craigslist2.jpeg)
<center><i style="color:#a6a6a6">What the Craigslist interface looks like</i></center>


<p>&nbsp;</p>

![Desired Output](/assets/img/blog/craigslist/craigslist3.jpeg)
<center><i style="color:#a6a6a6">What I wanted the results to look like</i></center>

<p>
Often times, it’s easy to get ahead of oneself and dive right into the problem. One thing that significantly helped my coding skills was to start off with smaller goals and building it up into a big, grand goal. My big goal is to get the headline of each post as well as it’s link to the individual page. I then want to move from one region of San Francisco (say Glen Park) to another one (say Balboa Park). Looking at the Craigslist interface, it appears as though each post has a title and a blue hyperlink that would be useful to show up beside the description.
</p>

<p>
So breaking down the big goal, my plan of attack is to:
</p>



<p> &nbsp; &nbsp; &nbsp; 1. Open up a remote controlled page using Selenium </p>
<p> &nbsp; &nbsp; &nbsp; 2. Scrape a single page of Craigslist housing to get the titles </p>
<p> &nbsp; &nbsp; &nbsp; 3. Scrape it further to get the links to seller’s posts </p>
<p> &nbsp; &nbsp; &nbsp; 4. Move to another location without rewriting anything </p>
<p> &nbsp; &nbsp; &nbsp; 5. Repeat this process until all locations are scraped </p>
<p> &nbsp; &nbsp; &nbsp; 6. Bring it all together to a nice function </p>



<p><strong>Step 1: Bring in the webscraping tools</strong></p>

<p>
<pre class="my-pre"><code>
from selenium import webdriver
from bs4 import BeautifulSoup
import urllib2
import re
import pandas as pd
import numpy as np
pd.set_option('max_colwidth', 5000)
pd.options.display.max_rows = 999
</code></pre>
</p>


<p>&nbsp;</p>


<p><strong>Step 2: Open up a webpage with Selenium</strong></p>

<p>
“<u><a href="https://selenium.dev/">Selenium</a></u> is a tool that lets us automate actions within a browser as if we were clicking or typing as a human. This lets us login and do anything else a human can do when using the browser. Perhaps most importantly, it allows us to scrape data from webpages that generate HTML using JavaScript executing within the browse” — Professor Terence Parr at the University of San Francisco
</p>


<p>
To initialize the Selenium tools, we need to call it from the path from which the Chromedriver (or geckodriver) is located. <u><a href="https://selenium.dev/downloads/">(Link to download Selenium)</a></u>
</p>

<p>
<pre class="my-pre"><code>
driver = webdriver.Chrome(executable_path="/Users/shsu/Downloads/chromedriver")
</code></pre>
</p>

<p>From there, we can use the driver to open up certain webpages, for example:</p>

<p>
<pre class="my-pre"><code>
driver.get('http://google.com')
</code></pre>
</p>

<p>&nbsp;</p>

<p>
This should open up a new browser on your Chrome which can then be automated with code. Try it out with any website of your choice!
</p>



<p><strong>Step 3: Scrape post headlines from a page</strong></p>

![Query](/assets/img/blog/craigslist/craigslist4.jpeg)
<center><i style="color:#a6a6a6">A general query for my housing limitations</i></center>

<p>&nbsp;</p>


<p>
As mentioned earlier, the first goal in our grand scheme of things is to scrape a single page of Craigslist. Let’s open up a single page, physically enter in some filters (parameters), and look at the content (to the image on the left). For my budget, I was looking for a place less than $900 and wanted the most recent posts to show up first. When I look at the hyperlink created by this query, I get this:
</p>

<p>&nbsp;</p>

<p>
“https://sfbay.craigslist.org/search/roo?query=<b>glen+park</b>&sort=<b>date</b>&max_price=<b>900</b>&availabilityMode=0”
</p>

<p>&nbsp;</p>

<p>
What do ya know? It looks like the headers in the url include exactly the parameters I entered, also known as a query string. Let’s start with that for our code.
</p>


<p>
<pre class="my-pre"><code>
url = https://sfbay.craigslist.org/search/roo?query=glen+park&sort=date&max_price=1000&availabilityMode=0
driver.get(url)
</code></pre>
</p>


<p>
At this point, you might be wondering why I chose Chromedriver instead of other web drivers. The main reason is because of the developer tools which can be opened with these three keys:
</p>

<p><strong>Option + Command + U </strong></p>


<p>By clicking those three keys simultaneously, you can view the HTML code behind any webpage.</p>


![Source](/assets/img/blog/craigslist/craigslist5.jpeg)
<center><i style="color:#a6a6a6">Inside the developer source with the tag information we need</i></center>

<p>&nbsp;</p>


<p>By searching for a single title post, we can then extract the tags and headers of that post. In this case, notice the “p” tag with class “result-info” at the top of the image above. Within this tag includes the key text (specifically the “Room for Rent in Lafayette”, “$1100”, and “(lafayetter / orinda / moraga)” that we can scrape with a .text.</p>

<p>
<pre class="my-pre"><code>
craig_title = []
all_posts = driver.find_elements_by_class_name("result-row")
for post in all_posts:
    craig_title.append(post.text)
</code></pre>
</p>

<p>
Now we’ve got all the important information from the headlines of posts! The next thing we’ll want to do to scrape this single page is to get not just the title information, but also the webpage that the title leads to.
</p>

<p>&nbsp;</p>


<p><strong>Step 4: Scrape the link to the individual post</strong></p>


<p>
Here comes the tricky part: how can I get the link to the individual seller’s page if I can’t click on the link and get it’s hyperlink without redirecting the Selenium browser?
</p>

<p>
Solution: use our web-scraping tools to look for a title and then generalize it to all posts on a page.
</p>


![](/assets/img/blog/craigslist/craigslist6.jpeg)
<center><i style="color:#a6a6a6">What we see on the website</i></center>


<p>&nbsp;</p>

![](/assets/img/blog/craigslist/craigslist7.jpeg)
<center><i style="color:#a6a6a6">What the code on the website looks like</i></center>

<p>&nbsp;</p>

<p>
By searching for the original title post, we can see the “a” tag with class “href” equal to post link that we’re searching for. We’ll first open the url with urllib2 (because we don’t want to remotely open another page with selenium), and then follow a similar process as Step 2 to get the post hyperlink
</p>

<p>
<pre class="my-pre"><code>
link_list = []
html_page = urllib2.urlopen(url)
soup = BeautifulSoup(html_page, "lxml")
for element in soup.findAll("a", {"class": "result-title hdrlnk"}):
    print element['href']
</code></pre>
</p>


<p>We have now scraped the post titles and their respective links.</p>

<p>&nbsp;</p>


<p><strong>Step 5 and 6: Iterating through the process and generalizing</strong></p>

<p>We can replace all parameters with variables to generalize our scraping.</p>

<p>For example, turn this:</p>

<p>
<pre class="my-pre"><code>
craig_title = []
url = <b>"https://sfbay.craigslist.org/search/roo?query=glen+park&sort=date&max_price=1000&availabilityMode=0"</b>
driver.get(url)
allthing = driver.find_elements_by_class_name("result-row")
for post in allthing:
    craig_title.append(post.text)
</code></pre>
</p>

<p>Into this:</p>

<p>
<pre class="my-pre"><code>
<b>place = ['glen+park']</b>
<b>price = '900'</b>
craig_title = []
url = <b>"https://sfbay.craigslist.org/search/roo?query="+ \
                str(place)+ 
                "&sort=date&max_price="+
                str(price)+
                "&availabilityMode=0"</b>
driver.get(url)
all_posts = driver.find_elements_by_class_name("result-row")
for post in all_posts:
    craig_title.append(post.text)
</code></pre>
</p>

<p>We can repeat this for all the other variables such as while we loop through the pages and append to the lists.</p>


<p>&nbsp;</p>


<p><strong>Step 7: Bring it all together</strong></p>

<p>We have the pieces to (1) scrape a page’s titles and links, (2) move to another page and repeat step 1, and (3) append information all to a list. So now, all we have to do is create a nice little function that allows us to include a number of arguments and return a data frame. Let’s call it: <b>craig_list()</b>.</p>


<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
from selenium import webdriver
from bs4 import BeautifulSoup
import urllib2
import re
import pandas as pd
import numpy as np
pd.set_option('max_colwidth', 5000)
pd.options.display.max_rows = 999
from IPython.display import display, HTML
driver = webdriver.Chrome(executable_path="/Users/shsu/Downloads/chromedriver")
def craig_list(price, location_list=list):
    """
    craig_list is a function that creates the condensed Craigslist post information
    Inputs:
        price_max: integer of the maximum price of houses one wants
        places: list of strings where the string represents the region of choice 
    Output:
        a dataframe with the description and the link of the post 
    """
    craig_title = []
    link_list = []
    for place in location_list:
        print("--------- MOVING TO PLACE: " + str(place) + " -----")
        link_list.append(" ")
        craig_title.append(str(place).upper())
        
        url = "https://sfbay.craigslist.org/search/roo?query="+ \
                str(place)+ "&sort=date&max_price="+ str(price)+"&availabilityMode=0"
        driver.get(url)
        all_posts = driver.find_elements_by_class_name("result-row")
        for post in all_posts:
            craig_title.append(post.text)
    
        html_page = urllib2.urlopen(url)
        soup = BeautifulSoup(html_page, "lxml")
        for pid in soup.findAll("a", {"class": "result-title hdrlnk"}):
         link_list.append(pid['href'])
    craig_df = pd.DataFrame(np.column_stack([craig_title,link_list]), 
columns = ["Info", "Link"])
    return craig_df
places = ["glen+park", "balboa+park"]
price_max = 900
craig_df = craig_list(price_max, places)
display(craig_df)
</code></pre>
</p>

<p>
Just like that, you too can now save 40 minutes of your day searching Craigslist for housing to finally rent a sofa in a converted living room!
</p>

<p>&nbsp;</p>


![](/assets/img/blog/craigslist/craigslist8.jpeg)
<center><i style="color:#a6a6a6">What the code on the website looks like</i></center>

<p>
So in conclusion, we learned that while scraping appears difficult and time consuming, it can be broken down into easier parts, visualized between the actual user interface and the HTML source behind it, and finally generalized to mimic the entire human behavior. Thanks for reading and good luck on your housing search!
</p>

<p>
P.S. A lot of posts are spammers that copy real posts and give a generic message to “Please send me your contact number and I will communicate with you as soon as possible.”
</p>


<p>&nbsp;</p>


<p>References:</p>


<p> 1. <a href = "https://selenium.dev/downloads/"><u> Selenium Download Page </u></a> </p>
<p> 2. <a href = "https://github.com/parrt/msds692/blob/master/notes/selenium.md"><u> Selenium tutorial from Professor Terence Parr </u></a> </p>
<p> 3. <a href = "https://www.crummy.com/software/BeautifulSoup/bs4/doc/"><u> Beautiful Soup Documentation </u></a></p>
<p> 4. <a href = "https://en.wikipedia.org/wiki/Query_string"><u> Query strings </u></a></p>
<p> 5. <a href = "https://sfbay.craigslist.org/"><u> San Francisco Craigslist </u></a> </p>