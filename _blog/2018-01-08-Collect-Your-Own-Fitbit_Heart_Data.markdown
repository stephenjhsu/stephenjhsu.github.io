---
layout: post
title:  "Collect Your Own Fitbit Heart Data"

description: Collecting and storing intraday heart rate data from Fitbit using Python

image_header: /assets/img/bg-img/12.jpg

pagination: 
  enabled: true
---

![Python Generated Fitbit Cloud](/assets/img/blog/fitbit/fitbit.png)

<p>&nbsp;</p>

<p>
So you’ve got your Fitbit over the Christmas break and you’ve got some New Years Resolutions. You go online and see the graphs on your dashboard but you’re still not pleased. You want more data, more graphs, and more information. Well say no more, because I’m going to teach you how to collect your own Fitbit data using nothing but a little Python code. With this tutorial, you can get your elusive minute by minute data (also known as intraday data), which is not readily available when you first get your Fitbit.
</p>


<p><strong>Step 1</strong>: Set up your account and create the app</p>


<p>The first thing you’ll need to do is create a <u><a href="https://www.fitbit.com/home">Fitbit account</a></u>. Once you’ve done that, you can go to <u><a href="https://dev.fitbit.com/">dev.fitbit.com </a></u>. Under “Manage”, go to “Register An App”. This will lead you to a page that looks like:</p>

<p>&nbsp;</p>

![Application Registration](/assets/img/blog/fitbit/fitbit1.jpeg)
<center><i style="color:#a6a6a6">Application Registration</i></center>

<p>&nbsp;</p>

<p>
For the application website and organization website, name it anything starting with “http://” or “https://”. Secondly, make sure the OAuth 2.0 Application Type is “Personal” as this is key to allowing us to download our intraday data. Lastly, make sure the Callback URL is “http://127.0.0.1:8080/” in order to get our Fitbit API to connect properly. After that, click on the agreement box and submit. 
</p>

<p>
<strong>NOTE:</strong>
depending on the app, we may need an additional step to fill out a form in order to gain permission to our intraday data at <u><a href="https://dev.fitbit.com/build/reference/web-api/help/">this link</a></u>. Fitbit is supportive of personal projects and any other non profit research, so these should already be callable, but commercial apps might take longer to be approved.
</p>


<p>
After that, you’ll be redirected to a page looking like this:
</p>

<p>&nbsp;</p>

![Registered Application](/assets/img/blog/fitbit/fitbit2.jpeg)
<center><i style="color:#a6a6a6">Registered Application</i></center>

<p>&nbsp;</p>

<p>
<strong>Step 2:</strong> The API
</p>



<p>
Once Step 1 is completed, our next step is to use a <u><a href="https://github.com/orcasgit/python-fitbit">Fitbit unofficial API</a></u>. Click the green button on the right side to download the repo and afterwards unzip the file. After that, open up the command line to change directories to the directory containing the unzipped files and run a quick ‘sudo pip install -r requirements/base.txt’ .
</p>

<p>
Now we’re finally ready to start coding. First things first, we will need to import the necessary packages and bring in our Client_ID and Client_Secret from earlier to authorize ourselves.
</p>

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

<p>Using the ID and Secret, we can obtain the access and refresh tokens that authorize us to get our data.</p>


<p>
<pre class="my-pre"><code>
server = Oauth2.OAuth2Server(CLIENT_ID, CLIENT_SECRET)
server.browser_authorize()

ACCESS_TOKEN = str(server.fitbit.client.session.token['access_token'])
REFRESH_TOKEN = str(server.fitbit.client.session.token['refresh_token'])

auth2_client = fitbit.Fitbit(CLIENT_ID, CLIENT_SECRET, oauth2=True, access_token=ACCESS_TOKEN, refresh_token=REFRESH_TOKEN)
</code></pre>
</p>

<p>&nbsp;</p>

![Initial Page](/assets/img/blog/fitbit/fitbit3.jpeg)

<center><i style="color:#a6a6a6">What the page should look like after authorization and signing in</i></center>

<p>&nbsp;</p>

<p>
We’re in! … but not so fast. We’ve authenticated ourselves but we still haven’t gotten our data. Before jumping in, I have one handy trick that will save a lot of manual typing: many of the calls require a date format of (YYYY-MM-DD) as a string format. So if you were to collect your data daily, we’ll have to change this string manually everyday. Instead, we can import the useful ‘datetime’ package and let it do the formatting for us instead.
</p>




<p>
<pre class="my-pre"><code>
yesterday = str((datetime.datetime.now() - datetime.timedelta(days=1)).strftime("%Y%m%d"))

yesterday2 = str((datetime.datetime.now() - datetime.timedelta(days=1)).strftime("%Y-%m-%d"))

today = str(datetime.datetime.now().strftime("%Y%m%d"))
</code></pre>
</p>

<p>&nbsp;</p>

<p><strong>Step 3:</strong> Acquire the data</p>

<p>We can finally now call for our data.</p>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
fit_statsHR = auth2_client.intraday_time_series('activities/heart', base_date=yesterday2, detail_level='1sec')
</code></pre>
</p>

<p>&nbsp;</p>

![Heart Rate Data](/assets/img/blog/fitbit/fitbit4.jpeg)
<center><i style="color:#a6a6a6">Heart Rate Data</i></center>

