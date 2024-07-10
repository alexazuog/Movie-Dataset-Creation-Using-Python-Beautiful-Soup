# Movie-Dataset-Creation-Using-Python-Beautiful-Soup
### Overview
This project focuses on developing a comprehensive dataset of Disney movies by scraping information from Wikipedia pages. The goal is to create an interactive and enriched dataset that includes key movie attributes, financial data, release dates, and additional features from external sources like the OMDB API. This dataset will be standardized and stored in a CSV file for further analysis and visualization.
### Objectives
#### Data Extraction
Utilize the BeautifulSoup library to scrape detailed information about Disney movies from Wikipedia, including titles, release dates, budgets, box office revenues, and more.
#### Data Cleaning
Perform extensive data cleaning to ensure consistency and standardization of key attributes like budget and release dates using regular expressions and the datetime library.
#### Data Enrichment
Integrate additional movie features such as IMDB ratings, Metascores, and Rotten Tomatoes scores by using the OMDB API.
#### Data Storage
Convert the cleaned and enriched dataset into a CSV file for easy access and analysis.

## Steps Involved
#### Importing Necessary Libraries:
Utilize libraries such as BeautifulSoup, requests, re, datetime, pandas, and urllib for web scraping, data cleaning, and data handling.
Web Scraping with BeautifulSoup:

#### Create functions to extract key movie attributes from Wikipedia pages.
Scrape a list of Disney movie titles and URLs from the Wikipedia page "List of Walt Disney Pictures films".
Extract detailed information for each movie by navigating to individual movie pages.
```python
def get_content_value(row_data):
    if row_data.find("li"):
        return [li.get_text(" ", strip=True).replace("\xa0", " ") for li in row_data.find_all("li")]
    elif row_data.find("br"):
        return [text for text in row_data.stripped_strings]
    else:
        return row_data.get_text(" ", strip=True).replace("\xa0", " ")

def clean_tags(soup):
    for tag in soup.find_all(["sup", "span"]):
        tag.decompose()
        
def get_info_box(url):

    r = requests.get(url)
    soup = bs(r.content)
    info_box = soup.find(class_="infobox vevent")
    info_rows = info_box.find_all("tr")
    
    clean_tags(soup)

    movie_info = {}
    for index, row in enumerate(info_rows):
        if index == 0:
            movie_info['title'] = row.find("th").get_text(" ", strip=True)
        else:
            header = row.find('th')
            if header:
                content_key = row.find("th").get_text(" ", strip=True)
                content_value = get_content_value(row.find("td"))
                movie_info[content_key] = content_value
    return movie_info    
```

**Check if it works**
```python
get_info_box("https://en.wikipedia.org/wiki/One_Little_Indian_(film)")
```
![screenshoot fo BS1](https://github.com/alexazuog/Movie-Dataset-Creation-Using-Python-Beautiful-Soup/assets/115574934/918d0225-3434-4679-be22-0ea556073cde)

Data Cleaning:
- Standardize budget and running time attributes using regular expressions.
- Convert budget values from word syntax (e.g., "$12.2 million") to numerical values.
- Standardize release dates to a uniform datetime format.

OMDB API Integration:
- Fetch additional movie features such as IMDB ratings, Metascores, and Rotten Tomatoes scores using the OMDB API.
- Create functions to retrieve and integrate these additional attributes into the dataset.

 ```python
import requests
import urllib
import os
def get_omdb_info(title):
    base_url = "http://www.omdbapi.com/?"
    parameters = {"apikey": "a32de05a", 't': title}
    params_encoded = urllib.parse.urlencode(parameters)
    full_url = base_url + params_encoded
    return requests.get(full_url).json()
def get_rotten_tomato_score(omdb_info):
    ratings = omdb_info.get('Ratings', [])
    for rating in ratings:
        if rating['Source'] == 'Rotten Tomatoes':
            return rating['Value']
    return None
  ```

- Convert the cleaned and enriched dataset into a pandas DataFrame.
- Save the DataFrame as a CSV file for further use.
- Display to check if it works
  
```python
df.head(20)
```
![BS2](https://github.com/alexazuog/Movie-Dataset-Creation-Using-Python-Beautiful-Soup/assets/115574934/e712a8b9-d95f-42cc-be2f-b69b5ba88875)

#### conclusion
This project demonstrates the process of web scraping, data cleaning, and API integration to build a rich and standardized dataset of Disney movies. By leveraging tools like BeautifulSoup and the OMDB API, the project provides a valuable resource for analyzing and visualizing key aspects of Disney's filmography. The final dataset can be used for various analytical purposes, supporting data-driven decision-making and strategic planning for stakeholders.
