# WebScraping news 
# scraping current news from a news site, what was trending most from that day

# importing all initial necessary programs
from time import time
from time import sleep
from random import randint
from IPython.core.display import clear_output
from warnings import warn
from bs4 import BeautifulSoup
import requests
# defining the function with arguments

def scrape_news(soup, news):

  #find all the elements with class: summary
  raw_news = soup.select('.summary')

  #then extract the information that I need per story
  for news in raw_news:
    news_stories = stories = news.select_one('h3 > a').get_text() # extracting the title for new stories
    headline = headlines = news.select_one('h3 > a').get_text() # extracting the title for headlines
    author = authors = news.select_one('h3 > a').get_text() # extracting the title for authors
    date = dates = news.select_one('h3 > a').get_text() # extracting the title for dates
    synopsis = synopsis_1 = news.select_one('h3 > a').get_text() # extracting the title for synopsis
    time = times = news.select_one('h3 > a').get_text() # extracting the title for times
    tags = [tag.get_text() for tag in news.select('.tags a')] # extract a list of tags
    new_news = {'news_stories': news_stories, 'headline ': headline , 'author': author, 'date': date, 'synopsis': synopsis, 'time': time, 'tags': tags} # construct a dictionary
    news.append(new_news) # add dictionary to list

#prepare to monitor logic
start_time = time() # note the system time when the program starts
request_count = 0 # track the number of requests made

# create variables to store the data
news = []

# variables to handle the request loop and show identity
has_next_page = True
MAX_REQUESTS = 10 # not exceding more than 10 pages
page_number = 1
query = {'tab':'newest', 'page': page_number}
url = 'https://www.space.com/news'
headers = {'user-agent': 'questionscraper - school project (kathleenreily@gmail.com)'}

while has_next_page and request_count < MAX_REQUESTS:
  # keep the output clear
  clear_output(wait = True)

  # make an initial request
  response = requests.get(url, params=query, headers=headers)

    # make sure we got a valid response
  if(response.ok):
    # get the full data from the response
    data = response.text
    soup = BeautifulSoup(data, 'html.parser')
    scrape_news(soup, news)

    # check for the next page
    # look for the presence of element with class *test-pagination-next*
    next_button = soup.select('a[rel="next"]')
    has_next_page = len(next_button) > 0 
  else:
    # display a warning if there are any problems
    warn('Request #: {}, Failed with status code: {}'.format(request_count, response.status_code))
  request_count += 1

  # go to sleep for a bit
  # we use a random number between 1 and 5 so
  # We can wait as long as 5 seconds to make a second request

sleep(randint(1,3))

   # output some logs for monitoring
elapsed_time = time() - start_time
print('Requests: {}, Frequency: {} requests/s, {} questions processed.'.format(request_count, request_count/elapsed_time, len(news)))
  
  # prepare for next iteration
page_number += 1

print('Sraping complete')
print('Requests: {}, Frequency: {} requests/s, {} questions processed.'.format(request_count, request_count/elapsed_time, len(news)))
  
# print the first five questions
news[0:5]

# install this library in order to save as csv
!pip install -U -q PyDrive

from pydrive.auth import GoogleAuth
from pydrive.drive import GoogleDrive
from google.colab import auth
from oauth2client.client import GoogleCredentials

# Authenticate and create the PyDrive client.
# This only needs to be done once in a notebook.
auth.authenticate_user()
gauth = GoogleAuth()
gauth.credentials = GoogleCredentials.get_application_default()
drive = GoogleDrive(gauth)

#create a csv string for news in google drive
import csv
import io


# Create an output stream
output = io.StringIO()

# these are the names of the properties in the dictionary
fieldnames = ['news_stories', 'headline ', 'author', 'date', 'synopsis', 'time', 'tags']

# create a writer object, it can write dictionaries to the output stream
writer = csv.DictWriter(output, fieldnames=fieldnames)

# write all the headings 
writer.writeheader()

for new in news:
  writer.writerow(question)

# Create & upload a text file.
uploaded = drive.CreateFile({'title': 'news.csv'})
uploaded.SetContentString(output.getvalue())
uploaded.Upload()
print('Uploaded file with ID {}'.format(uploaded.get('id'))) 
# when printed:
Requests: 1, Frequency: 0.2061814357544607 requests/s, 0 questions processed.
Sraping complete
Requests: 1, Frequency: 0.2061814357544607 requests/s, 0 questions processed.
Uploaded file with ID 12os8AQ6z4Ta0ozQ-hnNOYd9WWUrbHTUb



