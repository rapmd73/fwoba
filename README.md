# Frequency Weighted OrderBook Analysis (FWOBA)

This program is a full working example of orderbook analysis built on top of the [Jackrabbit Relay](https://github.com/rapmd73/JackrabbitRelay/wiki)
framework.

## Programs

| Name | Description |
| --- | --- |
| fwoba | This is the main interactive analysis program that displays orderbook information in relation to a weighted frequency count. |
| fwoba.cfg | The config file for the collector and analysis program |
| fwoba.collector | The actual collection program that stores the ticker data |
| LaunchCollector | BASH script suitable for launching in a crontab at reboot |

## Example Screens

This screen shot shows BTC/USDT being analyzed and monitored by FWOBA. A percision of 3 digits is used to better visualize the volume of each entry.

![BTC/USDT](https://github.com/rapmd73/fwoba/blob/main/Images/fwoba.BTCUSDT.jpg)

This screen shot shows EUR/USD being analyzed and monitored by FWOBA. This analyzes is at the pip level or four digits behind the decimal point.

![EUR/USD](https://github.com/rapmd73/fwoba/blob/main/Images/fwoba.EURUSD.jpg)

## fwoba

### Overview

This dataset provides a snapshot of the order book for a given asset, which is updated in near real-time with a specified time interval. It consists of
several columns, each containing essential information for analyzing the order book and making trading decisions. Here is a list of the keys and their
purpose in the program:

1. **q** or **Q**. Gracefully quit the program and return to the command prompt.
1. **1** to **8**. Digits after the decimal for vireing and correlating different zonwes and regions in the orderbook.

### Columns

- **FreqWeight:** Represents the weighted value associated with each data point.
- **Frequency:** Indicates the frequency count for each data point.
- **Volume:** Specifies the trading volume associated with each data point.
- **Buy:** Displays buy values on the bid side of the order book.
- **Spread:** Represents the spread between buy and sell values.
- **Sell:** Displays sell values on the ask side of the order book.

### Significance

1. Support and Resistance Levels. The data is instrumental in identifying support and resistance levels within the asset's market. Support levels are
where buying interest is potentially strong, preventing further price declines. Resistance levels are areas where selling interest may be robust,
halting upward price movement.

1. Liquidity Analysis. Order book data allows traders to assess market liquidity. By examining the depth of the order book and the volume of buy and
sell orders at various price levels, traders can gauge how easily they can enter or exit positions without significantly impacting the market price.
Higher liquidity often means lower trading costs and reduced slippage.

1. Market Sentiment. The distribution of buy and sell orders in the order book provides insights into market sentiment. For example, a lopsided order
book with a significant number of buy orders may indicate bullish sentiment, while a preponderance of sell orders suggests bearish sentiment. Monitoring
changes in sentiment can help traders make informed decisions.

1. Order Flow Analysis. Order book data allows for order flow analysis, which involves tracking the sequence of trades and orders in real-time. Traders
can identify trends in order flow, such as increasing buying or selling pressure, which can be valuable for short-term trading strategies.

1. Price Discovery. The order book plays a crucial role in price discovery. As new buy and sell orders are placed and executed, they contribute to price
movements. Traders can observe how the order book evolves to anticipate potential price changes and better time their entries and exits.

1. Stop Loss and Take Profit Levels. Apart from support and resistance, order book data can help traders determine appropriate stop loss and take profit
levels. For example, identifying areas with a cluster of stop orders can indicate potential stop loss levels, while areas with a high concentration of
limit orders can suggest take profit levels.

1. Market Health Assessment. By assessing the stability and consistency of the order book over time, traders can gauge the overall health of the market.
A stable and orderly order book suggests a healthy and well-functioning market, while erratic or disorganized order book behavior may signal market
stress or uncertainty.

1. High-Frequency Trading (HFT) Detection. Order book data is vital for detecting and monitoring high-frequency trading (HFT) activities. Rapid order
placement and cancellations, as well as unusual order patterns, can indicate HFT strategies. Recognizing HFT can help traders adapt their strategies to
changing market dynamics.

1. Risk Management. Traders can use order book data to implement risk management strategies. For example, by identifying potential price levels with
high volatility or low liquidity, traders can adjust their position sizes or opt to avoid trading in these areas altogether to mitigate risk.

1. Algorithmic Trading Strategies. Quantitative traders and algorithmic trading systems can use order book data as input for developing and fine-tuning
trading algorithms. Algorithms can be designed to respond to changes in the order book, allowing for automated trading decisions.

### Real-Time Updates

The dataset's near real-time nature, with updates at regular intervals, makes it valuable for traders to stay current with market dynamics. This allows
for timely decision-making based on shifts in the order book, offering a competitive advantage in the asset's market.

### Analysis and Decision-Making

1. **Volume Analysis:** Market manipulation may involve unusual volume spikes at specific price levels. For instance, if there's an abrupt and excessive
increase in buy volume at a particular support level followed by a sudden sell-off, it could be an attempt to lure traders into buying before a planned
market drop.

   *Example*: A sudden surge in buy volume, as seen in the order book data, is followed by an immediate and substantial sell-off, creating a false
   impression of strong buying interest.

2. **Spread Manipulation:** Market manipulators may intentionally widen the spread to create panic or confusion among traders. This can be observed as
an exaggerated spread increase that doesn't align with typical market conditions.

   *Example*: The spread between buy and sell orders rapidly widens to an extreme level, causing traders to panic and make hasty decisions.

3. **Spoofing:** Market manipulators may place and then quickly cancel large buy or sell orders to create a false impression of market sentiment. This
can be detected by monitoring frequent order cancellations at specific price levels.

   *Example*: Large buy orders appear at a key support level but are canceled within seconds, leading traders to believe that significant buying
   interest exists.

### Exposing Market Manipulation

Identifying market manipulation through order book data requires vigilance and a keen eye for unusual patterns. Traders and regulators should be
cautious of sudden, anomalous trading behavior that doesn't align with typical market dynamics. Reporting suspicious activity is essential to maintain
market integrity.

#### Example 1: Pump and Dump

**Scenario:** A group of manipulators artificially inflates the price of an asset by executing a series of large buy orders, creating the illusion of
strong demand. **Detection:** Traders should be cautious if they observe a rapid and significant increase in both volume and price, especially if it
deviates from historical trends. It's essential to verify whether the increased buying activity is backed by genuine market sentiment.

#### Example 2: Spread Manipulation

**Scenario:** Market manipulators intentionally widen the spread between buy and sell orders to discourage trading or create panic. **Detection:**
Traders can spot spread manipulation by monitoring the spread's behavior during normal market conditions. Abrupt and exaggerated spread widening that
doesn't align with news or events should raise suspicion.

#### Example 3: Spoofing

**Scenario:** A manipulator places a large sell order at a key resistance level to create the impression of impending price decline, only to cancel it
before execution. **Detection:** Traders should be wary of frequent order cancellations, especially if they occur immediately after substantial orders
are placed. This suggests an attempt to manipulate market sentiment.

### Exposing Market Manipulation

Identifying market manipulation through order book data requires vigilance and a keen eye for unusual patterns. Traders and regulators should be
cautious of sudden, anomalous trading behavior that doesn't align with typical market dynamics. Reporting suspicious activity is essential to maintain
market integrity.

### Usage

This dataset is intended for learning purposes and can be employed to develop trading strategies, conduct technical analysis, and enhance trading
education materials. Additionally, it plays a crucial role in maintaining market transparency and fairness by aiding in the detection of market
manipulation.
