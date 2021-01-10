# My Solutions - Notes

[ML for Trading](https://github.com/stefan-jansen/machine-learning-for-trading) by [Stefan Jansen](https://github.com/stefan-jansen).


```bash
# NOTE: run WITHOUT --rm so that I can re-start the container
docker run -it -v $(pwd):/home/manning/liveproject -p 8888:8888 -e QUANDL_API_KEY=<QUANDL_API_KEY> --name liveproject appliedai/manning:liveproject bash

# Continue your work using
docker start -a -i liveproject

jupyter lab --ip 0.0.0.0 --no-browser
# --ip 0.0.0.0: Listen on all IP addresses.
# --no-browser: Start notebook server without opening a web browser.
```
