# heroku-buildpack-firefox

Installs headless Firefox + geckodriver for Selenium (Python) on Heroku-22/24.

## Setup

```sh
heroku buildpacks:add --index 1 https://github.com/<you>/heroku-buildpack-firefox
heroku buildpacks:add heroku/python
# optional pins
heroku config:set FIREFOX_VERSION=131.0 GECKODRIVER_VERSION=0.36.0
```

Runtime env: `FIREFOX_BIN`, `GECKODRIVER_PATH`, `MOZ_HEADLESS=1`, both binaries on `PATH`.

## Python

```python
import os
from selenium import webdriver
from selenium.webdriver.firefox.options import Options
from selenium.webdriver.firefox.service import Service

opts = Options()
opts.add_argument("-headless")
opts.binary_location = os.environ["FIREFOX_BIN"]

driver = webdriver.Firefox(options=opts, service=Service(os.environ["GECKODRIVER_PATH"]))
driver.get("https://example.com")
print(driver.title)
driver.quit()
```

## Missing shared libs?

If Firefox fails with `error while loading shared libraries`, add
`heroku-community/apt` before this buildpack with an `Aptfile` containing e.g.:

```
libdbus-glib-1-2
libgtk-3-0
libasound2
```

Dyno memory: Firefox wants ~300–500 MB; use at least a Standard-2X dyno for real workloads.
