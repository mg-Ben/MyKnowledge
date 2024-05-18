---
tags:
  - Others
---
# Core principles
It is the process of parsing [[HTML]] and [[XML]] data from websites, typically using Scripts.
## Examples
### Python
You can [[Python#Install package|import these python packages]] to achieve Web scraping:
- BeautifulSoup: `beautifulsoup4` package. It serves for parsing [[HTML]] and [[XML]] documents
- Requests and Requests HTML: `requests`, `requests-html`, `lxml` and `lxml_html_clean` packages
#### Example template for requests packages
```Python
import requests
from requests_html import HTMLSession, HTML

url = "target_URL_to_scrape"

if __name__ == "__main__":
    try:
        session = HTMLSession()
        response = session.get(url)
    except requests.exceptions.RequestException as e:
        print(e)
	# Get html object:
	html_obj = response.html
	# Find all <span></span> elements:
	span_list = html_obj.find('span')
	# Get the attributes of some span object:
	print(span_list[0].attrs)
	# Access to the text attribute for example:
	print(span_list[0].text)
```