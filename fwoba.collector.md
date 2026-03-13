## Section 1 - Non-Technical Description

This program acts as a data collector for financial markets, specifically tracking price information for various trading pairs across different exchanges. It reads configuration settings to know which exchanges, accounts, and assets to monitor. For each monitored item, it continuously fetches the latest price data and stores a history of these prices. If configured, it can also save detailed order book information. The program is designed to manage multiple data collection tasks concurrently, distributing the workload across available processing power, and it includes mechanisms to prevent data loss or corruption, especially during unexpected interruptions.

## Section 2 - Technical Analysis

The Python script `JackrabbitRelay.py` is designed to collect and store ticker and order book data from financial exchanges. It utilizes several custom modules: `JackrabbitRelay` (aliased as `JRR`) for core relay functionalities and logging, and `JRRsupport` for utility functions like file locking, reading files, and managing processes.

The script begins by setting up the Python path to include a local library and then imports necessary modules such as `sys`, `os`, `gc`, `json`, `time`, and `datetime`. It defines a configuration file path `fwobaCFG`. The number of simultaneous processes to handle data collection is determined by the number of CPU cores available, stored in `NumberProcesses`. Global signal interception is initialized using `JRRsupport.SignalInterceptor` to prevent data corruption during program termination (e.g., via CTRL-C).

The `Collector` class is central to the data collection process. Each instance of `Collector` is responsible for a specific trading pair (`Asset`) on a given `Exchange` and `Account`.

-   **`__init__`**: Initializes the collector with exchange details, account, asset, and optional history length. It defines a directory for ticker data (`TickerDataDir`) and constructs a unique filename for the ticker data based on the exchange, account, and asset. A file locker (`JRRsupport.Locker`) is created for this ticker file to manage concurrent access. It also initializes flags and variables to store ticker and order book data, and sets up its own logger and signal interceptor. Crucially, it instantiates `JRR.JackrabbitRelay` to establish a connection to the specified exchange for data retrieval.
-   **`Help`**: A placeholder function that prints a usage message and exits if incorrect arguments are provided.
-   **`LoadTickerData`**: This method is responsible for fetching the latest ticker data and updating the historical record. It first re-initializes the `JRR.JackrabbitRelay` connection. It then retrieves the current ticker data using `self.relay.GetTicker()`. If the ticker file exists, it reads the historical ticker data from it, line by line, parsing each line as JSON. It handles potential errors during JSON parsing by skipping damaged lines. It compares the new ticker data with the last entry in the historical list and appends it if there's a change in bid or ask prices, marking `self.TickerUpdate` as `True`. The `TickerList` is then truncated to maintain the specified history length. Finally, if `self.TickerUpdate` is true, it calls `SaveTickerData`.
-   **`SaveTickerData`**: This method writes the updated historical ticker data to the ticker file. It uses a file locker to ensure exclusive write access. It iterates through the `TickerList`, writing each ticker as a JSON string to the file, but only if it differs from the previously written ticker to avoid redundant entries. If `self.saveOB` is `True`, it also fetches the order book data using `self.relay.GetOrderBook()` and saves it to a separate file named with the date and trading pair details.
-   **`GetTicker`**: A simple method that calls `self.relay.GetTicker()` to fetch the current ticker information for the asset.
-   **`GetOrderBook`**: A simple method that calls `self.relay.GetOrderBook()` to fetch the current order book for the asset.
-   **`DuplicateTicker`**: Compares the `oldticker` and `ticker` attributes. It returns `True` if either is `None` or if the 'Bid' and 'Ask' prices are identical, indicating no significant change. Otherwise, it returns `False`.

The `ReadConfig()` function reads configuration settings from the `fwobaCFG` file. It expects each line to be a JSON object representing a trading pair configuration. It validates that required keys (`Exchange`, `Account`, `Asset`, `History`, `Diagnostics`) are present, sets default values for `Diagnostics` and `History` if missing, and ensures `History` is at least 86400 seconds. It also adds a `Key` ('fwoba') and constructs a `Pair` identifier. The function returns a dictionary where keys are the `Pair` identifiers and values are the configuration dictionaries.

`LoadSingleTicker(Config)` takes a configuration dictionary for a single trading pair. It assigns the `Collector` instance to `Config['Relay']` and calls `LoadTickerData()` on it. If diagnostics are enabled, it prints the pair, bid, spread, and ask if `DuplicateTicker()` returns `False`. Finally, it clears `TickerCollect.TickerList` and calls `gc.collect()` to free up memory.

`SpawnLoadSingleTicker(*args,**kwargs)` is a wrapper function designed to be run in a separate process. It takes keyword arguments, unpacks them into a `Config` dictionary, calls `LoadSingleTicker`, and handles any exceptions. After execution, it signals completion by updating a shared memory lock (`fwobaMemory`) to 'Done'.

The `main()` function orchestrates the entire process.
-   It first checks command-line arguments for an optional `NumberProcesses` override.
-   It then clears command-line arguments that might interfere with the `JRR.JackrabbitRelay` initialization.
-   It initializes dictionaries `fwobaMemory` and `fwobaRelay` to store shared memory locks and `Collector` instances, respectively.
-   It enters an infinite loop (`while True`).
-   Inside the loop, it calls `ReadConfig()` to get the latest configuration.
-   It iterates through each configured trading pair (`cfg`).
-   If `NumberProcesses > 1` (multiprocessing is enabled):
    -   It ensures a `Collector` instance exists for the `cfg` in `fwobaRelay`, creating one if necessary. It also checks if order book saving is enabled for this pair.
    -   It ensures a shared memory lock exists for the `cfg` in `fwobaMemory`.
    -   It retrieves the current status of the trading pair from `fwobaMemory`. This status indicates whether the pair is 'Running', 'NoData', or 'Done'.
    -   If the status is 'NoData' or 'Done', it means the pair is available to be processed by a new worker. It updates the status in `fwobaMemory` to 'Running', associates the `Collector` and memory ID with the `Config` for this pair, and then starts a new process using `interceptor.StartProcess(SpawnLoadSingleTicker, kwargs=Config[cfg])`.
    -   It then enters a `while` loop to ensure that the number of active child processes does not exceed `NumberProcesses - 1`, pausing execution if it does.
-   If `NumberProcesses == 1` (single process mode):
    -   It creates a `Collector` instance for the pair.
    -   It directly calls `LoadSingleTicker()` to process the pair in the current process.
-   After processing all configured pairs, it pauses for a short duration using `JRRsupport.ElasticSleep(1)` before the loop repeats, re-reading the configuration and checking for new or available tasks.

The `if __name__ == '__main__':` block ensures that the `main()` function is called when the script is executed directly.