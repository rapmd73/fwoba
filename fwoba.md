## Section 1 - Non-Technical Description

This program displays real-time trading information for a specified financial asset on an exchange. It shows the buy and sell prices, along with data related to the volume and frequency of trades at different price levels. Users can interact with the display to change how the information is presented, such as switching between different views of trade data or adjusting the precision of the displayed prices. The program also allows users to exit by pressing the 'q' key.

## Section 2 - Technical Analysis

The Python script `fwoba` is designed to analyze and display order book data from financial exchanges in a terminal-based interface using the `curses` library.

The script begins by importing necessary modules, including `sys` for system-level operations, `os` for interacting with the operating system, `gc` for garbage collection, `json` for data serialization, `time` and `datetime` for time-related functions, and `curses` for terminal manipulation. It also imports custom modules `JackrabbitRelay` (aliased as `JRR`) and `JRRsupport`.

A configuration file path is defined as `fwobaCFG`.

The core functionality is encapsulated within the `OrderBookAnalyzer` class.

**`OrderBookAnalyzer` Class:**

*   **`__init__(self, exchange, account, asset, direction, screen, percision=None)`:**
    *   Initializes the analyzer with exchange, account, asset, and trade direction.
    *   Sets up directories for ticker data.
    *   Configures precision for price rounding, defaulting to 5 if not provided.
    *   Constructs a ticker file path based on exchange, account, and asset.
    *   Initializes a file locker (`JRRsupport.Locker`) for the ticker file.
    *   Initializes variables to store ticker and order book data (`oldticker`, `ticker`, `TickerList`, `OrderBook`, `Bid`, `Ask`).
    *   Sets up `curses` screen-related attributes (`screen`, `curses`).
    *   Checks for the correct number of command-line arguments and calls `Help` if insufficient.
    *   If more than 5 arguments are provided, it attempts to set the precision from the 6th argument.
    *   Clears the `sys.argv` list to prevent the `JackrabbitRelay` from processing configuration arguments.
    *   Initializes a logger (`JRR.JackrabbitLog`) and a signal interceptor (`JRRsupport.SignalInterceptor`) to handle graceful exits and prevent file corruption.
    *   Initializes the `JRR.JackrabbitRelay` object, which is responsible for fetching data from exchanges. The `percision` is adjusted to 4 if the `relay.Framework` is not 'ccxt'.
    *   Calls `InitializeScreen()` to set up the `curses` environment.

*   **`Help(self, args, argslen)`:**
    *   Prints a usage message to the console and exits the program if the required command-line arguments are not provided.

*   **`Div(self, a, b)`:**
    *   A utility function to perform division, returning 0 if a division-by-zero error occurs.

*   **`InitializeScreen(self)`:**
    *   Configures color pairs for the `curses` interface (red, green, yellow, white, cyan/blue, cyan/black, black/black).
    *   Disables echoing of typed characters to the screen (`curses.noecho()`).
    *   Sets the `curses` screen to non-blocking mode (`self.screen.nodelay(True)`), meaning `getch()` will not wait indefinitely for input.
    *   Clears the screen.

*   **`GetTicker(self)`:**
    *   Fetches the latest ticker information for the asset using `self.relay.GetTicker()` and stores it in `self.ticker`, while preserving the previous ticker data in `self.oldticker`.

*   **`GetOrderBook(self)`:**
    *   Fetches the order book data for the asset using `self.relay.GetOrderBook()`.
    *   If the framework is 'ccxt', it processes the 'bids' and 'asks' lists, rounding prices to the configured precision and aggregating volumes for identical prices. The processed lists are then sorted by price.
    *   If the framework is 'oanda', it processes the order book data, rounding prices and aggregating `longCountPercent` and `shortCountPercent` for identical prices. The results are stored in a sorted list.

*   **`LoadTickerData(self)`:**
    *   Calls `GetTicker()` and `GetOrderBook()` to update the latest market data.
    *   If the ticker file exists, it acquires a lock, reads lines from the file, releases the lock, and then processes each line.
    *   Each line is expected to be a JSON string representing ticker data. It attempts to load the JSON, removes any 'OrderBook' key if present, and appends the data to `self.TickerList`. Malformed JSON lines are skipped.
    *   Finally, it calls `FrequencyCount()` to process the loaded ticker data.

*   **`FrequencyCount(self)`:**
    *   If `self.TickerList` contains more than one entry, it calculates the frequency of bid and ask prices.
    *   It iterates through `self.TickerList`, rounding bid and ask prices to the configured precision.
    *   It populates `self.Bid` and `self.Ask` dictionaries, where keys are prices and values are the counts of how many times that price has appeared in the ticker data.

*   **`WipeScreen(self)`:**
    *   Resets flags related to maximum visible values and clears the `curses` screen from the cursor position to the bottom.

*   **`ShowHeader(self, buyTotal, buyWeight, buyFreq, buy, BuyPrice, SellPrice, sell, sellFreq, sellWeight, sellTotal)`:**
    *   Clears the screen and sets up header information.
    *   Determines color pairs based on the trade `direction` ('long' or 'other').
    *   Displays a colored header bar with "Buy", "Spread", and "Sell" labels.
    *   Displays the current buy price, sell price, and the spread between them.
    *   Conditionally displays different metrics in the header based on `self.htype`:
        *   `htype=0`: "FreqWeight" (weighted frequency) and calculated values.
        *   `htype=1`: "Frequency" (raw count) and associated prices.
        *   `htype=2`: "Volume" and associated prices.
    *   Uses `self.Div` for calculations to avoid division by zero.

