---
layout: post
title:  "Collecting Weather to Boost Data Science Models with Selenium"

description: Amplify geospatial machine learning projects easily with this tutorial

image_header: /assets/img/bg-img/13.jpg

pagination: 
  enabled: true
---

![Weather Data](/assets/img/blog/weather/weather1.png)


<p>&nbsp;</p>

<p>
Have you ever wonder how weather data would affect your data set or wanted to understand the historical weather patterns in your local city? Do you find it difficult to find weather data for your specific needs? I did.
I was working on a Kaggle competition involving geospatial data and I wanted more relevant data that was compatible with my data set. Unfortunately, I couldn’t find anything. I spent days looking and all I found was a website that would only provide weather condition such as whether or not the skies were cloudy or clear on a given day. That wasn’t enough for me.
</p>

<p>
TLDR: So instead, I built my own web scrapper, collected my own data, and applied it to my data set. The results led to a model that was more generalizable to new data sets. And now you can improve your results too with this tutorial.
</p>

<p>
I used Selenium to build a web scraper with Python to collect weather information such as Temperature, Humidity, Wind Speed, and Weather Conditions from <u><a href="https://www.wunderground.com">https://www.wunderground.com/</a></u>.
</p>


<p>&nbsp;</p>

![Source](/assets/img/blog/weather/weather2.png)
<center><i style="color:#a6a6a6">Interface of wunderground.com</i></center>


<p>&nbsp;</p>

<p>
What makes this website different from your typical websites is that the information is based on the weather conditions of nearby airports. While this sounds counter-intuitive, it is in fact generalizable to cities close to those airports and more informative than typical sites. This makes it easy to search for the closest airports using zip codes.
</p>

<p>
If you scroll all the way down to the bottom, you’ll see a gold mine of weather data for you to mine. Beautiful isn’t it? You can mine this for specific days or a sequence of days.
</p>



<p>&nbsp;</p>

![](/assets/img/blog/weather/weather3.png)

<p>
Here are my steps on how I collect the data from this table for a range of dates. You can skip to the bottom of the page if you want to see the full code snippet.
</p>

<p><strong>Step 1:</strong> Load the packages need</p>


<p>
<pre class="my-pre"><code>
import numpy as np
import pandas as pd
import time
import datetime
import re
from selenium import webdriver
</code></pre>
</p>



<p>
Selenium is a tool used to automate tasks and actions with a browser through programming languages such as Python or Java. You can think about it as controlling your web browser with commands that you provide it. We are going to use it to collect data from HTML webpages.
</p>

