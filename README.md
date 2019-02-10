# Pengar
Calculate your personal financial worth with an ipython script

This script calculates the worth of your funds and stocks. Can be used to get a summary of all your financial assets from multiple sources. I made it really simple to make it easy to expand. The assets dict in is where you specify all your assets and also the current value. If you map the current value to a python funciton then it will update each time you run it which is convinient since the worth of stocks and funds fluctuates.
Right now everything converts to SEK but that should be easy to change to whatever.

## Getting started
```
jupyter notebook
Open Calculate Personal Financial worth.ipynb
```

## Jupyter notebook example


```python
import re
import requests
from bs4 import BeautifulSoup

def morningstar(url):

    r  = requests.get(url)

    data = r.text

    soup = BeautifulSoup(data)
    a = soup.find('table', {"class": "alternatedtoplist"}).findAll('td')[1].contents[0]
    
    # todo: should care about currency
    a2 = map(int, re.findall(r'\d+', a))
    a3 = float('{}.{}'.format(a2[0], a2[1]))
    return a3
```


```python
def yahoo_share(share_name):
    from yahoo_finance import Share, Currency
    yahoo = Share(share_name)
    price = float(yahoo.get_price())
    
    stock_currency = yahoo.data_set['Currency']
    
    if stock_currency != 'SEK':
        currency_convert = Currency('{}SEK'.format(stock_currency))
        rate = currency_convert.get_rate()
        price_in_SEK = float(price) * float(rate)
    else:
        price_in_SEK = float(price)
    return price_in_SEK
```


```python
assets = {
    'AAPL': {
        'own': 1,
        'current_value': yahoo_share('AAPL')
    },
    'Coca-Cola': {
        'own': 3,
        'current_value': yahoo_share('KO')
    },
    'Duke Energy': {
        'own': 2,
        'current_value': yahoo_share('DUK')
    },
    'NOF': {
        'own': 170,
        'current_value': yahoo_share('NOF.OL')
    },
    'Target': {
        'own': 2,
        'current_value': yahoo_share('TGT')
    },
    'VITR': {
        'own': 10,
        'current_value': yahoo_share('VITR.ST')
    },
    
    'Carnegie Indien': {
        'own': 4.2735,
        'current_value': morningstar("http://www.morningstar.se/Funds/Quicktake/Overview.aspx?perfid=0P00000ZOO")
    },
    'JPM Global Health': {
        'own': 2.11,
        'current_value': morningstar("http://www.morningstar.se/Funds/Quicktake/Overview.aspx?perfid=0P0000LZRB")
    },
    'Nordnet Superfonden Sverige': {
        'own': 6.4099,
        'current_value': morningstar("http://www.morningstar.se/Funds/Quicktake/Overview.aspx?perfid=0P0000J24W")
    },
    'SEB Japanfond': {
        'own': 491.8263,
        'current_value': morningstar("http://www.morningstar.se/Funds/Quicktake/Overview.aspx?perfid=0P00000LU7")
    },    
}
```


```python
from ipy_table import *
def calculate_total_assets():
    total_worth = 0
    table = [
        ['Asset', 'Own', 'Price', 'Worth']
    ]
    for asset_name in assets:
        worth = assets[asset_name]['current_value']*assets[asset_name]['own']
        total_worth += worth
        table.append([asset_name, assets[asset_name]['own'], assets[asset_name]['current_value'], worth])
        #print '{}\t{}\t{}'.format(asset_name, assets[asset_name]['current_value'], worth)
    table.append(['', '', '', ''])
    table.append(['', '', '', total_worth])
    #print(total_worth)
    return table

make_table(calculate_total_assets())
apply_theme('basic')
```




<table border="1" cellpadding="3" cellspacing="0"  style="border:1px solid black;border-collapse:collapse;"><tr><td  style="background-color:LightGray;"><b>Asset</b></td><td  style="background-color:LightGray;"><b>Own</b></td><td  style="background-color:LightGray;"><b>Price</b></td><td  style="background-color:LightGray;"><b>Worth</b></td></tr><tr><td  style="background-color:Ivory;">Duke&nbspEnergy</td><td  style="background-color:Ivory;">2</td><td  style="background-color:Ivory;">563.0259</td><td  style="background-color:Ivory;">1126.0518</td></tr><tr><td  style="background-color:AliceBlue;">Target</td><td  style="background-color:AliceBlue;">2</td><td  style="background-color:AliceBlue;">641.6222</td><td  style="background-color:AliceBlue;">1283.2443</td></tr><tr><td  style="background-color:Ivory;">VITR</td><td  style="background-color:Ivory;">10</td><td  style="background-color:Ivory;">163.0000</td><td  style="background-color:Ivory;">1630.0000</td></tr><tr><td  style="background-color:AliceBlue;">SEB&nbspJapanfond</td><td  style="background-color:AliceBlue;">491.8263</td><td  style="background-color:AliceBlue;">6.5500</td><td  style="background-color:AliceBlue;">3221.4623</td></tr><tr><td  style="background-color:Ivory;">NOF</td><td  style="background-color:Ivory;">170</td><td  style="background-color:Ivory;">7.6285</td><td  style="background-color:Ivory;">1296.8484</td></tr><tr><td  style="background-color:AliceBlue;">AAPL</td><td  style="background-color:AliceBlue;">1</td><td  style="background-color:AliceBlue;">940.9305</td><td  style="background-color:AliceBlue;">940.9305</td></tr><tr><td  style="background-color:Ivory;">Nordnet&nbspSuperfonden&nbspSverige</td><td  style="background-color:Ivory;">6.4099</td><td  style="background-color:Ivory;">280.5200</td><td  style="background-color:Ivory;">1798.1051</td></tr><tr><td  style="background-color:AliceBlue;">Carnegie&nbspIndien</td><td  style="background-color:AliceBlue;">4.2735</td><td  style="background-color:AliceBlue;">368.6500</td><td  style="background-color:AliceBlue;">1575.4258</td></tr><tr><td  style="background-color:Ivory;">Coca-Cola</td><td  style="background-color:Ivory;">3</td><td  style="background-color:Ivory;">314.1378</td><td  style="background-color:Ivory;">942.4135</td></tr><tr><td  style="background-color:AliceBlue;">JPM&nbspGlobal&nbspHealth</td><td  style="background-color:AliceBlue;">2.1100</td><td  style="background-color:AliceBlue;">303.1000</td><td  style="background-color:AliceBlue;">639.5410</td></tr><tr><td  style="background-color:Ivory;"></td><td  style="background-color:Ivory;"></td><td  style="background-color:Ivory;"></td><td  style="background-color:Ivory;"></td></tr><tr><td  style="background-color:AliceBlue;"></td><td  style="background-color:AliceBlue;"></td><td  style="background-color:AliceBlue;"></td><td  style="background-color:AliceBlue;">14454.0227</td></tr></table>



## Support my creation of open source software:
[![Flattr this git repo](http://api.flattr.com/button/flattr-badge-large.png)](https://flattr.com/submit/auto?user_id=sebnil&url=https://github.com/sebnil/Pengar)

<a href='https://ko-fi.com/A0A2HYRH' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://az743702.vo.msecnd.net/cdn/kofi2.png?v=0' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>