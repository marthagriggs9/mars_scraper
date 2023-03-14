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

[Mars Weather Notebook](https://github.com/marthagriggs9/mars_scraper/blob/main/part_2_mars_weather-checkpoint.ipynb)

1. Use automated browsing to visit the [Mars Temperature Data Site](https://static.bc-edx.com/data/web/mars_facts/temperature.html). Inspect the page to identify which elements to scrape. Not that the URL is `https://static.bc-edx.com/data/web/mars_facts/temperature.html`.
```ruby
# Visit the website
# https://static.bc-edx.com/data/web/mars_facts/temperature.html

url = 'https://static.bc-edx.com/data/web/mars_facts/temperature.html'

browser.visit(url)
```

2. Create a Beautiful Soup object and use it to scrape the data in the HTML table. Note that this can also be achieved by using the Pandas `read_html` function. 
```ruby
# Read in HTML tables into a DataFrame

mars_df = pd.read_html('https://static.bc-edx.com/data/web/mars_facts/temperature.html')

table_df = mars_df[0]
```
3. Assemble the scraped data into a Pandas DataFrame. The columns should have the same headings as the table on the website. Here's an explanation of the column headings:
   * `id`: the identification number of a single transmission from the Curiosity rover
   * `terrestrial_date`: the date on Earth
   * `sol`: the number of elapsed sols (Martian days) since Curiosity landed on Mars
   * `ls`: the solar longitude
   * `month`: the Martian month
   * `min_temp`: the minimum temperature, in Celsius, of a single Martian day (sol)
   * `pressure`: the atmospheric pressure at Curiosity's location

```ruby
# Extract all rows of data
table_df.columns = ['id', 'terrestrial_date', 'sol', 'ls', 'month', 'min_temp', 'pressure']

table_df

# Create a Pandas DataFrame by using the list of rows and a list of the column names
mars_table_df = pd.DataFrame(table_df)
# Confirm DataFrame was created successfully
mars_table_df
```

4. Examine the data types that are currently associated with each column. If necessary, cast (or convert) the data to the appropriate `datetime`, `int`, or `float` data types. 
```ruby
# Examine data type of each column
mars_table_df.dtypes 

# Change data types for data analysis
mars_table_df['terrestrial_date'] = pd.to_datetime(mars_table_df['terrestrial_date'])

# Confirm type changes were successful by examining data types again
mars_table_df.dtypes
```

5. Analyze your dataset by using Pandas functions to answer the following questions:
   * How many months exist on Mars?
     ```ruby 
      How many months are there on Mars?
      months_on_mars = len(mars_table_df['month'].unique())
      print(f"There are", months_on_mars, 'months on Mars. ')
      There are 12 months on Mars. 
      ```
   * How many Martian (and not Earth) days worth of data exist in the scraped dataset?
     ``` ruby
        How many Martian days' worth of data are there?
        martian_days = mars_table_df['sol'].count()
        print(f"There are",  martian_days, "days worth of data in the scraped data set. ")
        There are 1867 days worth of data in the scraped data set. 
        ```
  * What are the coldest and the warmest month on Mars (at the location of Curiosity)? To answer this question:
     * Find the average minimum daily temperature for all of the months. 
     
       ```ruby
       average_min_temp = []

       for i in range(1, months_on_mars+1):
           weather_per_month = {'month_number': "", "average_min_temp": ""}
           avg_min_temp_month = mars_table_df.loc[mars_table_df['month'] == i]['min_temp'].mean()
           round_temp = round(avg_min_temp_month, 0)
           weather_per_month["month_number"] = i 
           weather_per_month["average_min_temp"] = round_temp
           average_min_temp.append(weather_per_month)
    
       print(average_min_temp)
       #Export list to JSON file
       import json
       json_mars_temp = json.dumps(average_min_temp)
       #Save as DataFrame
       mars_months_df = pd.read_json(json_mars_temp)
       mars_months_df
       
       # Identify the coldest and hottest months in Curiosity's location
       #Coldest Month
       min_avg_temp = mars_months_df['average_min_temp'].min()
       month_min_temp = mars_months_df.loc[mars_months_df['average_min_temp'] == min_avg_temp]
       print(month_min_temp.to_string(index=False))
       #month_min_temp
       
       #Warmest Month
       max_avg_temp = mars_months_df['average_min_temp'].max()
       month_max_temp = mars_months_df.loc[mars_months_df['average_min_temp'] == max_avg_temp]
       print(month_max_temp.to_string(index=False))
       ```
       
      * Plot the results as a bar chart. 
        ``` ruby
        # Plot the average temperature by month
        plt.bar(mars_months_df["month_number"], mars_months_df["average_min_temp"], color= 'cornflowerblue')

        #Create labels for the x and y axis
        plt.xlabel("Mars Month Number")
        plt.ylabel("Average Minimum Temperature (C)")

        #Create Title
        plt.title("Average Minimum Temperature on Mars by Month")
        ```
        ![image](https://user-images.githubusercontent.com/115905663/225116218-6fa33ea9-4eac-4910-ae38-c1aef872e613.png)

  * Which months have the lowest and the highest atmospheric pressure on Mars? To answer this question:
     * Find the average daily atmospheric pressure of all the months.
       ```ruby
       # Average pressure by Martian month
       average_pressure = []

       for i in range(1, months_on_mars+1):
           pressure_per_month = {'month_number': "", "pressure": ""}
           avg_pressure_month = mars_table_df.loc[mars_table_df['month'] == i]['pressure'].mean()
           round_pressure = round(avg_pressure_month, 2)
           pressure_per_month["month_number"] = i 
           pressure_per_month["pressure"] = round_pressure
           average_pressure.append(pressure_per_month)
    
       print(average_pressure)
       
       #Month with lowest pressure
       min_pressure = mars_monthly_pressure_df['pressure'].min()
       month_min_pressure = mars_monthly_pressure_df.loc[mars_monthly_pressure_df['pressure'] == min_pressure]
       print(month_min_pressure.to_string(index=False))
       
       #Month with highest pressure
       max_pressure = mars_monthly_pressure_df['pressure'].max()
       month_max_pressure = mars_monthly_pressure_df.loc[mars_monthly_pressure_df['pressure'] == max_pressure]
       print(month_max_pressure.to_string(index=False))
       
     * Plot the results as a bar chart.
       ```ruby
       #Export list to JSON file
       json_mars_pressure = json.dumps(average_pressure)
       #Save as DataFrame
       mars_monthly_pressure_df = pd.read_json(json_mars_pressure)
       mars_monthly_pressure_df
       
       # Plot the average pressure by month
       plt.bar(mars_monthly_pressure_df["month_number"], mars_monthly_pressure_df["pressure"], color= 'indianred')

       #Create labels for the x and y axis
       plt.xlabel("Mars Month Number")
       plt.ylabel("Average Atmospheric Pressure")

       #Create Title
       plt.title("Average Atmospheric Pressure on Mars by Month")
       ```
       ![image](https://user-images.githubusercontent.com/115905663/225120410-41f25ca9-79d4-4cc3-bc3e-85320c30adc6.png)

  
  * About how many terrestrial (Earth) days exist in a Martian year? To answer this question:
     * Consider how many days elapse on Earth in the time that Mars circles the Sun once. 
       ```ruby
       #How many terrestrial (earth) days are there in a Martian year?
       #one way is to use the column 'ls', which is the position of the sun
       #Find the starting position of the sun from the first data row
       first_ls = mars_table_df['ls'].loc[0]
       first_ls
     
       mars_table_df.loc[mars_table_df['terrestrial_date'].argmin()]
     
       #Find other rows that have 155 as the 'ls' value because that means the sun returned to
       #the same position which would mean Mars traveled around the sun 1 time which equals 1 year
       same_ls = mars_table_df.loc[(mars_table_df['ls'] == first_ls)] 
       same_ls
     
       #subtract the terrestrial date column to see how many earth days passed 
       x = same_ls['terrestrial_date'].loc[0]
       date_on_earth = same_ls['terrestrial_date'].iloc[2]
       days_that_passed = date_on_earth - x
       print(f"In one Martian year, nearly {days_that_passed} pass on Earth.")
       ```
     * Visually esitmate the result by plotting the daily minimum temperature. 
       ```ruby
       #Visually estimate these results by plotting the daily minimum temperature
       plt.bar(mars_table_df['sol'], mars_table_df['min_temp'], color = 'gold')

       #Create labels for the graph
       plt.xlabel("Martian Days")
       plt.ylabel("Minimum Temperature (C)")

       #Create title
       plt.title("Daily Minimum Temperature")
       ```
       ![image](https://user-images.githubusercontent.com/115905663/225121353-7c18a176-61d3-4e2a-9d62-10bb740cdad3.png)

6. Export the DataFrame to a CSV file.
```ruby
# Write the data to a CSV
mars_table_df.to_csv('mars_table.csv', index= False)

browser.quit()
```
## Findings

*  On average, the third and fourth months have the coldest average minimum temperature of -83 degrees Celsius (-117.4 degrees Fahrenheit). The warmest average minimum temperature occurs in the seventh month (-68 degrees Celsius, or -90.4 degrees Fahrenheit). Even the warmest month on Mars is not capable of supporting human life. It is important to note that this is based on the location of the Curiosity rover.
According to [NASA's Solar System Exploration](https://solarsystem.nasa.gov/planets/mars/in-depth/) website, Mars has a thin atmosphere which allows heat from the Sun to escape easily.
*  Atmospheric pressure is, on average, lowest in the sixth month and highest in the ninth. While both Earth and Mars' atmospheric pressure varies with altitude, Mars' atmospheric pressure also varies by season. Information from [Mars Education at Arizona State University](https://marsed.asu.edu/mep/atmosphere) website also mentions that this pressure change is due to the change in amount of CO2 gas in the atmosphere on Mars.
* Information found online, specifically [NASA's Mars Exploration](https://mars.nasa.gov/all-about-mars/facts/) website, informed me that a year on Mars is 687 Earth days. I considered how a year is measured (one revolution around the sun ) and decided to use the 'ls' column (solar longitude) to calculate how many Earth days are in one Martian year. I used this column because the position of the sun would return to the same longitude after on revolution. The first row solar longitude value was 155, and 4 other rows contained this value as well. I then subtracted the 'terrestrial_date' of row 1 (2012-08-16) from row 3 (2014-07-04) and got a result of 687 days.
The bar graph created using the minimum temperature of a single Martian day (min_temp) and Martian days (sol) shows temperature peaks at about 120 days and 800 days. 800-120 = 680 days, so this also matches up with the facts I found on the internet. 


