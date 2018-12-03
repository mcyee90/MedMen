

```python
# Dependencies

import pandas as pd
pd.set_option('display.float_format', lambda x: '%.3f' % x)
import plotly
plotly.tools.set_credentials_file(username='mcyee90', api_key='tyKEvUOsCK4MehoEmOKr')
import plotly.plotly as py
import plotly.graph_objs as go
import numpy as np
import math 

```


```python
traffic = pd.read_csv("traffic.csv")
traffic = traffic[['Date', 'Visitors', 'Visits', 'Page Views']]
traffic = traffic.apply(lambda x: x.str.replace(',',''))
traffic['Date'] =  pd.to_datetime(traffic['Date'])
traffic[['Visitors', 'Visits', 'Page Views']] = traffic[['Visitors', 'Visits', 'Page Views']].apply(pd.to_numeric)
traffic = traffic.groupby(['Date'])['Visitors', 'Visits', 'Page Views'].sum()
traffic = traffic.reset_index()
traffic.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Visitors</th>
      <th>Visits</th>
      <th>Page Views</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1100599</td>
      <td>1430780</td>
      <td>1650898</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>1196844</td>
      <td>1555899</td>
      <td>1795268</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>983286</td>
      <td>1278272</td>
      <td>1474928</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>1221271</td>
      <td>1587654</td>
      <td>1831909</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>1431153</td>
      <td>1860499</td>
      <td>2146730</td>
    </tr>
  </tbody>
</table>
</div>




```python
market = pd.read_csv("market.csv")
market = market.apply(lambda x: x.str.replace(',',''))
market['Date'] =  pd.to_datetime(market['Date'])
market[['Visits to competitor sites', 'Social Media Mentions']] = market[['Visits to competitor sites', 'Social Media Mentions']].apply(pd.to_numeric)
market.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Visits to competitor sites</th>
      <th>Social Media Mentions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1216161</td>
      <td>210225</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>1322513</td>
      <td>231825</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>1086531</td>
      <td>190125</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>1349506</td>
      <td>232925</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>1581425</td>
      <td>276055</td>
    </tr>
  </tbody>
</table>
</div>




```python
sku = pd.read_csv("sku.csv")
sku = sku[['SKU', 'Product Name', 'Value', 'Beginning Inventory']]
sku[['Beginning Inventory']] = sku[['Beginning Inventory']].apply(lambda x: x.str.replace(',',''))
sku[['Value']] = sku[['Value']].apply(lambda x: x.str.replace('$',''))
sku[['Value', 'Beginning Inventory']] = sku[['Value', 'Beginning Inventory']].apply(pd.to_numeric)
sku = sku[0:10]
sku[['SKU', 'Value', 'Beginning Inventory']] = sku[['SKU', 'Value', 'Beginning Inventory']].astype(int)
sku['Price Tier'] = ""
for index, row in sku.iterrows():
    if row['Value'] < 35:
        sku.at[index, 'Price Tier'] = "L"
    elif row['Value'] < 100:
        sku.at[index, 'Price Tier'] = "M"
    else:
        sku.at[index, 'Price Tier'] = "H"
sku.head(200)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SKU</th>
      <th>Product Name</th>
      <th>Value</th>
      <th>Beginning Inventory</th>
      <th>Price Tier</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>101</td>
      <td>Pineappple Express 1G</td>
      <td>50</td>
      <td>2518</td>
      <td>M</td>
    </tr>
    <tr>
      <th>1</th>
      <td>102</td>
      <td>Fruit Punch - 100 MG</td>
      <td>200</td>
      <td>2676</td>
      <td>H</td>
    </tr>
    <tr>
      <th>2</th>
      <td>103</td>
      <td>Lip Balm - CBD</td>
      <td>75</td>
      <td>852</td>
      <td>M</td>
    </tr>
    <tr>
      <th>3</th>
      <td>104</td>
      <td>zen pen - 500mg</td>
      <td>40</td>
      <td>1168</td>
      <td>M</td>
    </tr>
    <tr>
      <th>4</th>
      <td>105</td>
      <td>Blue Diamond - 3G</td>
      <td>30</td>
      <td>1274</td>
      <td>L</td>
    </tr>
    <tr>
      <th>5</th>
      <td>106</td>
      <td>Nosh OG - 0.3G</td>
      <td>30</td>
      <td>124</td>
      <td>L</td>
    </tr>
    <tr>
      <th>6</th>
      <td>107</td>
      <td>Blue Dream PreRoll</td>
      <td>25</td>
      <td>410</td>
      <td>L</td>
    </tr>
    <tr>
      <th>7</th>
      <td>108</td>
      <td>Cannabis T-Shirt</td>
      <td>50</td>
      <td>1694</td>
      <td>M</td>
    </tr>
    <tr>
      <th>8</th>
      <td>109</td>
      <td>WiFi - 1G</td>
      <td>75</td>
      <td>552</td>
      <td>M</td>
    </tr>
    <tr>
      <th>9</th>
      <td>110</td>
      <td>Cookie Brownie Bar - 10MG</td>
      <td>50</td>
      <td>588</td>
      <td>M</td>
    </tr>
  </tbody>
</table>
</div>




```python
sales = pd.read_csv("sales.csv")
sales[['Transaction ID (Unique)']] = sales[['Transaction ID (Unique)']].apply(lambda x: x.str.replace(',',''))
sales['Date'] =  pd.to_datetime(sales['Date'])
sales[['Transaction ID (Unique)']] = sales[['Transaction ID (Unique)']].apply(pd.to_numeric)
sales.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Transaction ID (Unique)</th>
      <th>SKU</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1001</td>
      <td>102</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-01</td>
      <td>1002</td>
      <td>102</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-01</td>
      <td>1003</td>
      <td>101</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-01</td>
      <td>1005</td>
      <td>101</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-01</td>
      <td>1006</td>
      <td>101</td>
    </tr>
  </tbody>
</table>
</div>




```python
sales_data = pd.merge(sales, sku, on="SKU", how="left")
sales_data = sales_data[['Date', 'Transaction ID (Unique)', 'SKU', 'Product Name', 'Value']]
sales_data = sales_data.groupby('Date')['Value'].agg(['sum', 'count'])
sales_data = sales_data.reset_index()
sales_data.columns = ['Date', 'Revenue', 'Sales']
sales_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Revenue</th>
      <th>Sales</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1000</td>
      <td>10</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>875</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>800</td>
      <td>9</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>800</td>
      <td>11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>1800</td>
      <td>13</td>
    </tr>
  </tbody>
</table>
</div>




```python
spend = pd.read_csv("media_spend.csv")
spend = spend.apply(lambda x: x.str.replace(',',''))
spend = spend.apply(lambda x: x.str.replace('$',''))
spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']] = spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']].apply(lambda x: x.str.replace(' ',''))
spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']] = spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']].apply(lambda x: x.str.replace('-',''))
spend['Date'] =  pd.to_datetime(spend['Date'])
spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']] = spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']].apply(pd.to_numeric)
spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']] = spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']].fillna(0)
spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']] = spend[['GRP (OOH)', 'PPC Spend', 'Display Spend', 'email Spend']].replace(np.nan, 0)
spend['total'] = ""
for index, row in spend.iterrows():
    spend.at[index, 'total'] = row['GRP (OOH)'] + row['PPC Spend'] + row['Display Spend'] + row['email Spend']
    
