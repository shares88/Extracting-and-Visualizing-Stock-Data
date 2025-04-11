# Extracting-and-Visualizing-Stock-Data
Python Project for Data Science / Final Assignment
## Question 1: Use yfinance to Extract Stock Data
Using the Ticker function enter the ticker symbol of the stock we want to extract data on to create a ticker object. The stock is Tesla and its ticker symbol is TSLA.

```python
import yfinance as yf

tesla = yf.Ticker("TSLA")
tesla_data = tesla.history(period="max")
tesla_data.reset_index(inplace=True)
tesla_data.head()
```

## Question 2: Use Webscraping to Extract Tesla Revenue Data
Use the requests library to download the webpage https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/...

```
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
```
### Extract the table with Tesla revenue
```tables = soup.find_all('table')
for index, table in enumerate(tables):
    print(f"Table {index}")
    print(table.prettify()[:1000])
```
### Convert the revenue table to a DataFrame
```for table in tables:
    if "Tesla Quarterly Revenue" in str(table):
        tesla_revenue = pd.read_html(str(table))[0]
        break

tesla_revenue.columns = ['Date', 'Revenue']
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(r'\$', '', regex=True).str.replace(',', '')
tesla_revenue['Revenue'] = pd.to_numeric(tesla_revenue['Revenue'])
tesla_revenue.head()
```
## Question 3: Use yfinance to Extract GameStop Stock Data
Extract historical market data for GameStop (ticker symbol: GME) using the same method.
```gme = yf.Ticker("GME")
gme_data = gme.history(period="max")
gme_data.reset_index(inplace=True)
gme_data.head()
```
## Question 4: Use Webscraping to Extract GME Revenue Data
Scrape GameStop revenue data from the given URL.
```url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.htm"
html_data = requests.get(url).text
soup = BeautifulSoup(html_data, "html.parser")
```
Extract GameStop revenue table
```tables = soup.find_all('table')
for table in tables:
    if "GameStop Quarterly Revenue" in str(table):
        gme_revenue = pd.read_html(str(table))[0]
        break

gme_revenue.columns = ['Date', 'Revenue']
gme_revenue.dropna(inplace=True)
gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
gme_revenue['Revenue'] = gme_revenue['Revenue'].str.replace(r'\$', '', regex=True).str.replace(',', '')
gme_revenue['Revenue'] = pd.to_numeric(gme_revenue['Revenue'])
gme_revenue.head()
```
## Question 5: Plot Tesla Stock Graph

### Historical Share Price Graph
```import matplotlib.pyplot as plt

def make_graph(stock_data, revenue_data, stock, color='tab:red'):
    fig, ax1 = plt.subplots(figsize=(14,6))

    ax1.set_xlabel('Date')
    ax1.set_ylabel('Stock Price', color=color)
    ax1.plot(stock_data['Date'], stock_data['Close'], color=color)
    ax1.tick_params(axis='y', labelcolor=color)

    ax2 = ax1.twinx()
    ax2.set_ylabel('Revenue (in Billions)', color='tab:blue')
    ax2.plot(revenue_data['Date'], revenue_data['Revenue'] / 1e3, color='tab:blue')
    ax2.tick_params(axis='y', labelcolor='tab:blue')

    plt.title(f"{stock} Stock Price and Revenue")
    plt.show()

make_graph(tesla_data, tesla_revenue, 'Tesla')
```

### Historical Revenue Graph
```
df_tesla_revenue["Revenue"] = pd.to_numeric(df_tesla_revenue["Revenue"], errors="coerce")

plt.figure(figsize=(14,6))
plt.plot(pd.to_datetime(df_tesla_revenue["Date"]), df_tesla_revenue["Revenue"])
plt.title("Tesla Revenue Over Time")
plt.xlabel("Date")
plt.ylabel("Revenue (USD)")
plt.grid(True)
plt.show()
```
## Question 6: Plot GameStop Stock Graph
make_graph(gme_data, gme_revenue, 'GameStop', color='tab:green')

### Historical Share Price Graph
```
plt.figure(figsize=(14,6))
plt.plot(gme_data['Date'], gme_data['Close'])
plt.title("GameStop Stock Price Over Time")
plt.xlabel("Date")
plt.ylabel("Closing Price (USD)")
plt.grid(True)
plt.show()
```
### Historical Revenue Graph
```
df_gme_revenue["Revenue"] = pd.to_numeric(df_gme_revenue["Revenue"], errors="coerce")

plt.figure(figsize=(14,6))
plt.plot(pd.to_datetime(df_gme_revenue["Date"]), df_gme_revenue["Revenue"])
plt.title("GameStop Revenue Over Time")
plt.xlabel("Date")
plt.ylabel("Revenue (USD)")
plt.grid(True)
plt.show()
```

