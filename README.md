# Python-Web-Scraper
Create a Python web scraper using libraries such as BeautifulSoup and requests to extract data from a website. The scraper should collect relevant information (e.g., headlines, links, or product details) and store it in a structured format like CSV or JSON for easy analysis. 
import requests
import csv
import json
import logging
from bs4 import BeautifulSoup

def setup_logging():
    logging.basicConfig(filename="scraper.log", level=logging.INFO,
                        format="%(asctime)s - %(levelname)s - %(message)s")

def fetch_html(url):
    try:
        response = requests.get(url, headers={"User-Agent": "Mozilla/5.0"})
        response.raise_for_status()
        return response.text
    except requests.exceptions.RequestException as e:
        logging.error(f"Error fetching URL {url}: {e}")
        return None

def parse_html(html, element, class_name):
    soup = BeautifulSoup(html, "html.parser")
    data = []
    for item in soup.find_all(element, class_=class_name):
        data.append(item.get_text(strip=True))
    return data

def save_to_csv(data, filename):
    with open(filename, mode='w', newline='', encoding='utf-8') as file:
        writer = csv.writer(file)
        writer.writerow(["Extracted Data"])
        for row in data:
            writer.writerow([row])
    logging.info(f"Data saved to {filename}")

def save_to_json(data, filename):
    with open(filename, 'w', encoding='utf-8') as file:
        json.dump(data, file, indent=4)
    logging.info(f"Data saved to {filename}")

def main():
    setup_logging()
    url = input("Enter the website URL: ")
    element = input("Enter the HTML element to scrape (e.g., h2, p, div): ")
    class_name = input("Enter the class name (or leave blank for none): ")
    
    html = fetch_html(url)
    if html:
        data = parse_html(html, element, class_name if class_name else None)
        if data:
            print("Extracted Data:")
            for item in data:
                print(item)
            
            save_to_csv(data, "scraped_data.csv")
            save_to_json(data, "scraped_data.json")
        else:
            print("No data found.")
            logging.warning("No data extracted from the given page.")
    else:
        print("Failed to retrieve page content.")

if __name__ == "__main__":
    main()