spend.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>GRP (OOH)</th>
      <th>PPC Spend</th>
      <th>Display Spend</th>
      <th>email Spend</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>118500.000</td>
      <td>1890.000</td>
      <td>1491</td>
      <td>1275.000</td>
      <td>123156.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>82500.000</td>
      <td>2273.000</td>
      <td>1272</td>
      <td>720.000</td>
      <td>86765.000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>159000.000</td>
      <td>1710.000</td>
      <td>1426</td>
      <td>660.000</td>
      <td>162796.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>153000.000</td>
      <td>1980.000</td>
      <td>1410</td>
      <td>750.000</td>
      <td>157140.000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>123000.000</td>
      <td>2160.000</td>
      <td>1166</td>
      <td>555.000</td>
      <td>126881.000</td>
    </tr>
  </tbody>
</table>
</div>




```python
cost_revs = pd.merge(sales_data, spend, on="Date", how="left")
all_data = pd.merge(cost_revs, traffic, on="Date", how="left")
all_data.to_csv('all_data.csv')
```


```python
trace1 = go.Scatter(
        x = all_data['Date'],
        y = all_data['Revenue'],
        name = "Revenues",
        line = {'color': "#008000"},
        mode = 'lines')

trace2 = go.Scatter(
        x = all_data['Date'],    
        y = all_data['total'],
        name = "Cost",
        line = {'color': "#ff0000"},
        mode = 'lines',
        yaxis='y2')

trace3 = go.Scatter(
        x=all_data['Date'],
        y=all_data['Visits'],
        name = "Visits",
        line = {'color': "#0000ff"},
        mode = 'lines',
        yaxis='y3')

day_data = [trace1, trace2, trace3]

layout = go.Layout(
    title='Revenue, Cost and Website Visits (Daily)',
    xaxis=dict(
        rangeselector=dict(
            buttons=list([
                dict(count=1,
                     label='1m',
                     step='month',
                     stepmode='backward'),
                dict(count=6,
                     label='6m',
                     step='month',
                     stepmode='backward'),
                dict(step='all')
            ])
        ),
        domain=[0.1, 0.9],
        rangeslider=dict(
            visible = True
        ),
        type='date'
    ),
    yaxis = dict(
        title='Revenue',
        titlefont=dict(
            color='#008000'
            ),
        tickfont=dict(
            color='#008000'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='Cost',
        titlefont=dict(
            color='#ff0000'
            ),
        tickfont=dict(
            color='#ff0000'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='Visits',
        titlefont=dict(
            color='#0000ff'
            ),
        tickfont=dict(
            color='#0000ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        )
)

fig1 = go.Figure(data=day_data, layout=layout)

py.iplot(fig1)

```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/373.embed" height="525px" width="100%"></iframe>




```python
grouped_data = all_data
grouped_data['Month'] = ""
grouped_data['Week'] = ""
for d, row in grouped_data.iterrows():
    year = str(row['Date'])[0:4]
    month = str(row['Date'])[5:7]
    week = math.floor((int(d)/7)+1)
    grouped_data.at[d, "Week"] = week
    grouped_data.at[d, "Month"] = year + "-" + month
grouped_data['Month'] = pd.to_datetime(grouped_data['Month'])
    
grouped_data.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Revenue</th>
      <th>Sales</th>
      <th>GRP (OOH)</th>
      <th>PPC Spend</th>
      <th>Display Spend</th>
      <th>email Spend</th>
      <th>total</th>
      <th>Visitors</th>
      <th>Visits</th>
      <th>Page Views</th>
      <th>Month</th>
      <th>Week</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1000</td>
      <td>10</td>
      <td>118500.000</td>
      <td>1890.000</td>
      <td>1491</td>
      <td>1275.000</td>
      <td>123156.000</td>
      <td>1100599</td>
      <td>1430780</td>
      <td>1650898</td>
      <td>2008-01-01</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>875</td>
      <td>11</td>
      <td>82500.000</td>
      <td>2273.000</td>
      <td>1272</td>
      <td>720.000</td>
      <td>86765.000</td>
      <td>1196844</td>
      <td>1555899</td>
      <td>1795268</td>
      <td>2008-01-01</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>800</td>
      <td>9</td>
      <td>159000.000</td>
      <td>1710.000</td>
      <td>1426</td>
      <td>660.000</td>
      <td>162796.000</td>
      <td>983286</td>
      <td>1278272</td>
      <td>1474928</td>
      <td>2008-01-01</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>800</td>
      <td>11</td>
      <td>153000.000</td>
      <td>1980.000</td>
      <td>1410</td>
      <td>750.000</td>
      <td>157140.000</td>
      <td>1221271</td>
      <td>1587654</td>
      <td>1831909</td>
      <td>2008-01-01</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>1800</td>
      <td>13</td>
      <td>123000.000</td>
      <td>2160.000</td>
      <td>1166</td>
      <td>555.000</td>
      <td>126881.000</td>
      <td>1431153</td>
      <td>1860499</td>
      <td>2146730</td>
      <td>2008-01-01</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
grouped_data.columns
```




    Index(['Date', 'Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
           'email Spend', 'total', 'Visitors', 'Visits', 'Page Views', 'Month',
           'Week'],
          dtype='object')




```python
month_grouped = grouped_data.groupby(['Month'])['Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
       'email Spend', 'total', 'Visitors', 'Visits', 'Page Views'].sum()
month_grouped['total'] = ""
for index, row in month_grouped.iterrows():
    month_grouped.at[index, 'total'] = row['GRP (OOH)'] + row['PPC Spend'] + row['Display Spend'] + row['email Spend']
month_grouped = month_grouped.reset_index()
month_grouped.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Month</th>
      <th>Revenue</th>
      <th>Sales</th>
      <th>GRP (OOH)</th>
      <th>PPC Spend</th>
      <th>Display Spend</th>
      <th>email Spend</th>
      <th>Visitors</th>
      <th>Visits</th>
      <th>Page Views</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>41025</td>
      <td>367</td>
      <td>3958500.000</td>
      <td>64334.000</td>
      <td>39241</td>
      <td>21382.000</td>
      <td>40134319</td>
      <td>52174598</td>
      <td>60201465</td>
      <td>4083457.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-02-01</td>
      <td>36400</td>
      <td>330</td>
      <td>3954000.000</td>
      <td>56527.000</td>
      <td>35795</td>
      <td>18441.000</td>
      <td>35994492</td>
      <td>46792838</td>
      <td>53991752</td>
      <td>4064763.000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-03-01</td>
      <td>37325</td>
      <td>349</td>
      <td>1536500.000</td>
      <td>62175.000</td>
      <td>38656</td>
      <td>17652.000</td>
      <td>37134444</td>
      <td>48274776</td>
      <td>55701681</td>
      <td>1654983.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-04-01</td>
      <td>18860</td>
      <td>333</td>
      <td>1557250.000</td>
      <td>22009.000</td>
      <td>37743</td>
      <td>8262.000</td>
      <td>34946558</td>
      <td>45430536</td>
      <td>52419846</td>
      <td>1625264.000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-05-01</td>
      <td>21410</td>
      <td>335</td>
      <td>1622750.000</td>
      <td>24406.000</td>
      <td>39188</td>
      <td>7886.000</td>
      <td>34470589</td>
      <td>44811769</td>
      <td>51705887</td>
      <td>1694230.000</td>
    </tr>
  </tbody>
</table>
</div>




```python
week_grouped = grouped_data.groupby(['Week'])['Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
       'email Spend', 'total', 'Visitors', 'Visits', 'Page Views'].sum()
week_grouped['total'] = ""
for index, row in week_grouped.iterrows():
    week_grouped.at[index, 'total'] = row['GRP (OOH)'] + row['PPC Spend'] + row['Display Spend'] + row['email Spend']
