# mars_scraper

## Background
You're now ready to take on a full web-scraping and data analysis project. You've learned to identify HTML elements on a page, identify their `id` and `class` attributes and use this knowledge to extract information via both automated browsing with Splinter and HTML parsing with Beautiful Soup. You've also learned to scrape various types of information. These include HTML tables and recurring elements, like multiple news articles on a webpage. 

This new assignment consists of two technical products. You will submit the following deliverables:
* Deliverable 1: Scrape titles and preview text from Mars news articles. 
* Deliverable 2: Scrape and analyze Mars weather data, which exists in a table. 

## Part 1: Scrape Titles and Preview Text from Mars News
[Mars News Notebook](https://github.com/marthagriggs9/mars_scraper/blob/main/part_1_mars_news.ipynb)

Mars News [JSON](https://github.com/marthagriggs9/mars_scraper/blob/main/mars_news.json) file and [CSV](https://github.com/marthagriggs9/mars_scraper/blob/main/mars.csv) file.

1. Use automated browsing to visit the [Mars news site](https://static.bc-edx.com/data/web/mars_news/index.html). Inspect the page to identify which elements to scrape.
```ruby
# Visit the Mars news site: https://static.bc-edx.com/data/web/mars_news/index.html
url = 'https://static.bc-edx.com/data/web/mars_news/index.html'

browser.visit(url)
```
2. Create a Beautiful Soup object and use it to extract text elements from the website. 
```ruby
# Create a Beautiful Soup object
html = browser.html
mars_soup = soup(html, 'html.parser')
```

3. Extract the titles and preview text of the news articles that you scraped. Store the scraping results in Python data structures as follows. 
   * Store each title and preview pair in a Python dictionary and give each dictionary two keys: `title` and `preview`. 
   * Store all dictionaries in a Python list. 
   * Print the list in your notebook. 
```ruby
# Parent element that holds all the elements
slide_elem = mars_soup.find_all('div', class_ = 'list_text')
# Create an empty list to store the dictionaries
mars_list = []
# Loop through the text elements
for elem in slide_elem:
    
    # Extract the title and preview text from the elements
    title = elem.find('div', class_ = 'content_title').text
    summary = elem.find('div', class_ = 'article_teaser_body').text
    
    # Store each title and preview pair in a dictionary
    mars_data = {
        "title": "", 
        "preview": ""
    }
    
    # Add the dictionary to the list
    mars_data["title"] = title
    mars_data["preview"] = summary
    
    
    mars_list.append(mars_data)
# Print the list to confirm success
mars_list
```

4. Optionally, store the scraped data in a file (to ease sharing the data with others). To do so, export the scraped data to a JSON file. 
```ruby
import json
print(json.dumps(mars_list, indent=4))
#Export Python List to a JSON file
json_mars = json.dumps(mars_list, indent=4)

import pandas as pd
#Save json as a csv file
mars_df = pd.read_json(json_mars)
mars_df.to_csv('mars.csv', index = False)

browser.quit()
```

## Part 2: Scrape amd Analyze Mars Weather Data

1. Use automated browsing to visit the [Mars Temperature Data Site](https://static.bc-edx.com/data/web/mars_facts/temperature.html). Inspect the page to identify which elements to scrape. Not that the URL is `https://static.bc-edx.com/data/web/mars_facts/temperature.html`.
2. Create a Beautiful Soup object and use it to scrape the data in the HTML table. Not that this can also be achieved by using the Pandas `read_html` function. However, use Beautiful Soup here to continue sharpening your web scraping skills. 
3. Assemble the scraped data into a Pandas DataFrame. The columns should have the same headings as the table on the website. Here's an explanation of the column headings:
   * `id`: the identification number of a single transmission from the Curiosity rover
   * `terrestrial_date`: the date on Earth
   * `sol`: the number of elapsed sols (Martian days) since Curiosity landed on Mars
   * `ls`: the solar longitude
   * `month`: the Martian month
   * `min_temp`: the minimum temperature, in Celsius, of a single Martian day (sol)
   * `pressure`: the atmospheric pressure at Curiosity's location
   
4. Examine the data types that are currently associated with each column. If necessary, cast (or convert) the data to the appropriate `datetime`, `int`, or `float` data types. 
5. Analyze your dataset by using Pandas functions to answer the following questions:
   * How many months exist on Mars?
   * How many Martian (and not Earth) days worth of data exist in the scraped dataset?
   * What are the coldest and the warmest month on Mars (at the location of Curiosity)? To answer this question:
     * Find the average minimum daily temperature for all of the months. 
     * Plot the results as a bar chart. 
   * Which months have the lowest and the highest atmospheric pressure on Mars? To answer this question:
     * Find the average daily atmospheric pressure of all the months.
     * Plot the results as a bar chart.
   * About how many terrestrial (Earth) days exist in a Martian year? To answer this question:
     * Consider how many days elapse on Earth in the time that Mars circles the Sun once. 
     * Visually esitmate the result by plotting the daily minimum temperature. 
6. Export the DataFrame to a CSV file.