<p>
To initialize Selenium, you’ll need to load the webdriver at given path. (Here is the installation guide for Selenium using Python: <u><a href="http://selenium-python.readthedocs.io/installation.html">http://selenium-python.readthedocs.io/installation.html)</a></u>
</p>



<p>
<pre class="my-pre"><code>
driver = webdriver.Chrome('/usr/local/bin/chromedriver')
driver.get('https://www.wunderground.com/history/airport/KSFO/2018/03/14/DailyHistory.html?req_city=San%20Francisco&req_state=CA&reqdb.zip=94128&reqdb.magic=4&reqdb.wmo=99999')
</code></pre>
</p>


<p>
webdriver.Chrome will load the required software to run Selenium on Chrome. driver.get() will open a new web browser that it can control.
</p>


<p>&nbsp;</p>

<p><strong>Step 2:</strong> Get a sequence of dates to search for</p>


<p>&nbsp;</p>

<p>
<pre class="my-pre"><code>
def list_dates(start,end):
    """ This creates a list of of dates between the 'start' date and the 'end' date """
    # create datetime object for the start and end dates
    start = datetime.datetime.strptime(start, '%Y-%m-%d')
    end = datetime.datetime.strptime(end, '%Y-%m-%d')
    # generates list of dates between start and end dates
    step = datetime.timedelta(days=1)
    dates = []
    while start <= end:
        dates.append(start.date())
        start += step
    # return the list of dates in string format
    return [str(date) for date in dates]
</code></pre>
</p>


<p>
I wrote the list_dates() function to produce a sequence of dates from a start date to an end date. This is useful because you won’t need to need to manually input dates.
</p>





<p>
<pre class="my-pre"><code>
# this dictionary is used to map the months produced in the previous function with the full name of the month 
    month_dict ={
    1:'January',
    2:'February',
    3:'March',
    4:'April',
    5:'May',
    6:'June',
    7:'July',
    8:'August',
    9:'September',
    10:'October',
    11:'November',
    12:'December'
}
def date_part(data,f_mat ='%Y-%m-%d'):
    """Extracts the date information produced by list_dates() for Month,Day,Year """
    # creates a pandas dataframe of dates
    dates = pd.DataFrame(data,columns=['date'])
    date_time = dates['date']
    fld = pd.to_datetime(date_time, format=f_mat)
    for n in ('Month', 'Day','Year'):
        dates[n] = getattr(fld.dt,n.lower())
    dates['Month'] = dates['Month'].map(month_dict)
    return dates
</code></pre>
</p>



<p>
The date_part() function is used to break down the dates produced by list_date() into Month, Day, and Year so that it can easily be sent to wunderground to search for a date to collect data from. For example, both functions will produce the following pandas dataframe:
</p>



<p>&nbsp;</p>


<p>
<pre class="my-pre"><code>
start = '2014-1-1'
end = '2014-1-4'
date = list_dates(start,end)
date = date_part(date,'%Y-%m-%d')
</code></pre>
</p>


<p>&nbsp;</p>

![](/assets/img/blog/weather/weather4.png)
<center><i style="color:#a6a6a6">Date dictionary to be used to scrape specific weather dates</i></center>



<p>&nbsp;</p>

<p><strong>Step 3:</strong> Search by location</p>

<p>
Now that we have the date dictionary, you will need to provide a zip code so that the wunderground can pull up the weather information. You can automate this task with Selenium. If you go to the search bar, right-click it, and click inspect, you will get HTML tag information about that search bar. This is generalizable to any HTML-JavaScript-CSS feature on the web page. For example, here you can see that the unique ‘id’ for the search bar:
</p>


<p>&nbsp;</p>

![Source](/assets/img/blog/weather/weather5.png)
<center><i style="color:#a6a6a6">Inspecting source code of wunderground web page</i></center>


<p>&nbsp;</p>

![Source](/assets/img/blog/weather/weather5.png)
<center><i style="color:#a6a6a6">Inside the developer source with the tag information</i></center>



<p>&nbsp;</p>


<p>
To send a zip code to the search bar, the following code will look for the search bar by a specific ‘id’ with ‘find_element_by_id’, clear the search bar, send the zip code, and finally submit it to wunderground to search for the city.
</p>


<p>
<pre class="my-pre"><code>
zipcode='91770'
search=driver.find_element_by_id('history-icao-search')
search.clear() # clears field
search.send_keys(zipcode) # send zipcode to search field
search.submit() # submits zip code to search
</code></pre>
</p>


<p>&nbsp;</p>

<p><strong>Step 4:</strong> Search for date </p>

![Source](/assets/img/blog/weather/weather6.png)
<center><i style="color:#a6a6a6">Inside the developer source with the tag information</i></center>


<p>&nbsp;</p>

<p>
You can similarly search for dates like Step 3. By looking for HTML tags associated to the ‘Weather History Date’ pull down menus, you’ll see that it contains class attributes such as month, day and year. We can use this to send information to search for those pull down menus. You can iterate through the pandas data frame produced from Step 2 to search the weather of a given date.
</p>

<p>
<pre class="my-pre"><code>
Month='March'
Day='14'
Year='2018'
month = driver.find_element_by_class_name('month')
month.send_keys(Month)
day = driver.find_element_by_class_name('day')
day.send_keys(Day)
year = driver.find_element_by_class_name('year')
year.send_keys(Year)
year.submit()
</code></pre>
</p>


<p>&nbsp;</p>

<p><strong>Step 5:</strong> Collect weather data</p>

![](/assets/img/blog/weather/weather7.png)
<center><i style="color:#a6a6a6">Inside the developer source with the tag information</i></center>



<p>&nbsp;</p>

<p>
We’re now ready to collect the weather data. We can see that the weather table is contained in this div tag with unique ‘id’ of ‘observations_details’. We can use this information to scrape the whole weather table with a simple line of code:
</p>


<p>
<pre class="my-pre"><code>
weatherdata = driver.find_elements_by_id('observations_details')[0].text
</code></pre>
</p>



<p>&nbsp;</p>


<p><strong>Step 6:</strong> Bring it all together</p>

<p>
Finally, we can combine all the previous steps into a compact script. I have also including some preprocessing steps and a function in runs everything with one function:
</p>







<p>
<pre class="my-pre"><code>
from selenium import webdriver
import time
import numpy as np
import pandas as pd
import datetime
import pickle
import re
def list_dates(start,end):
    """ This creates a list of of dates between the 'start' date and the 'end' date """
    # create datetime object for the start and end dates
    start = datetime.datetime.strptime(start, '%Y-%m-%d')
    end = datetime.datetime.strptime(end, '%Y-%m-%d')
    # generates list of dates between start and end dates
    step = datetime.timedelta(days=1)
    dates = []
    while start <= end:
        dates.append(start.date())
        start += step
    # return the list of dates in string format
    return [str(date) for date in dates]
# this dictionary is used to map the months produced in the previous function with the full name of the month 
month_dict ={
    1:'January',
    2:'February',
    3:'March',
    4:'April',
    5:'May',
    6:'June',
    7:'July',
    8:'August',
    9:'September',
    10:'October',
    11:'November',
    12:'December'
}
def date_part(data,f_mat ='%Y-%m-%d'):
    """Extracts the date information produced by list_dates() for Month,Day,Year """
    # creates a pandas dataframe of dates
    dates = pd.DataFrame(data,columns=['date'])
    date_time = dates['date']
    fld = pd.to_datetime(date_time, format=f_mat)
    for n in ('Month', 'Day','Year'):
        dates[n] = getattr(fld.dt,n.lower())
    dates['Month'] = dates['Month'].map(month_dict)
    return dates
def scrapper(dates,zipcode):
    data=[] # list to append scrapped data
    # submits the zipcode to find the closest weather center
    search= driver.find_element_by_xpath('//*[@id="history-icao-search"]')
    search.clear()
    search.send_keys(zipcode)
    search.submit()
    time.sleep(3) # sleep timer to wait for page to load (not necessary)
    
    # iterates through provided list of dates to scrap weather for
    for i,v in dates.iterrows():
        # inputs month, day, year into website to view information
        month = driver.find_element_by_class_name('month')
        month.send_keys(v['Month'])
        day = driver.find_element_by_class_name('day')
        day.send_keys(v['Day'])
        year = driver.find_element_by_class_name('year')
        year.send_keys(v['Year'])
        year.submit() # submits to search for month, day, year
#         time.sleep(3) # sleep timer to wait for page to load (not necessary)
# scraps table on the bottom for weather information
        weatherdata = driver.find_elements_by_id('observations_details') # locates the data
        x = weatherdata[0].text # scrapes that data
        x= re.sub(r'[^\x00-\x7F]+',' ', x) # removes unicode
        x = x.split('\n') # breaks the data into observations per row
        x = x[1:-1] # removes the last line
        data.extend([i+' '+v['date'] for i in x]) # appends all scraped data
    return data
def preprocess_data(data):
    """Preprocess the scraped data and load the data into a pandas dataframe"""
    dt = [i.replace('Calm Calm', 'Calm 0.0 mph') for i in data]
    dt = [i.replace(' AM', 'AM') for i in dt]
    dt = [i.replace(' PM', 'PM') for i in dt]
    dt = [i.replace('%', '') for i in dt]   
    dt = [i.replace(' mi', '') for i in dt]
    dt = [i.replace(' mph', '') for i in dt]
    dt = [i.replace(' in', '') for i in dt]
    dt = [re.sub(' +',' ',i) for i in dt]
dt = [i.replace('Mostly ', 'Mostly') for i in dt]
    dt = [i.replace('Partly ', 'Partly') for i in dt]
    dt = [i.replace('Scattered ', 'Scattered') for i in dt]
    dt = [i.replace('Light ', 'Light') for i in dt]
    dt = [i.replace('Heavy ', 'Heavy') for i in dt]
    
    dt = [i.replace('Fog , Rain', 'Rain') for i in dt]
    dt = [i.replace('Fog , Snow', 'FogSnow') for i in dt]
    dt = [i.replace('Fog', ' ',1) for i in dt]
    dt = [i.replace('Rain , Thunderstorm', 'RainThunderstorm') for i in dt]
    
    dt = [i.replace('Thunderstorm', '',1) for i in dt]
    dt = [i.replace('Thunderstorms and Rain', 'ThunderstormsandRain') for i in dt]
dt = [i.replace('Rain', '',1) for i in dt]
    dt = [i.replace('Snow', '',1) for i in dt]
#     dt = [i.replace('Light Drizzle', 'LightDrizzle') for i in dt]
    
    dt = [i.replace('F', '',) for i in dt]
    dt = [i.replace(' og', ' Fog') for i in dt]
    dt = [i.replace('Patches of Fog', 'PatchesofFog',1) for i in dt]
    dt = [i.replace('Lightreezing Rain', 'LightFreezingRain') for i in dt]
  
    dt = [i.split() for i in dt]
    dt = [ i[:2] +i[-10:] for i in dt]
    
    dt = pd.DataFrame(dt,columns = ['time','temp(F)','dewpoint(F)','humidity(%)','pressure(in)','visibility(mi)','winddir','windspeed(mph)','gustspeed(mph)','precip(in)','conditions','date'])
    dt['time'] = [datetime.datetime.strftime(datetime.datetime.strptime(val, "%I:%M%p"), "%H:%M") for val in dt['time']]
    return dt
def weather_scrapper(start_date,end_date, zipcode):
    """final webscrapper function"""
    dates = list_dates(start_date,end_date)
    dates = date_part(dates,'%Y-%m-%d')
    data = scrapper(dates,zipcode)
    return preprocess_data(data)
####################################################################
driver = webdriver.Chrome('/usr/local/bin/chromedriver')
x = 'https://www.wunderground.com/history/airport/KSFO/2018/2/24/DailyHistory.html?req_city=San%20Francisco&req_statename=California'
driver.get(x)
# YYYY-MM-DD
start = '2014-1-1'
end = '2014-1-5'
zipcode = '10001'
weather_scrapper(start,end,zipcode)
</code></pre>
</p>


<p>&nbsp;</p>

<p>
Congratulations! Now you can collect your weather data with Selenium and enhance all your Kaggle / work / school / side projects without compromising validation scores! You can also use what you’ve from this and collect data from other sources too!
</p>


<p>&nbsp;</p>

<p>References:</p>


<p> 1. <a href = "https://www.wunderground.com/"><u> Weather Underground </u></a> </p>
<p> 2. <a href = "https://www.seleniumhq.org/download/"><u> Selenium Download </u></a> </p>
<p> 3. <a href = "https://github.com/davidkes/Weather-Data-Scaper"><u> Personal Github </u></a></p>