week_grouped = week_grouped.reset_index()
week_grouped.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Week</th>
      <th>Revenue</th>
      <th>Sales</th>
      <th>GRP (OOH)</th>
      <th>PPC Spend</th>
      <th>Display Spend</th>
      <th>email Spend</th>
      <th>Visitors</th>
      <th>Visits</th>
      <th>Page Views</th>
      <th>total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>7600</td>
      <td>77</td>
      <td>898500.000</td>
      <td>13681.000</td>
      <td>9133</td>
      <td>5333.000</td>
      <td>8444986</td>
      <td>10978488</td>
      <td>12667485</td>
      <td>926647.000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>10525</td>
      <td>89</td>
      <td>957000.000</td>
      <td>15998.000</td>
      <td>9271</td>
      <td>4765.000</td>
      <td>9743912</td>
      <td>12667074</td>
      <td>14615858</td>
      <td>987034.000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>8625</td>
      <td>83</td>
      <td>865500.000</td>
      <td>14290.000</td>
      <td>7910</td>
      <td>4689.000</td>
      <td>9088268</td>
      <td>11814743</td>
      <td>13632401</td>
      <td>892389.000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>8975</td>
      <td>79</td>
      <td>937500.000</td>
      <td>14064.000</td>
      <td>8888</td>
      <td>4667.000</td>
      <td>8620549</td>
      <td>11206716</td>
      <td>12930822</td>
      <td>965119.000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>10650</td>
      <td>83</td>
      <td>771000.000</td>
      <td>13997.000</td>
      <td>8318</td>
      <td>4629.000</td>
      <td>9031102</td>
      <td>11740427</td>
      <td>13546650</td>
      <td>797944.000</td>
    </tr>
  </tbody>
</table>
</div>




```python
month_rev = go.Scatter(
            x = month_grouped['Month'],
            y = month_grouped['Revenue'],
            name = "Revenues",
            line = {'color': "#008000", "width":3},
            mode = 'lines',
            yaxis="y1")

month_cost = go.Scatter(
            x=month_grouped['Month'],
            y=month_grouped['total'],
            name = "cost",
            line = {'color': "#ff0000", "width":1},
            mode = 'lines',
            yaxis='y2')

month_visits = go.Scatter(
            x=month_grouped['Month'],
            y=month_grouped['Visits'],
            name = "Visits",
            line = {'color': "#0000ff", "width":3},
            mode = 'lines',
            yaxis='y3')

data = [month_rev, month_cost, month_visits]

layout = go.Layout(
    title='Revenue, Cost and Website Visits (Monthly)',
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        rangeselector=dict(
            buttons=list([
                dict(count=1,
                     label='1m',
                     step='month',
                     stepmode='backward'),
                dict(count=6,
                     label='6m',
                     step='month',
                     stepmode='backward'),
                dict(step='all')
            ])
        ),
        domain=[0.1, 0.9],
        rangeslider=dict(
            visible = True
        ),
        type='date'
    ),
    yaxis = dict(
        title='Revenue',
        titlefont=dict(
            color='#008000'
            ),
        tickfont=dict(
            color='#008000'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='Cost',
        titlefont=dict(
            color='#ff0000'
            ),
        tickfont=dict(
            color='#ff0000'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='Visits',
        titlefont=dict(
            color='#0000ff'
            ),
        tickfont=dict(
            color='#0000ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        )
)


fig = go.Figure(data=data, layout=layout)

py.iplot(fig)
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/375.embed" height="525px" width="100%"></iframe>




```python
week_rev = go.Scatter(
            x = week_grouped['Week'],
            y = week_grouped['Revenue'],
            name = "Revenues",
            line = {'color': "#008000", "width":3},
            mode = 'lines',
            yaxis="y1")

week_cost = go.Scatter(
            x=week_grouped['Week'],
            y=week_grouped['total'],
            name = "cost",
            line = {'color': "#ff0000", "width":1},
            mode = 'lines',
            yaxis='y2')

week_visits = go.Scatter(
            x=week_grouped['Week'],
            y=week_grouped['Visits'],
            name = "Visits",
            line = {'color': "#0000ff", "width":3},
            mode = 'lines',
            yaxis='y3')

data = [week_rev, week_cost, week_visits]

