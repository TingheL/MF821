# Name: Tinghe Lou
# Email address:thlou@bu.edu
# Description: VWAP
# 
#
# 
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime
import datetime as dt
import mplfinance as mpf

df = pd.read_csv('AAPL_20130730_trades.csv')
df.DATE = df.DATE.astype(str)
date = df.DATE+' '+df.TIME
df['datetime'] = pd.to_datetime(date, format='%Y%m%d %H:%M:%S')

## exlude time outside normal trading hours
start30 = datetime.strptime('2013-07-30 09:30:00','%Y-%m-%d %H:%M:%S')
end30 = datetime.strptime('2013-07-30 16:00:00','%Y-%m-%d %H:%M:%S')
start31 = datetime.strptime('2013-07-31 09:30:00','%Y-%m-%d %H:%M:%S')
end31 = datetime.strptime('2013-07-31 16:00:00','%Y-%m-%d %H:%M:%S')
df = df.drop(df[(df.datetime < start30) | ((df.datetime > end30) & (df.datetime < start31)) | (df.datetime > end31)].index).reset_index(drop=True)
# print((df.datetime[22]-df.datetime[21]).seconds)

## On the way to VWAP
OPEN = []
HIGH = []
LOW = []
CLOSE = []
VOLUME = []
price = []
volume = []
ind = [df.datetime[0]]
p = df.datetime[0]
j = 0
for i in range(len(df)-1):
    diff = df.datetime[i+1]-p
    if diff.days==0 and diff.seconds<60:
        if price == []:
            price.append(df.PRICE[j])
            volume.append(df.SIZE[j])
        price.append(df.PRICE[i+1])
        volume.append(df.SIZE[i+1])
    else:
        j = i+1
        if p < datetime.strptime('2013-07-30 16:00:00','%Y-%m-%d %H:%M:%S'):
            p = pd.to_datetime((p+dt.timedelta(minutes=1)).strftime("%Y-%m-%d %H:%M:%S"))
        elif p == datetime.strptime('2013-07-30 16:00:00','%Y-%m-%d %H:%M:%S'):
            p = datetime.strptime('2013-07-31 9:30:00','%Y-%m-%d %H:%M:%S')
        else:
            p = pd.to_datetime((p+dt.timedelta(minutes=1)).strftime("%Y-%m-%d %H:%M:%S"))
        ind.append(p)
        OPEN.append(price[0])
        CLOSE.append(price[-1])
        HIGH.append(max(price))
        LOW.append(min(price))
        VOLUME.append(sum(volume))
        price = []
        volume = []

OPEN.append(price[0])
CLOSE.append(price[-1])
HIGH.append(max(price))
LOW.append(min(price))
VOLUME.append(sum(volume))

chart = pd.DataFrame(columns = ['time','open','high','low','close','volume',
                                'TP','PV','TPV','Tvolu','VWAP'])
chart['open'] = OPEN
chart['high'] = HIGH
chart['low'] = LOW
chart['close'] = CLOSE
chart['time'] = ind
chart['volume'] = VOLUME
chart['TP'] = (chart.high+chart.low+chart.close)/3
chart['PV'] = chart.volume*chart.TP
chart.loc[:391,'TPV'] = chart.PV[:391].cumsum()
chart.loc[391:,'TPV'] = chart.PV[391:].cumsum()
chart.loc[:391,'Tvolu'] = chart['volume'][:391].cumsum()
chart.loc[391:,'Tvolu'] = chart['volume'][391:].cumsum()
chart['VWAP'] = chart.TPV/chart.Tvolu
print(chart)

## Visualization
## VWAP vs close price
plt.plot(chart.close)
plt.plot(chart.VWAP)
plt.legend(['close','VWAP'])
plt.title('VWAP vs close price')
plt.xlabel('time')
plt.ylabel('price')
plt.show()
## candlestick
chart = chart.set_index(['time'])
mpf.plot(chart.iloc[:,0:5], title="Candlestick for AAPL",type='candle',style="binance",volume=True)