*   **`ShowRow(self, row, c, buyTotal, buyWeight, buyFreq, buy, BuyPrice, SellPrice, sell, sellFreq, sellWeight, sellTotal, maxLval, maxRval)`:**
    *   Displays a single row of order book data.
    *   Adds the row number and the spread.
    *   Highlights the maximum bid and ask prices if they are encountered, setting `self.maxLvisable` and `self.maxRvisable` flags.
    *   Displays the bid and ask prices, applying bold green color if they are the maximums.
    *   Conditionally displays different metrics in the row based on `self.htype`:
        *   `htype=0`: Weighted frequency.
        *   `htype=1`: Raw frequency.
        *   `htype=2`: Volume.
    *   Uses color pair 7 (black on black) for zero values in the displayed metrics.

*   **`DisplayOanda(self)`:**
    *   Handles the display logic specifically for the Oanda exchange.
    *   Checks if the terminal screen width is sufficient and exits if not.
    *   Retrieves the current bid and ask prices from `self.ticker`.
    *   Iterates through the processed `self.OrderBook` data (which is already sorted and aggregated for Oanda).
    *   For each entry, it calculates:
        *   `buyFreq` and `sellFreq` by looking up the price in `self.Bid` and `self.Ask` dictionaries (obtained from `LoadTickerData`).
        *   `buyWeight` and `sellWeight` by multiplying volume/percentage by frequency.
        *   `buyTotal` and `sellTotal` accumulate the weighted values.
    *   Determines the indices (`buyIDX`, `sellIDX`) corresponding to the current bid and ask prices within the order book.
    *   Calls `ShowHeader()` to display the header information.
    *   Calculates the maximum depth to display based on screen height.
    *   Iterates to find the maximum weighted frequency, frequency, or volume (`maxBorders`, `maxSorders`) and their corresponding prices (`maxLval`, `maxRval`) to enable highlighting.
    *   Iterates again to display the order book rows using `ShowRow()`, up to the calculated `depth`.
    *   If the maximum bid or ask values are off-screen, they are displayed in the last row.
    *   Refreshes the `curses` screen.

*   **`DisplayCCXT(self)`:**
    *   Handles the display logic for exchanges using the CCXT framework.
    *   Performs similar initial checks for screen width.
    *   Retrieves current bid and ask prices.
    *   Processes the `self.OrderBook['bids']` and `self.OrderBook['asks']` lists, which are already aggregated and sorted by `GetOrderBook`.
    *   Calculates `buyFreq`, `sellFreq`, `buyWeight`, `sellWeight`, `buyTotal`, and `sellTotal` similarly to `DisplayOanda`.
    *   Determines `buyIDX` and `sellIDX` for the current bid and ask prices.
    *   Calls `ShowHeader()`.
    *   Calculates the display `depth` and finds maximum values for highlighting.
    *   Displays order book rows using `ShowRow()`.
    *   Handles displaying off-screen maximums in the last row.
    *   Refreshes the `curses` screen.

**Helper Functions:**

*   **`SortPrice(n)`:** A simple function that returns the first element of a list, used for sorting.

*   **`ReadConfig(exchange, account, asset)`:**
    *   Reads the configuration from the `fwobaCFG` file.
    *   Expects each line to be a JSON object.
    *   Validates that required keys ('Exchange', 'Account', 'Asset', 'Percision', 'History') are present in each configuration entry.
    *   Sets default values for 'History' (86400 seconds) and 'Percision' (4) if they are missing.
    *   Ensures 'History' is at least 86400.
    *   Parses 'Percision' and 'History' to integers.
    *   Returns the configuration dictionary that matches the provided `exchange`, `account`, and `asset`.
    *   Exits the program if the configuration file is damaged, missing required items, or if the specified asset is not found in the configuration.

**Main Driver (`main(screen)`):**

*   This function is executed when the script is run directly. It's wrapped by `curses.wrapper` which handles initialization and cleanup of the `curses` environment.
*   It retrieves the exchange, account, asset, and direction from command-line arguments.
*   It calls `ReadConfig` to load the relevant configuration.
*   It instantiates the `OrderBookAnalyzer` class.
*   It enters an infinite loop (`while True`):
    *   It gets user input using `screen.getch()`.
    *   If 'q' or 'Q' is pressed, it exits the program gracefully.
    *   If a digit from '0' to '8' is pressed, it updates `fwoba.percision` and clears the screen.
    *   If the spacebar is pressed, it cycles through `htype` (0, 1, 2) to change the displayed metric.
    *   Specific keys ('W'/'w', 'F'/'f', 'V'/'v') directly set `htype` to 0, 1, or 2 respectively.
    *   It calls `fwoba.LoadTickerData()` to fetch and process market data.
    *   It calls either `fwoba.DisplayCCXT()` or `fwoba.DisplayOanda()` based on the `relay.Framework`.
    *   It uses `JRRsupport.ElasticSleep(1)` to introduce a delay if no input was received in the previous iteration, otherwise, it proceeds without an explicit sleep, relying on `getch()`'s non-blocking nature and the refresh rate of the display.

**Execution Entry Point (`if __name__ == '__main__':`)**

*   This block ensures that `curses.wrapper(main)` is called only when the script is executed directly, not when imported as a module. `curses.wrapper` handles the setup and teardown of the `curses` terminal environment.
