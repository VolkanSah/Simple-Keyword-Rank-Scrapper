# Keyword-Rank-Checker-in-Python without API 
Searchengines: Bing.de, Bing.com, Google.de, Google.com

```bash
pip install requests beautifulsoup4
```
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
