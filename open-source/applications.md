# Applications

## What is Applications?

**Applications** is a new feature of dstack that allows Python developers to quickly build, run, and share interactive data applications. This new feature is coming with the new version of dstack \(`v0.6`\) which is to be released within December 2020/January 2021. A limited preview of this feature can be tried already now. Read on this document to learn how this feature works, and how to try it.

### Installation

In order to try the new feature, you have to install the latest "preview" version of the `dstack` package. This can be done by the following command:

```bash
pip install -i https://test.pypi.org/simple/ dstack -U
```

Once the package is installed, make sure to run the server locally:

```bash
dstack server start
```

Now, that you've started the dstack server, you can use the `dstack`'s API from a Python script or a Jupyter notebook to build applications and push them to the dstack server.

## Creating a Minimal Applicarion

Here's an example of a very simple application. In this application, the user is prompted to choose one of the stock symbols to see its latest market data in a form of a Candlestick chart:

```python
from datetime import datetime, timedelta

import dstack.controls as ctrl
import dstack as ds
import plotly.graph_objects as go
import pandas_datareader.data as web


def get_chart(symbols: ctrl.ComboBox):
    start = datetime.today() - timedelta(days=30)
    end = datetime.today()
    df = web.DataReader(symbols.value(), 'yahoo', start, end)
    fig = go.Figure(
        data=[go.Candlestick(x=df.index, open=df['Open'], high=df['High'], low=df['Low'], close=df['Close'])])
    return fig


app = ds.app(get_chart, symbols=ctrl.ComboBox(["FB", "AMZN", "AAPL", "NFLX", "GOOG"], require_apply=False))

result = ds.push("faang", app)
print(result.url)
```

Let's take a closer look at this code and describe every step.

**Application output**

First, we define the function `get_chart` that takes the argument `symbols` of the type `ctrl.ComboBox`. The argument represents a combo box in which the user selects a stock symbol \(e.g. `"FB"`, `"AMZN"`, etc\). Based on the selected symbol \(see `symbols.value()`\), the function fetches the market data for the corresponding stock \(from the Yahoo Financial Services – using the `pandas_datareader` package\), makes a Candlestick chart \(using the `plotly` package\), and returns the resulting figure.

Here's the code:

```python
def get_chart(symbols: ctrl.ComboBox):
    start = datetime.today() - timedelta(days=30)
    end = datetime.today()
    df = web.DataReader(symbols.value(), 'yahoo', start, end)
    fig = go.Figure(
        data=[go.Candlestick(x=df.index, open=df['Open'], high=df['High'], low=df['Low'], close=df['Close'])])
    return fig
```

**User controls and application**

Once the function is defined, we call the function `dstack.app` where we pass our function that produces the output, and assign an instance of `ctrl.ComboBox` into the argument named `symbols`. This call creates an instance of an application. The application contains information on the function that produces the visualizations and binds an instance `ctrl.ComboBox`to the name of the argument of the function \(`symbols`\).

```python
app = ds.app(get_chart, symbols=ctrl.ComboBox(["FB", "AMZN", "AAPL", "NFLX", "GOOG"], require_apply=False))
```

**Deploy application**

Finally, we deploy our application to the dstack server by using the function `dstack.push`. The arguments of the call are `"faang"` – the name of the application, and `app` – the instance of our application. If successful, this call returns a push result that has an attribute `url`. This is the URL of the deployed application.

```python
result = ds.push("faang", app)
print(result.url)
```

If we click the URL, we'll see the following application:

### 

