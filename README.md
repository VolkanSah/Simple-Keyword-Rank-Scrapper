# Simple Keyword Rank Checker (Scrapper)

A simple Python script that checks the ranking of a given domain for specified keywords on selected search engines.

**WARNING**: Scraping search engine results may violate their terms of service. Please consider using their official APIs (e.g., Google Search Console API or Bing Webmaster API) to obtain keyword rankings in a compliant manner.

## Features

- Supports Google and Bing search engines (google.de, bing.de, google.com, bing.com)
- Allows users to input a domain and 1-5 keywords
- Outputs keyword rankings in a text file
- you can ad your own search engines

## Requirements

- Python 3
- `requests`
- `beautifulsoup4`

## Installation

1. Clone this repository:
2. Install required libraries:

```bash
pip install requests beautifulsoup4
```
## Usage


```python
import requests
from bs4 import BeautifulSoup
import re
import time

def get_search_url(search_engine, query):
    if search_engine == "google.de" or search_engine == "google.com":
        url = f"https://www.{search_engine}/search?q={query}"
    elif search_engine == "bing.de" or search_engine == "bing.com":
        url = f"https://www.{search_engine}/search?q={query}"
    return url

def get_rank(url, domain, search_engine):
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:89.0) Gecko/20100101 Firefox/89.0"
    }
    response = requests.get(url, headers=headers)
    soup = BeautifulSoup(response.text, "html.parser")

    rank = -1

    if search_engine.startswith("google"):
        search_results = soup.find_all("div", class_="tF2Cxc")
        for index, result in enumerate(search_results):
            link = result.find("a")["href"]
            if domain in link:
                rank = index + 1
                break

    elif search_engine.startswith("bing"):
        search_results = soup.find_all("li", class_="b_algo")
        for index, result in enumerate(search_results):
            link = result.find("a")["href"]
            if domain in link:
                rank = index + 1
                break

    return rank

def save_to_file(filename, data):
    with open(filename, "w") as file:
        for item in data:
            file.write(f"{item}\n")

def main():
    domain = input("Geben Sie die Domain ein: ")
    search_engine = input("Wählen Sie die Suchmaschine aus (google.de, bing.de, google.com, bing.com): ")
    keywords = input("Geben Sie 1-5 Keywords ein (getrennt durch Kommas): ").split(",")

    results = []

    for keyword in keywords:
        query = keyword.strip().replace(" ", "+")
        search_url = get_search_url(search_engine, query)
        rank = get_rank(search_url, domain, search_engine)
        results.append(f"{keyword.strip()}: {rank}")

    save_to_file("keyword_rankings.txt", results)
    print("Die Keyword-Rankings wurden in 'keyword_rankings.txt' gespeichert.")

if __name__ == "__main__":
    main()

```

1. Run the script:

```bash
python keyword_rank_checker.py
```
or
```bash
python3 keyword_rank_checker.py
```
2. Follow the prompts to input the domain, search engine, and keywords.

3. The keyword rankings will be saved in a text file named `keyword_rankings.txt`.

## License

This project is licensed by Volkan Kücükbudak. See the [LICENSE](LICENSE) file for more details. 

## Disclaimer

The author of this script is not responsible for any misuse or violations of search engines' terms of service. Please use this tool responsibly and respect the rules and guidelines of the respective search engines.


