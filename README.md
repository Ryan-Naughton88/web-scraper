# Code Documentation

## Overview

This Python script utilizes the requests library to fetch HTML content from the Hacker News website (https://news.ycombinator.com/news) and then uses BeautifulSoup for web scraping to extract information about the top news stories. The script retrieves the information from the first and second pages of the website, combines the data, and finally, filters and sorts the news stories based on the number of votes they have received.

## Dependencies

- `requests`: Used for making HTTP requests to retrieve the HTML content of the web pages.
- `BeautifulSoup`: A library for pulling data out of HTML and XML files, used here for parsing the HTML content.
- `pprint`: Pretty-prints the final list of top news stories.

Make sure to install these dependencies before running the script. You can install them using the following:

```bash
pip install requests
pip install beautifulsoup4
```

## Code Structure

1. **Importing Libraries:**
   - `requests`: For making HTTP requests.
   - `BeautifulSoup`: For parsing HTML content.
   - `pprint`: For pretty-printing the final list.

```python
import requests
from bs4 import BeautifulSoup
import pprint
```

2. **Fetching HTML Content:**
   - Sends HTTP requests to Hacker News for the first and second pages and parses the HTML content.

```python
res = requests.get('https://news.ycombinator.com/news')
res2 = requests.get('https://news.ycombinator.com/news?p=2')
soup = BeautifulSoup(res.text, 'html.parser')
soup2 = BeautifulSoup(res2.text, 'html.parser')
```

3. **Extracting Data:**
   - Selects relevant HTML elements using CSS selectors to extract links and subtext (which includes votes).

```python
links = soup.select('.titleline > a')
subtext = soup.select('.subtext')
links2 = soup2.select('.titleline > a')
subtext2 = soup2.select('.subtext')
```

4. **Combining Data:**
   - Combines the links and subtext from both pages into two mega-lists.

```python
mega_links = links + links2
mega_subtext = subtext + subtext2
```

5. **Sorting Stories by Votes:**
   - Defines a function to sort the list of news stories based on the number of votes in descending order.

```python
def sort_stories_by_votes(hnlist):
    return sorted(hnlist, key=lambda k: k['votes'], reverse=True)
```

6. **Creating Custom Hacker News List:**
   - Defines a function to create a custom list of Hacker News stories based on specific criteria (e.g., votes > 99).

```python
def create_custom_hn(links, subtext):
    hn = []
    for idx, item in enumerate(links):
        title = item.getText()
        href = item.get('href', None)
        vote = subtext[idx].select('.score')
        if len(vote):
            points = int(vote[0].getText().replace(' points', ''))
            if points > 99:
                hn.append({'title': title, 'link': href, 'votes': points})
    return sort_stories_by_votes(hn)
```

7. **Prints the Result:**
   - Calls the functions with the combined data and prints the final list of top news stories.

```python
pprint.pprint(create_custom_hn(mega_links, mega_subtext))
```

## Execution

To execute the script, run it in a Python environment after installing the required dependencies. The output will be a list of top news stories from Hacker News, filtered and sorted based on the specified criteria.
