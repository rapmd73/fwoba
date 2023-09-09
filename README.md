# Frequency Weighted OrderBook Analysis (FWOBA)

This is a collection programs that download and analyze orderbook data in near real-time and is a full working example of orderbook analysis built on
top of the [Jackrabbit Relay](https://github.com/rapmd73/JackrabbitRelay/wiki) framework.

## Programs/Files

| Name | Description |
| --- | --- |
| [fwoba.cfg](https://github.com/rapmd73/fwoba/#fwobacfg) | The config file for the collector and analysis program |
| [LaunchCollector](https://github.com/rapmd73/fwoba/#launchcollector) | BASH script suitable for launching in a crontab at reboot |
| [fwoba.collector](https://github.com/rapmd73/fwoba/#fwobacollector) | The actual collection program that stores the ticker data |
| [fwoba](https://github.com/rapmd73/fwoba/#fwoba) | This is the main interactive analysis program that displays orderbook information in relation to a weighted frequency count. |

## Example Screens

This screen shot shows BTC/USDT being analyzed and monitored by FWOBA. A percision of 3 digits is used to better visualize the volume of each entry.

![BTC/USDT](https://github.com/rapmd73/fwoba/blob/main/Images/fwoba.BTCUSDT.jpg)

This screen shot shows EUR/USD being analyzed and monitored by FWOBA. This analyzes is at the pip level or four digits behind the decimal point.

![EUR/USD](https://github.com/rapmd73/fwoba/blob/main/Images/fwoba.EURUSD.jpg)

## fwoba.cfg

### Configuration File Description

The configuration file provided here plays a pivotal role in defining the behavior and parameters of the program responsible for collecting and
analyzing order book data from various exchanges. This file is instrumental in ensuring the program operates effectively and efficiently. Here's a
breakdown of the key elements within the configuration file:

- **Exchange**: This field specifies the source of the order book data. It can take one of two values: "OANDA" or one of the 100+ different
cryptocurrency exchanges. For example, if set to "Kraken," it indicates that the program will collect data from the Kraken exchange.

- **Account**: The "Account" field serves as a reference to the account information within the Jackrabbit Relay exchange file. When using exchanges like
Kraken, this field should match the corresponding Kraken.cfg file within Jackrabbit Relay. It helps establish the connection to the specific exchange
account.

- **Asset**: The "Asset" field designates the trading instrument or asset for which order book data will be collected. It should align with the
supported trading pairs on the chosen exchange. For instance, "EUR/USD" indicates the Euro to US Dollar trading pair.

- **Precision**: This field controls the number of decimal places used in calculations for range, support, and resistance levels based on the order book
data. It's an essential parameter for tailoring the analysis to specific trading preferences. A higher precision value means more detailed calculations.

- **History**: The "History" field defines the duration for which bid and ask data is stored on disk. It's a crucial parameter that balances historical
data retention with storage requirements. The value is expressed in seconds, and its significance cannot be overstated.

- **Diagnostics**: The "Diagnostics" field serves as a visual reference. When set to "No," it indicates that no additional diagnostic information will
be displayed. However, if running the program from a screen or command line and set to "Yes," it will print ticker data on the screen, aiding in
monitoring and debugging.

### Importance of Historical Data and Storage Requirements

The "History" parameter is particularly significant in this configuration file, as it directly impacts the quality of analysis and storage requirements.
Here's why historical data and its careful management are critical:

- **Data Quality**: The historical data duration, defined by the "History" field, influences the quality of the analysis. Inadequate historical data,
with too short a duration, can result in poor results. Without sufficient historical context, it becomes challenging to identify meaningful patterns and
trends in the order book.

- **Storage Balancing**: On the other hand, storing excessive historical data unnecessarily can lead to substantial storage requirements. This can be
costly and inefficient. Therefore, selecting an appropriate duration for historical data strikes a balance between data quality and storage efficiency.

- **Informed Decision-Making**: Historical data is the foundation for informed decision-making in trading. It allows traders to understand past market
behavior, identify support and resistance levels, and assess order flow dynamics. Without a sufficient historical dataset, traders risk making
less-informed decisions.

- **Backtesting Strategies**: Historical data is essential for backtesting trading strategies. Traders can evaluate how their strategies would have
performed in past market conditions. Incomplete historical data limits the accuracy of backtesting and the ability to refine strategies effectively.

## LaunchCollector

### Installing "LaunchCollector" in Cron

To ensure that the "fwoba.collector" program runs continuously and automatically at system reboot, you can add the "LaunchCollector" script to the cron
tab for the root user. Follow these steps:

1. **Edit the Crontab Configuration**:

- Open the cron configuration for the root user using the terminal:

```bash
sudo crontab -e
```

2. **Add a Cron Job Entry**:

- In the text editor that opens, add a new cron job entry. This entry specifies when and how often the script should run. To run the "LaunchCollector"
script at system reboot, use the `@reboot` directive.

- Example entry:

```bash
@reboot /bin/bash /home/fwoba/LaunchCollector
```

1. **Save and Exit**:

- Save the changes to the crontab file and exit the text editor. The method for saving and exiting depends on the text editor you're using. For example,
in the nano editor, you can press `Ctrl + O` to save and `Ctrl + X` to exit.

1. **Verification**:

- You can verify that the cron job has been added successfully by listing the root user's crontab:

```bash
sudo crontab -l
```

This command should display the cron job entry you just added.

Now, the "LaunchCollector" script will be executed automatically at system reboot as the root user, ensuring that the "fwoba.collector" program runs
continuously even after system restarts.

## fwoba.collector

### Overview

The Ticker Data Collector is a software tool designed to gather and manage real-time price information, known as ticker data, from various
cryptocurrency exchanges. This tool is typically used as part of a larger trading system, such as the Jackrabbit Relay, to ensure that traders have
access to up-to-date and accurate pricing information for different cryptocurrency trading pairs.

### Key Features

1. **Data Collection**: The primary function of this tool is to collect and maintain ticker data. Ticker data represents the current prices (bid and
ask) of cryptocurrencies on specific exchanges.

1. **Configuration**: The tool is configurable through a settings file. This file contains information about which trading pairs to monitor, the
associated exchanges, and additional settings. Users can customize it to suit their trading needs.

1. **Concurrency**: To efficiently manage multiple trading pairs, the tool can run multiple processes simultaneously. This allows it to gather data for
multiple pairs at the same time, leveraging the processing power of the computer.

1. **Diagnostics**: For users interested in monitoring changes in ticker data, the tool offers a diagnostics option. This feature helps identify when
prices for a particular trading pair have changed.

### Purpose

The primary purpose of the Ticker Data Collector is to provide traders with accurate and real-time pricing information. This information is essential for making informed decisions when buying or selling cryptocurrencies. By continuously collecting and updating ticker data, the tool supports traders in executing their trading strategies effectively.

### Usage

Traders typically integrate the Ticker Data Collector into their trading systems. They configure the tool to monitor the specific trading pairs they are interested in and set it to run continuously. Importantly, it is designed to run automatically from `cron` at server reboot. As the tool collects and updates ticker data, it becomes a crucial resource for traders to monitor market conditions and make timely trading decisions.

## fwoba

### Overview

The Order Book Analyzer is a powerful tool designed for experienced traders and programmers seeking in-depth insights into order book dynamics on
various cryptocurrency exchanges. This tool is part of the Jackrabbit Relay project, providing real-time trading data analysis capabilities.

### Features

- **Exchange Agnostic**: Supports multiple cryptocurrency exchanges, providing a unified analysis platform.

- **Custom Precision**: Adjust the precision level to control the number of decimal places in the analysis, enabling tailored data views.

- **Real-time Data**: Access real-time ticker and order book data to make informed trading decisions.

- **Educational**: Use it for learning purposes to explore order book mechanics, market depth, and trading strategies.

- **Curses-based UI**: Utilizes the curses library to create a user-friendly, terminal-based interface for displaying data.

### Usage

**Run the Script**: Execute the script with the following command, replacing `<exchange>`, `<account>`, `<asset>`, and `<direction>` with your
trading parameters:

```bash
./fwoba <exchange> <account> <asset> <direction>
```

The Frequency Weighted OrderBook Analyzer (FWOBA) is a powerful tool designed for experienced traders and programmers seeking in-depth insights into
order book dynamics on various cryptocurrency exchanges and provides a snapshot of the order book for a given asset, which is updated in near real-time
with a specified time interval. It consists of several columns, each containing essential information for analyzing the order book and making trading
decisions. Here is a list of the keys and their purpose in the program:

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

### Utilization

This program is intended for learning purposes and can be employed to develop trading strategies, conduct technical analysis, and enhance trading
education materials. Additionally, it plays a crucial role in maintaining market transparency and fairness by aiding in the detection of market
manipulation.
