# Bollinger-Bands
from QuantConnect.Indicators import *
import decimal as d

### <summary>
### In this example we are looking for price to breakout above the bollinger bands
### and look to buy when we see that. We hold our position until price touches the 
### middle band of the bollinger bands.
###

class BollingerBreakoutAlgorithm(QCAlgorithm):

    def Initialize(self):

        self.SetStartDate(2016, 6, 1)  #Set Start Date
        self.SetEndDate(2017, 7, 1)    #Set End Date
        self.SetCash(10000)             #Set Strategy Cash
        self.SetBrokerageModel(BrokerageName.GDAX)
        self.AddCrypto("BTCUSD", Resolution.Daily)
        
        # create a bollinger band
        self.Bolband = self.BB("BTCUSD", 20, 2, MovingAverageType.Simple, Resolution.Daily)

        # set warmup period
        self.SetWarmUp(20)
        

    def OnData(self, data):
        
        
        holdings = self.Portfolio["BTCUSD"].Quantity
        price = self.Securities["BTCUSD"].Close
        
        # buy if price closes above upper bollinger band
        if holdings <= 0:
             if price > self.Bolband.LowerBand.Current.Value:
                self.SetHoldings("BTCUSD", 1.0)
        
        # sell if price closes below middle bollinger band
        if holdings > 0 and price < self.Bolband.MiddleBand.Current.Value:
                self.Liquidate()                        
