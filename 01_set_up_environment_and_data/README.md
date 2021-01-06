# Setting up your environment and sourcing the data

## Objective

Set up your Python environment with the requisite libraries, install and test Zipline, and download the Quandl Wiki data.

## Workflow

1. **Install [Docker Desktop](https://docs.docker.com/desktop/)** for [Windows](https://docs.docker.com/docker-for-windows/install/) or [Mac OS](https://docs.docker.com/docker-for-mac/install/).
    - For guidance on installing Docker for Windows 10 Home, which requires an additional step to enable the virtual machine platform, see [here](https://docs.docker.com/docker-for-windows/install-windows-home/).
    - Review the "Getting Started" guides for [Windows](https://docs.docker.com/docker-for-windows/) or [Mac OS](https://docs.docker.com/docker-for-mac/).
    - Please see [here](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/installation#running-the-notebooks-using-a-docker-container) for additional details on Docker installation and some advice on troubleshooting.
    - Under *Preferences*, look for *Resources* to find out how you can **increase the memory** allocated to the container; the default setting is too low given the size of the data. Increase to at least 4GB.
    - The Docker service needs to be running as a [daemon](https://en.wikipedia.org/wiki/Daemon_(computing)) in the background before you can work with containers; this is usually enabled by default. See instructions for [linux](https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot) command line. For Docker Desktop, you can enable this under Settings/General.  
2. **Clone the starter repo** using the following command: `git clone https://github.com/stefan-jansen/automated-trading-strategy-using-machine-learning.git` and change into the new directory.
3. [Register](https://www.quandl.com/sign-up) for a (free) personal **Quandl account** to obtain an API key that you'll below to download stock price data.  
3. We'll be using a **Docker image** based on Ubuntu 20.04 with the Anaconda Python distribution installed. It also contains two `conda` environments, one to run backtests with Zipline and one for everything else. To work with the Docker image, run the following command:
   The following Docker `docker run` command (executed in the shell from the root directory of the cloned starter repo) accomplishes several things at once (see the Getting Started guides linked above for more detail). The Mac version is as follows (more details below):
    
   ```docker
   docker run -it -v $(pwd):/home/manning/liveproject -p 8888:8888 -e QUANDL_API_KEY=<yourkey> --name liveproject appliedai/manning:liveproject bash
    ```
   - flag -`it`: create and run a local container in interactive mode
   - `-v $(pwd):/home/manning/liveproject`: mount the current directory with the starter project files as a volume in the directory `/home/packt/ml4t` inside the container (path format changes on Windows). 
   - `appliedai/manning:liveproject`: from the Docker Hub account `appliedai`, pull the Docker image in the repository `manning` with the tag `starter` (only happens on first run) 
   - `--name liveproject`: assign the name `liveproject` to this container for easier to restart
   - `-p: 8888:888`: forward the port 8888 used by the `jupyter` server
   - `-e QUANDL_API_KEY=<yourkey>` set the environment variable `QUANDL_API_KEY` with the value of your key (that you need to fill in for `<your API key>`), and
   - start a `bash` terminal inside the container, resulting in a new command prompt for the user `manning`.
   To do this yourself:
   1. On Mac, open a Terminal; on Windows, a Powershell window,
   2. Navigate to the directory containing the [liveproject](https://github.com/stefan-jansen/automated-trading-strategy-using-machine-learning) starter code that you cloned in step 2.,
   3. In the root directory of the local version of the repo, run the following command, taking into account the different path formats required by Mac and Windows:
       - **Mac OS**: you can use the `pwd` command as a shell variable that contains the absolute path to the present working directory (and you could use `$QUANDL_API_KEY` if you created such an environment variable for `<your API key>` in the previous step):  
           ```docker
           docker run -it -v $(pwd):/home/manning/liveproject -p 8888:8888 -e QUANDL_API_KEY=<your API key> --name liveproject appliedai/manning:liveproject bash
           ```
      - **Windows**: enter the absolute path to the current directory **with forward slashes**, e.g. `C:/Users/stefan/Documents/automated-trading-strategy-using-machine-learning` instead of `C:\Users\stefan\Documents\automated-trading-strategy-using-machine-learning`, so that the command becomes (for this example):                                                                                                                                                                                                                                                                                                                                                                                                  
                                                                                                                                                                                                                                                                                                                                                                                                                     
        ```docker
        docker run -it -v C:/Users/stefan/Documents/automated-trading-strategy-using-machine-learning:/home/manning/liveproject -p 8888:8888 -e QUANDL_API_KEY=<yourkey> --name liveproject appliedai/manning:liveproject bash
        ```
   How to work with Docker:
   - Run `exit` from the container shell to exit and stop the container. 
   - To resume working, you can run `docker start -a -i liveproject` from Mac OS terminal or Windows Powershell in the root directory to restart the container and attach it to the host shell in interactive mode (see Docker docs for more detail).
   - To get information about the container, run `docker inspect liveproject`
   - To view memory use and other resource usage stats: run `docker stats`
   > The `installation` directory contains the Dockerfile used to create the image as well as the `conda` environment files; feel free to use/modify them as you see fit for this project.
6. Now you are running a shell inside the container and can access the `conda` environments.
    - Run `conda env list` to see that there are a `base`, `liveproject` (default), and a `liveproject-zipline` environment.
    - You can **switch to another `conda` environment** using the command `conda activate <env_name>` (you can also switch to another environment from a notebook as we'll explain below) 
    - However, before doing so the first time, you may get an error message suggesting you run `conda init bash`. 
        - After doing so, reload the shell with the command `source .bashrc`.
    - You can exit the container by just typing `exit` in the container shell, and continue your work using `docker start -a -i liveproject` to restart the container in interactive mode.
7. Next, we **launch the [juypter](https://jupyter.org/) server**. 
    - You can run notebooks using either the traditional or the more recent Jupyter Lab interface; both are available for all `conda` environments. Both commands are long due to several settings and we included shortcuts for both to make your life easier.
    - To run notebooks using the traditional interface, run the shorthand `nb` that executes the following command:
    ```bash
    jupyter notebook --ip 0.0.0.0 --no-browser
   ```
   - To run notebooks using the more recent `jupter lab` interface, run the shorthand `lab` that executes the following command:
    ```bash
    jupyter lab --ip 0.0.0.0 --no-browser
   ```
   - The options set/modify the jupyter server's behavior in the following ways:
        - `--ip 0.0.0.0`: listen on all IP addresses
        - `--no-browser`: Start notebook server without opening a web browser
    - After starting `jupyter` from the `base` environment, you can switch to another environment from the the notebook's `Kernel` menu due to the `nb_conda_kernels` package (see [docs](https://github.com/Anaconda-Platform/nb_conda_kernels)). 
    - The terminal will display a few messages and at the end indicate what to paste into your browser to access the jupyter server from the current working directory.
8. Our first post-installation task is to test Zipline. Create a `zipline_test.ipynb` notebook in this directory. 
    - Select the `liveproject-zipline` environment from the menu list under `Kernel` > `Change Kernel`.
    - To run Zipline backtests, we need to `ingest` data. See the [Beginner Tutorial](https://www.zipline.io/beginner-tutorial.html) for more information. The image has been configured to store the data in a `.zipline` directory in the working directory (from where you are running the container, which should be the root folder of the project starter code).
    - You can run the command `zipline ingest` from a notebook cell (where you need to prefix it with !) or the command line (after activating the `conda` environment). It creates the default `quandl` data bundle we'll be working with. 
   - You should see numerous messages as Zipline processes around 3,000 stock price series
   - The command `zipline bundles` displays the ingest history.
   - When running a backtest, you will likely encounter an [error](https://github.com/quantopian/zipline/issues/2517) because the current Zipline version requires a country code entry in the `exchanges` table of the `assets-7.sqlite` database where it stores the asset metadata. 
        - The linked issue describes how to address this by [opening the SQLite database](https://sqlitebrowser.org/dl/) and entering `US` in the `country_code` field of the `exchanges`.
         In practice, this looks as follows:
         1. Use the [SQLite Browser](https://sqlitebrowser.org/dl/) to open the file `assets-7.sqlite` in the directory containing your latest bundle download. The path will look like this (on Linux/Max OSX) if you ran the command as just described:  `~/machine-learning-for-trading/data/.zipline/data/quandl/2020-12-29T02;06;08.894865/`
         2. Select the table `exchanges` as outlined in the following screenshot:
         <p align="center">
         <img src="https://i.imgur.com/khq6gtX.png" title="Modifying QUANDL SQLite - Step 1" width="50%"/>
         </p>
         3. Insert the country code and save the result (you'll get a prompt when closing the program):
         <p align="center">
         <img src="https://i.imgur.com/mtdiylk.png" title="Modifying QUANDL SQLite - Step 1" width="50%"/>
         </p>
         That's all. Unfortunately, you need to repeat this everytime you run `zipline ingest`.
        - Alternatively, you could pass the argument `domain = EquityCalendarDomain('??', trading_calendar.name)` when instantiating a `zipline.Pipeline` object in part 4, where `trading_calendar = get_calendar('NYSE')`. I'd suggest modifying the table, though, as it's fairly straightforward and solves this issue for good (well, until the next `zipline ingest`).
   - Now you can implement the [Dual Moving Average Cross-Over example](https://www.zipline.io/beginner-tutorial.html#access-to-previous-prices-using-history) from the Zipline tutorial in the following notebook cells to familiarize yourself with the Zipline interface. It should display the backtest results at the end.
9. Finally, after logging into Quandl, download the [Quandl Wiki Stock data](https://www.quandl.com/tables/WIKIP/WIKI-PRICES/export) and unzip into the root directory of the project starter files as `us_stocks.csv`. Then, create a second notebook to simplify the data as follows (see pandas [docs](https://pandas.pydata.org/docs/) as necessary), using the `liveproject` environment: 
    - Convert the `date` column to `datetime` format
    - Select stock price data only from 2000 onwards
    - Set `ticker` and `date` as index
    - Keep only the adjusted open, low, high, close, and volume (OHLCV) prices, and rename by removing the `adj_` prefix
    - Store in HDF5 format for fast access.  
   
## Importance to project
- We need our programming environment up and running to proceed with our project; Docker helps us avoid some trickier and OS-dependent installation issues.
- It's good to know that things work, so we run a quick test that avoids running into problems later.
- Finally, we need data to build an ML model and a trading strategy. Now we have consistent data that we can use in the notebook to engineer features and design a predictive model, as well as run a Zipline backtest on.

## Resources

- For additional detail for a very similar workflow see installation instructions for my book [here](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/installation).
- [Docker - Getting Started Guide](https://docs.docker.com/get-started/)
- [Docker Cheatsheet](https://raw.githubusercontent.com/sangam14/dockercheatsheets/master/dockercheatsheet8.png)
- [A comprehensive introduction to Docker, Virtual Machines, and Containers](https://www.freecodecamp.org/news/comprehensive-introductory-guide-to-docker-vms-and-containers-4e42a13ee103/)
- [Zipline Beginner Tutorial](https://www.zipline.io/beginner-tutorial.html#my-first-algorithm)

