# Extracting-and-Visualizing-Stock-Data
Python Project for Data Science / Final Assignment

# Question 1: Use yfinance to Extract Stock Data
Using the Ticker function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is TSLA.```python

import yfinance as yf
tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
tesla_data.reset_index(inplace=True)
tesla_data.head()

# Question 2: Use Webscraping to Extract Tesla Revenue Data
Use the requests library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm Save the text of the response as a variable named html_data.

import requests
from bs4 import BeautifulSoup
import pandas as pd

url = "https://www.macrotrends.net/stocks/charts/TSLA/tesla/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

tables = soup.find_all("table")
for table in tables:
    if "Tesla Quarterly Revenue" in str(table):
        rows = table.find_all("tr")
        break

tesla_revenue = []
for row in rows[1:]:
    cols = row.find_all("td")
    if len(cols) == 2:
        date = cols[0].text.strip()
        revenue = cols[1].text.strip().replace("$", "").replace(",", "")
        tesla_revenue.append([date, revenue])

df_tesla_revenue = pd.DataFrame(tesla_revenue, columns=["Date", "Revenue"])
df_tesla_revenue.head()

# Question 3: Use yfinance to Extract Stock Data
gamestop = yf.Ticker("GME")
gme_data = gamestop.history(period="max")
gme_data.reset_index(inplace=True)
gme_data.head()

# Question 4: Use Webscraping to Extract GME Revenue Data
url = "https://www.macrotrends.net/stocks/charts/GME/gamestop/revenue"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")

tables = soup.find_all("table")
for table in tables:
    if "GameStop Quarterly Revenue" in str(table):
        rows = table.find_all("tr")
        break

gme_revenue = []
for row in rows[1:]:
    cols = row.find_all("td")
    if len(cols) == 2:
        date = cols[0].text.strip()
        revenue = cols[1].text.strip().replace("$", "").replace(",", "")
        gme_revenue.append([date, revenue])

df_gme_revenue = pd.DataFrame(gme_revenue, columns=["Date", "Revenue"])
df_gme_revenue.head()

# Question 5: Plot Tesla Stock Graph
Historical Share Price Graph

import matplotlib.pyplot as plt

plt.figure(figsize=(14,6))
plt.plot(tesla_data['Date'], tesla_data['Close'])
plt.title("Tesla Stock Price Over Time")
plt.xlabel("Date")
plt.ylabel("Closing Price (USD)")
plt.grid(True)
plt.show()

Historical Revenue Graph

df_tesla_revenue["Revenue"] = pd.to_numeric(df_tesla_revenue["Revenue"], errors="coerce")

plt.figure(figsize=(14,6))
plt.plot(pd.to_datetime(df_tesla_revenue["Date"]), df_tesla_revenue["Revenue"])
plt.title("Tesla Revenue Over Time")
plt.xlabel("Date")
plt.ylabel("Revenue (USD)")
plt.grid(True)
plt.show()

# Question 6: Plot GameStop Stock Graph
Historical Share Price Graph

plt.figure(figsize=(14,6))
plt.plot(gme_data['Date'], gme_data['Close'])
plt.title("GameStop Stock Price Over Time")
plt.xlabel("Date")
plt.ylabel("Closing Price (USD)")
plt.grid(True)
plt.show()

Historical Revenue Graph

df_gme_revenue["Revenue"] = pd.to_numeric(df_gme_revenue["Revenue"], errors="coerce")

plt.figure(figsize=(14,6))
plt.plot(pd.to_datetime(df_gme_revenue["Date"]), df_gme_revenue["Revenue"])
plt.title("GameStop Revenue Over Time")
plt.xlabel("Date")
plt.ylabel("Revenue (USD)")
plt.grid(True)
plt.show()