<p>&nbsp;</p>

<p>
While this data looks readable to us, it’s not yet ready to be saved on our computer. The following bit of code reads the dictionary format and iterates through the dictionary values — saving the respective time and value values as lists before combining both into a <a href="https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html"><u>pandas data frame.</u></a>
</p>


<p>
<pre class="my-pre"><code>
time_list = []
val_list = []

for i in fit_statsHR['activities-heart-intraday']['dataset']:
    val_list.append(i['value'])
    time_list.append(i['time'])

heartdf = pd.DataFrame({'Heart Rate':val_list,'Time':time_list})
</code></pre>
</p>

<p>&nbsp;</p>


![Pandas Data Frame](/assets/img/blog/fitbit/fitbit5.jpeg)
<center><i style="color:#a6a6a6">Our Newly Created Fitbit pandas data frame</i></center>

<p>&nbsp;</p>

<p>
Now to finally save our data locally. Thinking ahead, we’ll be calling our data about once a data (1 file = 1 day), so we’ll want to have a good naming convention that prevents any future mixups or overriding data. My preferred format is to go with heartYYYMMDD.csv. The following code takes our heart data frame and saves them as a CSV in the /Downloads/python-fitbit-master/Heart/ directory.
</p>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
heartdf.to_csv('/Users/shsu/Downloads/python-fitbit-master/Heart/heart'+ \
               yesterday+'.csv', \
               columns=['Time','Heart Rate'], header=True, \
               index = False)
</code></pre>
</p>



![All data](/assets/img/blog/fitbit/fitbit6.jpeg)
<center><i style="color:#a6a6a6">Small army of heart rate files</i></center>

<p>&nbsp;</p>

<p>But wait! There’s still more!</p>

<p>Reading the <a href="https://python-fitbit.readthedocs.io/en/latest/"><u>documentation </u></a>, there’s plenty of other data we can still collect like:</p>

<p>Our sleep log data:</p>

<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
"""Sleep data on the night of ...."""
fit_statsSl = auth2_client.sleep(date='today')
stime_list = []
sval_list = []

for i in fit_statsSl['sleep'][0]['minuteData']:
    stime_list.append(i['dateTime'])
    sval_list.append(i['value'])

sleepdf = pd.DataFrame({'State':sval_list,
                     'Time':stime_list})

sleepdf['Interpreted'] = sleepdf['State'].map({'2':'Awake','3':'Very Awake','1':'Asleep'})
sleepdf.to_csv('/Users/shsu/Downloads/python-fitbit-master/Sleep/sleep' + \
               today+'.csv', \
               columns = ['Time','State','Interpreted'],header=True, 
               index = False)
</code></pre>
</p>


<p>&nbsp;</p>

![Sleep log](/assets/img/blog/fitbit/fitbit7.jpeg)
<center><i style="color:#a6a6a6">Sleep log</i></center>


<p>Or our sleep summary:</p>


<p>
<pre class="my-pre"><code>
"""Sleep Summary on the night of ...."""

fit_statsSum = auth2_client.sleep(date='today')['sleep'][0]

ssummarydf = pd.DataFrame({'Date':fit_statsSum['dateOfSleep'],
                'MainSleep':fit_statsSum['isMainSleep'],
               'Efficiency':fit_statsSum['efficiency'],
               'Duration':fit_statsSum['duration'],
               'Minutes Asleep':fit_statsSum['minutesAsleep'],
               'Minutes Awake':fit_statsSum['minutesAwake'],
               'Awakenings':fit_statsSum['awakeCount'],
               'Restless Count':fit_statsSum['restlessCount'],
               'Restless Duration':fit_statsSum['restlessDuration'],
               'Time in Bed':fit_statsSum['timeInBed']
                        } ,index=[0])
</code></pre>
</p>


<p>&nbsp;</p>

![Sleep Summary](/assets/img/blog/fitbit/fitbit8.jpeg)
<center><i style="color:#a6a6a6">Sleep Summary</i></center>

<p>&nbsp;</p>

<p>
And that’s all you need to get started on collecting all your Fitbit data! So play around some more, read the Python Fitbit documentation, get lost in it for a whole, find your way out, and see overall how nifty your heart data is. In case you wanted a feel for what you can make with that data, here is a link to one of my analyses of my own heart data.
</p>

<p>
Thanks for taking the time to read my tutorial and feel free to leave a comment or connect on LinkedIn as I’ll be posting more tutorials on data mining and data science.
</p>

<p>References:</p>


<p> 1. <a href = "https://github.com/orcasgit/python-fitbit"><u> Orcas Fitbit API Github </u></a> </p>
<p> 2. <a href = "https://python-fitbit.readthedocs.io/en/latest/"><u> Python-Fitbit documentation </u></a> </p>
<p> 3. <a href = "https://dev.fitbit.com/build/reference/web-api/"><u> Official Fitbit API documentation </u></a></p>
<p> 4. <a href = "https://dev.fitbit.com/build/reference/web-api/help/"><u> Fitbit Intraday Access Form </u></a></p>
<p> 5. <a href = "https://github.com/stephenjhsu/fitbit"><u> Personal Github with all the code </u></a> </p>
<p> 6. <a href = "https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html"><u> Pandas data frame documentation </u></a> </p>

