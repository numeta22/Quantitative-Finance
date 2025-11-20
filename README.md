# Quantitative Trading Backtester
Below is an example of a Simple Moving Average crossover backtest using Python package "backtesting", enjoy! - Beau

from backtesting import Strategy, Backtest
from backtesting.lib import crossover
import pandas as pd

ticker = 'META'
#Created a new DataFrame for modular backtesting

df = pd.DataFrame({
'Date': merged['Date'],
'Open': merged[f'{ticker} Open'],
'High': merged[f'{ticker} High'],
'Low': merged[f'{ticker} Low'],
'Close': merged[f'{ticker} Close'],
'Volume': merged[f'{ticker} Volume']
})
#Referenced the backtesting sub-module 
class SMACrossoverStrategy(Strategy):
    
    params = dict(
        short_window=10,
        mid_window=20,
        long_window=50
    )
#Review the init and next functions, "from talib import SMA" for rolling mean calculations  
    def init(self):
        price = self.data.Close
        self.sma_short = self.I(lambda x: pd.Series(x).rolling(self.p.short_window).mean(), price)                
        self.sma_mid = self.I(lambda x: pd.Series(x).rolling(self.p.mid_window).mean(), price)
        self.sma_long = self.I(lambda x: pd.Series(x).rolling(self.p.long_window).mean(), price)

    def next(self):
        if not self.position:
            if self.sma_short[-1] > self.sma_mid[-1] > self.sma_long[-1]:
                self.buy()
            else:
                if self.sma_short[-1] < self.sma_mid[-1] < self.sma_long[-1]:
                    self.close()
# Initiate backtest and review results
bt = Backtest(df, SMACrossoverStrategy, cash=10000, commission=.002)
stats = bt.run()

print(stats)