layout = go.Layout(
    title='Revenue, Cost and Website Visits (Weekly)',
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        domain=[0.1, 0.9],
        rangeslider=dict(
            visible = True
        )
    ),
    yaxis = dict(
        title='Revenue',
        titlefont=dict(
            color='#008000'
            ),
        tickfont=dict(
            color='#008000'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='Cost',
        titlefont=dict(
            color='#ff0000'
            ),
        tickfont=dict(
            color='#ff0000'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='Visits',
        titlefont=dict(
            color='#0000ff'
            ),
        tickfont=dict(
            color='#0000ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        )
)


fig = go.Figure(data=data, layout=layout)

py.plot(fig)
```




    'https://plot.ly/~mcyee90/377'




```python
sku_sales = sales.groupby(['SKU']).count()
sku_sales = sku_sales.reset_index()
sku_sales = pd.merge(sku_sales, sku, on="SKU", how="left")
sku_sales['Percent Sold'] = ""
sku_sales['Revenue Total'] = ""
sku_sales['Market Share'] = ""
sku_sales.head(10)
for i, r in sku_sales.iterrows():
    sku_sales.at[i, 'Percent Sold'] = r['Transaction ID (Unique)']/r['Beginning Inventory']
    sku_sales.at[i, 'Revenue Total'] = r['Transaction ID (Unique)']*r['Value']
sku_sales = sku_sales.sort_values(by=['Value'])
total_sales = sku_sales['Revenue Total'].sum()
for i, r in sku_sales.iterrows():
    sku_sales.at[i, "Market Share"] = r['Revenue Total']/total_sales
sku_sales
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SKU</th>
      <th>Date</th>
      <th>Transaction ID (Unique)</th>
      <th>Product Name</th>
      <th>Value</th>
      <th>Beginning Inventory</th>
      <th>Price Tier</th>
      <th>Percent Sold</th>
      <th>Revenue Total</th>
      <th>Market Share</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>6</th>
      <td>107</td>
      <td>205</td>
      <td>205</td>
      <td>Blue Dream PreRoll</td>
      <td>25</td>
      <td>410</td>
      <td>L</td>
      <td>0.500</td>
      <td>5125</td>
      <td>0.008</td>
    </tr>
    <tr>
      <th>4</th>
      <td>105</td>
      <td>637</td>
      <td>637</td>
      <td>Blue Diamond - 3G</td>
      <td>30</td>
      <td>1274</td>
      <td>L</td>
      <td>0.500</td>
      <td>19110</td>
      <td>0.030</td>
    </tr>
    <tr>
      <th>5</th>
      <td>106</td>
      <td>186</td>
      <td>186</td>
      <td>Nosh OG - 0.3G</td>
      <td>30</td>
      <td>124</td>
      <td>L</td>
      <td>1.500</td>
      <td>5580</td>
      <td>0.009</td>
    </tr>
    <tr>
      <th>3</th>
      <td>104</td>
      <td>584</td>
      <td>584</td>
      <td>zen pen - 500mg</td>
      <td>40</td>
      <td>1168</td>
      <td>M</td>
      <td>0.500</td>
      <td>23360</td>
      <td>0.037</td>
    </tr>
    <tr>
      <th>0</th>
      <td>101</td>
      <td>1259</td>
      <td>1259</td>
      <td>Pineappple Express 1G</td>
      <td>50</td>
      <td>2518</td>
      <td>M</td>
      <td>0.500</td>
      <td>62950</td>
      <td>0.100</td>
    </tr>
    <tr>
      <th>7</th>
      <td>108</td>
      <td>847</td>
      <td>847</td>
      <td>Cannabis T-Shirt</td>
      <td>50</td>
      <td>1694</td>
      <td>M</td>
      <td>0.500</td>
      <td>42350</td>
      <td>0.067</td>
    </tr>
    <tr>
      <th>9</th>
      <td>110</td>
      <td>881</td>
      <td>881</td>
      <td>Cookie Brownie Bar - 10MG</td>
      <td>50</td>
      <td>588</td>
      <td>M</td>
      <td>1.498</td>
      <td>44050</td>
      <td>0.070</td>
    </tr>
    <tr>
      <th>2</th>
      <td>103</td>
      <td>1278</td>
      <td>1278</td>
      <td>Lip Balm - CBD</td>
      <td>75</td>
      <td>852</td>
      <td>M</td>
      <td>1.500</td>
      <td>95850</td>
      <td>0.153</td>
    </tr>
    <tr>
      <th>8</th>
      <td>109</td>
      <td>828</td>
      <td>828</td>
      <td>WiFi - 1G</td>
      <td>75</td>
      <td>552</td>
      <td>M</td>
      <td>1.500</td>
      <td>62100</td>
      <td>0.099</td>
    </tr>
    <tr>
      <th>1</th>
      <td>102</td>
      <td>1338</td>
      <td>1338</td>
      <td>Fruit Punch - 100 MG</td>
      <td>200</td>
      <td>2676</td>
      <td>H</td>
      <td>0.500</td>
      <td>267600</td>
      <td>0.426</td>
    </tr>
  </tbody>
</table>
</div>




```python
trace1 = go.Bar(
    x = sku_sales['Product Name'],
    y = sku_sales['Value'],
    name='Value',
    text= (sku_sales['Value']).map('${:,.0f}'.format),
    textposition = 'outside',
    textfont=dict(
        color='#000000',
    ),
    marker=dict(
        color='#0000ff',
        line=dict(
            color='#0000ff',
            width=1),
        ),
    opacity=.5
)

trace2 = go.Bar(
    x = sku_sales['Product Name'],
    y = 100*sku_sales['Percent Sold'],
    name='Percent of Inventory Sold',
    text= (100*sku_sales['Percent Sold']).map('{:,.1f}%'.format),
    textposition = 'outside',
    textfont=dict(
        color='#000000',
    ),
    marker=dict(
        color='#008000',
        line=dict(
            color='#008000',
            width=1),
        ),
    opacity=.5,
    )

data = [trace1, trace2]

layout = go.Layout(
    barmode='group',
    title='Sales Analysis'
)

fig = go.Figure(data=data, layout=layout)
py.iplot(fig, filename='grouped-bar')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/379.embed" height="525px" width="100%"></iframe>




```python
labels = sku_sales['Product Name']
values = sku_sales['Revenue Total']

trace = go.Pie(labels=labels, values=values)

layout = go.Layout(
    title='Market Share by Product'
)

fig = go.Figure(data=[trace], layout=layout)

py.iplot(fig, filename='Market Share')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/381.embed" height="525px" width="100%"></iframe>




```python
sales_lmh = pd.merge(sales, sku, on="SKU", how="left")
sales_lmh = sales_lmh.groupby(['Date', 'Price Tier'])['Price Tier'].agg(['count'])
sales_lmh = sales_lmh.reset_index()
sales_lmh.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Price Tier</th>
      <th>count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>H</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-01</td>
      <td>M</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-02</td>
      <td>H</td>
      <td>2</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-02</td>
      <td>M</td>
      <td>9</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-03</td>
      <td>H</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>




```python
sales_l = sales_lmh[sales_lmh['Price Tier']=="L"]
sales_l = pd.merge(grouped_data, sales_l, on='Date', how='left')
sales_l_week = sales_l.groupby(['Week'])['Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
       'email Spend', 'total', 'Visitors', 'Visits', 'Page Views','count'].sum()
sales_l_week['count'] = sales_l_week['count'].fillna(0)
sales_l_week['count'] = sales_l_week['count'].replace(np.nan, 0)
sales_l_week = sales_l_week.reset_index()

week_sales = go.Scatter(
            x = sales_l_week['Week'],
            y = sales_l_week['count'],
            name = "Sales",
            line = {'color': "#ffd700", "width":4.5},
            mode = 'lines',
            yaxis="y1")

week_grp = go.Scatter(
            x=sales_l_week['Week'],
            y=sales_l_week['GRP (OOH)'],
            name = "GRP (OOH)",
            line = {'color': "#00ffd7", "width":1},
            mode = 'lines',
            yaxis='y2')

week_ppc = go.Scatter(
            x=sales_l_week['Week'],
            y=sales_l_week['PPC Spend'],
            name = "PPC Spend",
            line = {'color': "#d700ff", "width":1},
            mode = 'lines',
            yaxis='y3')

week_email = go.Scatter(
            x=sales_l_week['Week'],
            y=sales_l_week['email Spend'],
            name = "Email",
            line = {'color': "#ff5700", "width":1},
            mode = 'lines',
            yaxis='y4')

data = [week_sales, week_grp, week_ppc, week_email]

layout = go.Layout(
    title='Weekly Marketing Campaigns and Low Cost Sales (less than $35 per product)',
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        domain=[0, 0.9],
        rangeslider=dict(
            visible = True
        )
    ),
    yaxis = dict(
        title='Sales',
        titlefont=dict(
            color='#ffd700'
            ),
        tickfont=dict(
            color='#ffd700'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='GRP',
        titlefont=dict(
            color='#00ffd7'
            ),
        tickfont=dict(
            color='#00ffd7'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='PPC',
        titlefont=dict(
            color='#d700ff'
            ),
        tickfont=dict(
            color='#d700ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        ),
    yaxis4=dict(
        title='Email',
        titlefont=dict(
            color='#ff5700'
            ),
        tickfont=dict(
            color='#ff5700'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=1
        )
)


fig = go.Figure(data=data, layout=layout)

py.iplot(fig)
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/383.embed" height="525px" width="100%"></iframe>




```python
sales_m = sales_lmh[sales_lmh['Price Tier']=="M"]
sales_m = pd.merge(grouped_data, sales_m, on='Date', how='left')
sales_m_week = sales_m.groupby(['Week'])['Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
       'email Spend', 'total', 'Visitors', 'Visits', 'Page Views','count'].sum()
sales_m_week['count'] = sales_m_week['count'].fillna(0)
sales_m_week['count'] = sales_m_week['count'].replace(np.nan, 0)
sales_m_week = sales_m_week.reset_index()

week_sales = go.Scatter(
            x = sales_m_week['Week'],
            y = sales_m_week['count'],
            name = "Sales",
            line = {'color': "#ffd700", "width":4.5},
            mode = 'lines',
            yaxis="y1")

week_grp = go.Scatter(
            x=sales_m_week['Week'],
            y=sales_m_week['GRP (OOH)'],
            name = "GRP (OOH)",
            line = {'color': "#00ffd7", "width":1},
            mode = 'lines',
            yaxis='y2')

week_ppc = go.Scatter(
            x=sales_m_week['Week'],
            y=sales_m_week['PPC Spend'],
            name = "PPC Spend",
            line = {'color': "#d700ff", "width":1},
            mode = 'lines',
            yaxis='y3')

week_email = go.Scatter(
            x=sales_m_week['Week'],
            y=sales_m_week['email Spend'],
            name = "Email",
            line = {'color': "#ff5700", "width":1},
            mode = 'lines',
            yaxis='y4')

data = [week_sales, week_grp, week_ppc, week_email]

layout = go.Layout(
    title="Weekly Marketing Campaigns and Middle Cost Sales (between $35 and 150 per product)",
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        domain=[0, 0.9],
        rangeslider=dict(
            visible = True
        )
    ),
    yaxis = dict(
        title='Sales',
        titlefont=dict(
            color='#ffd700'
            ),
        tickfont=dict(
            color='#ffd700'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='GRP',
        titlefont=dict(
            color='#00ffd7'
            ),
        tickfont=dict(
            color='#00ffd7'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='PPC',
        titlefont=dict(
            color='#d700ff'
            ),
        tickfont=dict(
            color='#d700ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        ),
    yaxis4=dict(
        title='Email',
        titlefont=dict(
            color='#ff5700'
            ),
        tickfont=dict(
            color='#ff5700'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=1
        )
)


fig = go.Figure(data=data, layout=layout)

py.iplot(fig)
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/385.embed" height="525px" width="100%"></iframe>




```python
sales_h = sales_lmh[sales_lmh['Price Tier']=="H"]
sales_h = pd.merge(grouped_data, sales_h, on='Date', how='left')
sales_h_week = sales_h.groupby(['Week'])['Revenue', 'Sales', 'GRP (OOH)', 'PPC Spend', 'Display Spend',
       'email Spend', 'total', 'Visitors', 'Visits', 'Page Views','count'].sum()
sales_h_week['count'] = sales_h_week['count'].fillna(0)
sales_h_week['count'] = sales_h_week['count'].replace(np.nan, 0)
sales_h_week = sales_h_week.reset_index()

week_sales = go.Scatter(
            x = sales_h_week['Week'],
            y = sales_h_week['count'],
            name = "Sales",
            line = {'color': "#ffd700", "width":4.5},
            mode = 'lines',
            yaxis="y1")

week_grp = go.Scatter(
            x=sales_h_week['Week'],
            y=sales_h_week['GRP (OOH)'],
            name = "GRP (OOH)",
            line = {'color': "#00ffd7", "width":1},
            mode = 'lines',
            yaxis='y2')

week_ppc = go.Scatter(
            x=sales_h_week['Week'],
            y=sales_h_week['PPC Spend'],
            name = "PPC Spend",
            line = {'color': "#d700ff", "width":1},
            mode = 'lines',
            yaxis='y3')

week_email = go.Scatter(
            x=sales_h_week['Week'],
            y=sales_h_week['email Spend'],
            name = "Email",
            line = {'color': "#ff5700", "width":1},
            mode = 'lines',
            yaxis='y4')

data = [week_sales, week_grp, week_ppc, week_email]

layout = go.Layout(
    title='Weekly Marketing Campaigns and Low Cost Sales (greater than $150 per product)',
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        domain=[0, 0.9],
        rangeslider=dict(
            visible = True
        )
    ),
    yaxis = dict(
        title='Sales',
        titlefont=dict(
            color='#ffd700'
            ),
        tickfont=dict(
            color='#ffd700'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='GRP',
        titlefont=dict(
            color='#00ffd7'
            ),
        tickfont=dict(
            color='#00ffd7'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='PPC',
        titlefont=dict(
            color='#d700ff'
            ),
        tickfont=dict(
            color='#d700ff'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=.95
        ),
    yaxis4=dict(
        title='Email',
        titlefont=dict(
            color='#ff5700'
            ),
        tickfont=dict(
            color='#ff5700'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=1
        )
)


fig = go.Figure(data=data, layout=layout)

py.iplot(fig)
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/387.embed" height="525px" width="100%"></iframe>




```python
competition = pd.read_csv('market.csv')
competition['Date'] =  pd.to_datetime(competition['Date'])
competition[['Visits to competitor sites', 'Social Media Mentions']] = competition[['Visits to competitor sites', 'Social Media Mentions']].apply(lambda x: x.str.replace(',',''))
competition[['Visits to competitor sites', 'Social Media Mentions']] = competition[['Visits to competitor sites', 'Social Media Mentions']].apply(pd.to_numeric)
competition[['Visits to competitor sites', 'Social Media Mentions']] = competition[['Visits to competitor sites', 'Social Media Mentions']].fillna(0)
competition[['Visits to competitor sites', 'Social Media Mentions']] = competition[['Visits to competitor sites', 'Social Media Mentions']].replace(np.nan, 0)
complete_merge = pd.merge(grouped_data, competition, on="Date", how="left")
complete_merge
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Date</th>
      <th>Revenue</th>
      <th>Sales</th>
      <th>GRP (OOH)</th>
      <th>PPC Spend</th>
      <th>Display Spend</th>
      <th>email Spend</th>
      <th>total</th>
      <th>Visitors</th>
      <th>Visits</th>
      <th>Page Views</th>
      <th>Month</th>
      <th>Week</th>
      <th>Visits to competitor sites</th>
      <th>Social Media Mentions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2008-01-01</td>
      <td>1000</td>
      <td>10</td>
      <td>118500.000</td>
      <td>1890.000</td>
      <td>1491</td>
      <td>1275.000</td>
      <td>123156.000</td>
      <td>1100599</td>
      <td>1430780</td>
      <td>1650898</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1216161</td>
      <td>210225</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2008-01-02</td>
      <td>875</td>
      <td>11</td>
      <td>82500.000</td>
      <td>2273.000</td>
      <td>1272</td>
      <td>720.000</td>
      <td>86765.000</td>
      <td>1196844</td>
      <td>1555899</td>
      <td>1795268</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1322513</td>
      <td>231825</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2008-01-03</td>
      <td>800</td>
      <td>9</td>
      <td>159000.000</td>
      <td>1710.000</td>
      <td>1426</td>
      <td>660.000</td>
      <td>162796.000</td>
      <td>983286</td>
      <td>1278272</td>
      <td>1474928</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1086531</td>
      <td>190125</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2008-01-04</td>
      <td>800</td>
      <td>11</td>
      <td>153000.000</td>
      <td>1980.000</td>
      <td>1410</td>
      <td>750.000</td>
      <td>157140.000</td>
      <td>1221271</td>
      <td>1587654</td>
      <td>1831909</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1349506</td>
      <td>232925</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2008-01-05</td>
      <td>1800</td>
      <td>13</td>
      <td>123000.000</td>
      <td>2160.000</td>
      <td>1166</td>
      <td>555.000</td>
      <td>126881.000</td>
      <td>1431153</td>
      <td>1860499</td>
      <td>2146730</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1581425</td>
      <td>276055</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2008-01-06</td>
      <td>1275</td>
      <td>11</td>
      <td>138000.000</td>
      <td>1733.000</td>
      <td>1085</td>
      <td>713.000</td>
      <td>141531.000</td>
      <td>1200314</td>
      <td>1560409</td>
      <td>1800473</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1326349</td>
      <td>234245</td>
    </tr>
    <tr>
      <th>6</th>
      <td>2008-01-07</td>
      <td>1050</td>
      <td>12</td>
      <td>124500.000</td>
      <td>1935.000</td>
      <td>1283</td>
      <td>660.000</td>
      <td>128378.000</td>
      <td>1311519</td>
      <td>1704975</td>
      <td>1967279</td>
      <td>2008-01-01</td>
      <td>1</td>
      <td>1449228</td>
      <td>256230</td>
    </tr>
    <tr>
      <th>7</th>
      <td>2008-01-08</td>
      <td>2550</td>
      <td>20</td>
      <td>148500.000</td>
      <td>3825.000</td>
      <td>1219</td>
      <td>728.000</td>
      <td>154272.000</td>
      <td>2172624</td>
      <td>2824404</td>
      <td>3258932</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>2400742</td>
      <td>428150</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2008-01-09</td>
      <td>1700</td>
      <td>12</td>
      <td>135000.000</td>
      <td>2160.000</td>
      <td>1268</td>
      <td>773.000</td>
      <td>139201.000</td>
      <td>1310882</td>
      <td>1704148</td>
      <td>1966323</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1448524</td>
      <td>257550</td>
    </tr>
    <tr>
      <th>9</th>
      <td>2008-01-10</td>
      <td>900</td>
      <td>12</td>
      <td>133500.000</td>
      <td>1980.000</td>
      <td>1321</td>
      <td>480.000</td>
      <td>137281.000</td>
      <td>1325506</td>
      <td>1723158</td>
      <td>1988260</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1464685</td>
      <td>258270</td>
    </tr>
    <tr>
      <th>10</th>
      <td>2008-01-11</td>
      <td>1500</td>
      <td>13</td>
      <td>123000.000</td>
      <td>2250.000</td>
      <td>1449</td>
      <td>735.000</td>
      <td>127434.000</td>
      <td>1431081</td>
      <td>1860402</td>
      <td>2146616</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1581342</td>
      <td>280605</td>
    </tr>
    <tr>
      <th>11</th>
      <td>2008-01-12</td>
      <td>950</td>
      <td>9</td>
      <td>108000.000</td>
      <td>1665.000</td>
      <td>1434</td>
      <td>623.000</td>
      <td>111722.000</td>
      <td>979953</td>
      <td>1273939</td>
      <td>1469929</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1082847</td>
      <td>194760</td>
    </tr>
    <tr>
      <th>12</th>
      <td>2008-01-13</td>
      <td>1425</td>
      <td>12</td>
      <td>138000.000</td>
      <td>2138.000</td>
      <td>1246</td>
      <td>713.000</td>
      <td>142097.000</td>
      <td>1320063</td>
      <td>1716080</td>
      <td>1980094</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1458670</td>
      <td>260310</td>
    </tr>
    <tr>
      <th>13</th>
      <td>2008-01-14</td>
      <td>1500</td>
      <td>11</td>
      <td>171000.000</td>
      <td>1980.000</td>
      <td>1334</td>
      <td>713.000</td>
      <td>175027.000</td>
      <td>1203803</td>
      <td>1564943</td>
      <td>1805704</td>
      <td>2008-01-01</td>
      <td>2</td>
      <td>1330201</td>
      <td>239250</td>
    </tr>
    <tr>
      <th>14</th>
      <td>2008-01-15</td>
      <td>1375</td>
      <td>13</td>
      <td>118500.000</td>
      <td>2183.000</td>
      <td>1018</td>
      <td>615.000</td>
      <td>122316.000</td>
      <td>1409368</td>
      <td>1832177</td>
      <td>2114053</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1557353</td>
      <td>283530</td>
    </tr>
    <tr>
      <th>15</th>
      <td>2008-01-16</td>
      <td>925</td>
      <td>13</td>
      <td>103500.000</td>
      <td>2318.000</td>
      <td>1020</td>
      <td>540.000</td>
      <td>107378.000</td>
      <td>1420563</td>
      <td>1846734</td>
      <td>2130846</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1569723</td>
      <td>284375</td>
    </tr>
    <tr>
      <th>16</th>
      <td>2008-01-17</td>
      <td>900</td>
      <td>8</td>
      <td>111000.000</td>
      <td>1440.000</td>
      <td>1219</td>
      <td>675.000</td>
      <td>114334.000</td>
      <td>869120</td>
      <td>1129856</td>
      <td>1303680</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>960379</td>
      <td>175420</td>
    </tr>
    <tr>
      <th>17</th>
      <td>2008-01-18</td>
      <td>950</td>
      <td>13</td>
      <td>129000.000</td>
      <td>2205.000</td>
      <td>1118</td>
      <td>690.000</td>
      <td>133013.000</td>
      <td>1432677</td>
      <td>1862481</td>
      <td>2149018</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1583109</td>
      <td>285740</td>
    </tr>
    <tr>
      <th>18</th>
      <td>2008-01-19</td>
      <td>1625</td>
      <td>12</td>
      <td>153000.000</td>
      <td>1868.000</td>
      <td>1422</td>
      <td>623.000</td>
      <td>156913.000</td>
      <td>1334394</td>
      <td>1734710</td>
      <td>2001591</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1474503</td>
      <td>264510</td>
    </tr>
    <tr>
      <th>19</th>
      <td>2008-01-20</td>
      <td>1325</td>
      <td>11</td>
      <td>121500.000</td>
      <td>2138.000</td>
      <td>1095</td>
      <td>758.000</td>
      <td>125491.000</td>
      <td>1208101</td>
      <td>1570530</td>
      <td>1812149</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1334951</td>
      <td>243100</td>
    </tr>
    <tr>
      <th>20</th>
      <td>2008-01-21</td>
      <td>1525</td>
      <td>13</td>
      <td>129000.000</td>
      <td>2138.000</td>
      <td>1018</td>
      <td>788.000</td>
      <td>132944.000</td>
      <td>1414045</td>
      <td>1838255</td>
      <td>2121064</td>
      <td>2008-01-01</td>
      <td>3</td>
      <td>1562518</td>
      <td>288080</td>
    </tr>
    <tr>
      <th>21</th>
      <td>2008-01-22</td>
      <td>1150</td>
      <td>10</td>
      <td>111000.000</td>
      <td>1845.000</td>
      <td>1437</td>
      <td>698.000</td>
      <td>114980.000</td>
      <td>1092057</td>
      <td>1419673</td>
      <td>1638083</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1206724</td>
      <td>222175</td>
    </tr>
    <tr>
      <th>22</th>
      <td>2008-01-23</td>
      <td>1100</td>
      <td>9</td>
      <td>142500.000</td>
      <td>1620.000</td>
      <td>1037</td>
      <td>638.000</td>
      <td>145795.000</td>
      <td>980926</td>
      <td>1275203</td>
      <td>1471387</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1083923</td>
      <td>200385</td>
    </tr>
    <tr>
      <th>23</th>
      <td>2008-01-24</td>
      <td>1450</td>
      <td>12</td>
      <td>115500.000</td>
      <td>2025.000</td>
      <td>1498</td>
      <td>765.000</td>
      <td>119788.000</td>
      <td>1305519</td>
      <td>1697174</td>
      <td>1958279</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1442598</td>
      <td>267810</td>
    </tr>
    <tr>
      <th>24</th>
      <td>2008-01-25</td>
      <td>1300</td>
      <td>12</td>
      <td>148500.000</td>
      <td>2070.000</td>
      <td>1022</td>
      <td>593.000</td>
      <td>152185.000</td>
      <td>1314812</td>
      <td>1709257</td>
      <td>1972220</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1452869</td>
      <td>268530</td>
    </tr>
    <tr>
      <th>25</th>
      <td>2008-01-26</td>
      <td>1425</td>
      <td>10</td>
      <td>132000.000</td>
      <td>1868.000</td>
      <td>1252</td>
      <td>413.000</td>
      <td>135533.000</td>
      <td>1086925</td>
      <td>1413001</td>
      <td>1630388</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1201052</td>
      <td>224325</td>
    </tr>
    <tr>
      <th>26</th>
      <td>2008-01-27</td>
      <td>975</td>
      <td>12</td>
      <td>139500.000</td>
      <td>2273.000</td>
      <td>1491</td>
      <td>795.000</td>
      <td>144059.000</td>
      <td>1322941</td>
      <td>1719825</td>
      <td>1984411</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1461849</td>
      <td>269850</td>
    </tr>
    <tr>
      <th>27</th>
      <td>2008-01-28</td>
      <td>1575</td>
      <td>14</td>
      <td>148500.000</td>
      <td>2363.000</td>
      <td>1151</td>
      <td>765.000</td>
      <td>152779.000</td>
      <td>1517369</td>
      <td>1972583</td>
      <td>2276054</td>
      <td>2008-01-01</td>
      <td>4</td>
      <td>1676693</td>
      <td>315735</td>
    </tr>
    <tr>
      <th>28</th>
      <td>2008-01-29</td>
      <td>1575</td>
      <td>12</td>
      <td>109500.000</td>
      <td>2093.000</td>
      <td>1370</td>
      <td>615.000</td>
      <td>113578.000</td>
      <td>1298300</td>
      <td>1687788</td>
      <td>1947448</td>
      <td>2008-01-01</td>
      <td>5</td>
      <td>1434620</td>
      <td>271410</td>
    </tr>
    <tr>
      <th>29</th>
      <td>2008-01-30</td>
      <td>1575</td>
      <td>13</td>
      <td>87000.000</td>
      <td>1913.000</td>
      <td>1317</td>
      <td>690.000</td>
      <td>90920.000</td>
      <td>1422015</td>
      <td>1848617</td>
      <td>2133021</td>
      <td>2008-01-01</td>
      <td>5</td>
      <td>1571325</td>
      <td>294840</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>697</th>
      <td>2009-12-02</td>
      <td>790</td>
      <td>10</td>
      <td>0.000</td>
      <td>1245.000</td>
      <td>1435</td>
      <td>453.000</td>
      <td>3133.000</td>
      <td>985523</td>
      <td>1281181</td>
      <td>1478287</td>
      <td>2009-12-01</td>
      <td>100</td>
      <td>1089003</td>
      <td>595575</td>
    </tr>
    <tr>
      <th>698</th>
      <td>2009-12-03</td>
      <td>710</td>
      <td>10</td>
      <td>0.000</td>
      <td>1395.000</td>
      <td>1093</td>
      <td>333.000</td>
      <td>2821.000</td>
      <td>1001592</td>
      <td>1302069</td>
      <td>1502392</td>
      <td>2009-12-01</td>
      <td>100</td>
      <td>1106759</td>
      <td>596075</td>
    </tr>
    <tr>
      <th>699</th>
      <td>2009-12-04</td>
      <td>960</td>
      <td>13</td>
      <td>0.000</td>
      <td>1350.000</td>
      <td>1072</td>
      <td>430.000</td>
      <td>2852.000</td>
      <td>1298237</td>
      <td>1687709</td>
      <td>1947359</td>
      <td>2009-12-01</td>
      <td>100</td>
      <td>1434550</td>
      <td>775645</td>
    </tr>
    <tr>
      <th>700</th>
      <td>2009-12-05</td>
      <td>740</td>
      <td>9</td>
      <td>0.000</td>
      <td>870.000</td>
      <td>1421</td>
      <td>423.000</td>
      <td>2714.000</td>
      <td>901734</td>
      <td>1172254</td>
      <td>1352603</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>996415</td>
      <td>537480</td>
    </tr>
    <tr>
      <th>701</th>
      <td>2009-12-06</td>
      <td>845</td>
      <td>10</td>
      <td>0.000</td>
      <td>1478.000</td>
      <td>1006</td>
      <td>325.000</td>
      <td>2809.000</td>
      <td>991709</td>
      <td>1289223</td>
      <td>1487567</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1095838</td>
      <td>597675</td>
    </tr>
    <tr>
      <th>702</th>
      <td>2009-12-07</td>
      <td>1165</td>
      <td>12</td>
      <td>0.000</td>
      <td>1568.000</td>
      <td>1196</td>
      <td>675.000</td>
      <td>3439.000</td>
      <td>1205408</td>
      <td>1567031</td>
      <td>1808114</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1331976</td>
      <td>717870</td>
    </tr>
    <tr>
      <th>703</th>
      <td>2009-12-08</td>
      <td>1085</td>
      <td>12</td>
      <td>0.000</td>
      <td>1718.000</td>
      <td>1141</td>
      <td>598.000</td>
      <td>3457.000</td>
      <td>1190353</td>
      <td>1547459</td>
      <td>1785533</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1315340</td>
      <td>718590</td>
    </tr>
    <tr>
      <th>704</th>
      <td>2009-12-09</td>
      <td>945</td>
      <td>11</td>
      <td>0.000</td>
      <td>1358.000</td>
      <td>1048</td>
      <td>343.000</td>
      <td>2749.000</td>
      <td>1106315</td>
      <td>1438210</td>
      <td>1659476</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1222477</td>
      <td>659340</td>
    </tr>
    <tr>
      <th>705</th>
      <td>2009-12-10</td>
      <td>725</td>
      <td>10</td>
      <td>0.000</td>
      <td>998.000</td>
      <td>1227</td>
      <td>410.000</td>
      <td>2635.000</td>
      <td>999061</td>
      <td>1298781</td>
      <td>1498595</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1103963</td>
      <td>599925</td>
    </tr>
    <tr>
      <th>706</th>
      <td>2009-12-11</td>
      <td>660</td>
      <td>11</td>
      <td>0.000</td>
      <td>1290.000</td>
      <td>1385</td>
      <td>615.000</td>
      <td>3290.000</td>
      <td>1088679</td>
      <td>1415284</td>
      <td>1633021</td>
      <td>2009-12-01</td>
      <td>101</td>
      <td>1202991</td>
      <td>660495</td>
    </tr>
    <tr>
      <th>707</th>
      <td>2009-12-12</td>
      <td>985</td>
      <td>10</td>
      <td>0.000</td>
      <td>1268.000</td>
      <td>1359</td>
      <td>595.000</td>
      <td>3222.000</td>
      <td>1003948</td>
      <td>1305134</td>
      <td>1505926</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1109363</td>
      <td>600975</td>
    </tr>
    <tr>
      <th>708</th>
      <td>2009-12-13</td>
      <td>630</td>
      <td>10</td>
      <td>0.000</td>
      <td>975.000</td>
      <td>1044</td>
      <td>545.000</td>
      <td>2564.000</td>
      <td>1016933</td>
      <td>1322013</td>
      <td>1525401</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1123709</td>
      <td>601475</td>
    </tr>
    <tr>
      <th>709</th>
      <td>2009-12-14</td>
      <td>1530</td>
      <td>12</td>
      <td>0.000</td>
      <td>2025.000</td>
      <td>1495</td>
      <td>628.000</td>
      <td>4148.000</td>
      <td>1194376</td>
      <td>1552689</td>
      <td>1791566</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1319784</td>
      <td>722430</td>
    </tr>
    <tr>
      <th>710</th>
      <td>2009-12-15</td>
      <td>1395</td>
      <td>12</td>
      <td>0.000</td>
      <td>1793.000</td>
      <td>1375</td>
      <td>403.000</td>
      <td>3571.000</td>
      <td>1198113</td>
      <td>1557549</td>
      <td>1797173</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1323915</td>
      <td>723150</td>
    </tr>
    <tr>
      <th>711</th>
      <td>2009-12-16</td>
      <td>1005</td>
      <td>9</td>
      <td>0.000</td>
      <td>1028.000</td>
      <td>1185</td>
      <td>335.000</td>
      <td>2548.000</td>
      <td>907988</td>
      <td>1180384</td>
      <td>1361983</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1003327</td>
      <td>542835</td>
    </tr>
    <tr>
      <th>712</th>
      <td>2009-12-17</td>
      <td>700</td>
      <td>12</td>
      <td>0.000</td>
      <td>1230.000</td>
      <td>1218</td>
      <td>503.000</td>
      <td>2951.000</td>
      <td>1199122</td>
      <td>1558859</td>
      <td>1798685</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1325030</td>
      <td>724410</td>
    </tr>
    <tr>
      <th>713</th>
      <td>2009-12-18</td>
      <td>1150</td>
      <td>10</td>
      <td>0.000</td>
      <td>1845.000</td>
      <td>1020</td>
      <td>528.000</td>
      <td>3393.000</td>
      <td>1011821</td>
      <td>1315368</td>
      <td>1517735</td>
      <td>2009-12-01</td>
      <td>102</td>
      <td>1118063</td>
      <td>604225</td>
    </tr>
    <tr>
      <th>714</th>
      <td>2009-12-19</td>
      <td>1210</td>
      <td>12</td>
      <td>0.000</td>
      <td>1785.000</td>
      <td>1171</td>
      <td>465.000</td>
      <td>3421.000</td>
      <td>1190905</td>
      <td>1548177</td>
      <td>1786360</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1315952</td>
      <td>725730</td>
    </tr>
    <tr>
      <th>715</th>
      <td>2009-12-20</td>
      <td>1030</td>
      <td>12</td>
      <td>0.000</td>
      <td>1635.000</td>
      <td>1208</td>
      <td>663.000</td>
      <td>3506.000</td>
      <td>1211240</td>
      <td>1574614</td>
      <td>1816864</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1338420</td>
      <td>726450</td>
    </tr>
    <tr>
      <th>716</th>
      <td>2009-12-21</td>
      <td>1175</td>
      <td>12</td>
      <td>0.000</td>
      <td>1883.000</td>
      <td>1449</td>
      <td>435.000</td>
      <td>3767.000</td>
      <td>1202458</td>
      <td>1563196</td>
      <td>1803690</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1328716</td>
      <td>727170</td>
    </tr>
    <tr>
      <th>717</th>
      <td>2009-12-22</td>
      <td>1125</td>
      <td>10</td>
      <td>0.000</td>
      <td>1208.000</td>
      <td>1284</td>
      <td>278.000</td>
      <td>2770.000</td>
      <td>997647</td>
      <td>1296942</td>
      <td>1496473</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1102400</td>
      <td>606525</td>
    </tr>
    <tr>
      <th>718</th>
      <td>2009-12-23</td>
      <td>730</td>
      <td>9</td>
      <td>0.000</td>
      <td>1005.000</td>
      <td>1151</td>
      <td>590.000</td>
      <td>2746.000</td>
      <td>902261</td>
      <td>1172939</td>
      <td>1353394</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>996998</td>
      <td>546300</td>
    </tr>
    <tr>
      <th>719</th>
      <td>2009-12-24</td>
      <td>1045</td>
      <td>11</td>
      <td>0.000</td>
      <td>1508.000</td>
      <td>1455</td>
      <td>370.000</td>
      <td>3333.000</td>
      <td>1091820</td>
      <td>1419367</td>
      <td>1637731</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1206461</td>
      <td>668250</td>
    </tr>
    <tr>
      <th>720</th>
      <td>2009-12-25</td>
      <td>915</td>
      <td>12</td>
      <td>0.000</td>
      <td>1583.000</td>
      <td>1003</td>
      <td>0.000</td>
      <td>2586.000</td>
      <td>1198648</td>
      <td>1558242</td>
      <td>1797976</td>
      <td>2009-12-01</td>
      <td>103</td>
      <td>1324505</td>
      <td>729690</td>
    </tr>
    <tr>
      <th>721</th>
      <td>2009-12-26</td>
      <td>1410</td>
      <td>10</td>
      <td>0.000</td>
      <td>1395.000</td>
      <td>1272</td>
      <td>0.000</td>
      <td>2667.000</td>
      <td>996591</td>
      <td>1295569</td>
      <td>1494890</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1101231</td>
      <td>608625</td>
    </tr>
    <tr>
      <th>722</th>
      <td>2009-12-27</td>
      <td>1065</td>
      <td>13</td>
      <td>0.000</td>
      <td>1988.000</td>
      <td>1396</td>
      <td>0.000</td>
      <td>3384.000</td>
      <td>1298889</td>
      <td>1688557</td>
      <td>1948336</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1435271</td>
      <td>791960</td>
    </tr>
    <tr>
      <th>723</th>
      <td>2009-12-28</td>
      <td>830</td>
      <td>11</td>
      <td>0.000</td>
      <td>1305.000</td>
      <td>1104</td>
      <td>0.000</td>
      <td>2409.000</td>
      <td>1100986</td>
      <td>1431283</td>
      <td>1651483</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1216590</td>
      <td>670780</td>
    </tr>
    <tr>
      <th>724</th>
      <td>2009-12-29</td>
      <td>535</td>
      <td>9</td>
      <td>0.000</td>
      <td>833.000</td>
      <td>1482</td>
      <td>0.000</td>
      <td>2315.000</td>
      <td>913403</td>
      <td>1187424</td>
      <td>1370107</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1009310</td>
      <td>549270</td>
    </tr>
    <tr>
      <th>725</th>
      <td>2009-12-30</td>
      <td>1120</td>
      <td>12</td>
      <td>0.000</td>
      <td>1770.000</td>
      <td>1362</td>
      <td>0.000</td>
      <td>3132.000</td>
      <td>1186070</td>
      <td>1541893</td>
      <td>1779108</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1310608</td>
      <td>732990</td>
    </tr>
    <tr>
      <th>726</th>
      <td>2009-12-31</td>
      <td>620</td>
      <td>10</td>
      <td>0.000</td>
      <td>1110.000</td>
      <td>1487</td>
      <td>0.000</td>
      <td>2597.000</td>
      <td>997089</td>
      <td>1296216</td>
      <td>1495635</td>
      <td>2009-12-01</td>
      <td>104</td>
      <td>1101782</td>
      <td>611375</td>
    </tr>
  </tbody>
</table>
<p>727 rows × 15 columns</p>
</div>




```python
complete_merge = complete_merge.groupby(['Week'])['Visits', 'Visitors', 'Page Views', 'Visits to competitor sites', 'Social Media Mentions'].sum()
complete_merge = complete_merge.reset_index()
complete_merge.to_csv("complete_data_merged.csv")
own_visits = go.Scatter(
            x = complete_merge['Week'],
            y = complete_merge['Visits'],
            name = "MM Website Visits",
            line = {'color': "#800000", "width":3},
            mode = 'lines',
            yaxis="y1")

comp_visits = go.Scatter(
            x=complete_merge['Week'],
            y=complete_merge['Visits to competitor sites'],
            name = "Visits to Competitor Sites",
            line = {'color': "#0a82cc", "width":1},
            mode = 'lines',
            yaxis='y2')

comp_social = go.Scatter(
            x=complete_merge['Week'],
            y=complete_merge['Social Media Mentions'],
            name = "Competitor Social Media Mentions",
            line = {'color': "#ff8c00", "width":1},
            mode = 'lines',
            yaxis='y3')

data = [own_visits, comp_visits, comp_social]

layout = go.Layout(
    title='MedMen vs. Competitor Views and Social Media Mentions',
    titlefont=dict(
            size=24,
        ),
    xaxis=dict(
        domain=[0, 0.95],
        rangeslider=dict(
            visible = True
        )
    ),
    yaxis = dict(
        title='MM Website Visits',
        titlefont=dict(
            color='#800000'
            ),
        tickfont=dict(
            color='#800000'
            ),
        anchor='x',
        # overlaying='y',
        side='left',
        position=0
        ),
    yaxis2 = dict(
        title='Visits to competitor sites',
        titlefont=dict(
            color='#0a82cc'
            ),
        tickfont=dict(
            color='#0a82cc'
            ),
        anchor='x',
        overlaying='y',
        side='right',
        ),
    yaxis3=dict(
        title='Social Media Mentions',
        titlefont=dict(
            color='#ff8c00'
            ),
        tickfont=dict(
            color='#ff8c00'
            ),
        anchor='free',
        overlaying='y',
        side='right',
        position=1
        )
)


fig = go.Figure(data=data, layout=layout)

py.iplot(fig)
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~mcyee90/389.embed" height="525px" width="100%"></iframe>


