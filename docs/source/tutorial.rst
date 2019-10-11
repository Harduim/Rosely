Tutorial
========

This tutorial covers basic usage of the ``Rosely`` package including
loading of data, calculatiion of wind statistics, and wind rose plotting
customizations.

.. code:: ipython3

    import pandas as pd
    import plotly.express as px
    from rosely import WindRose

Read input data
---------------

``Rosely`` requires wind data to first be loaded into a
``pandas.DataFrame`` object, also wind direction should be in degrees,
i.e. in [0, 360].

The example data used in this tutorial is a modified version of 30
minute data that was originally from the “Twitchell Alfalfa” AmeriFlux
eddy covariance flux tower site in the Sacramento–San Joaquin River
Delta in California. The site is located in alfalfa fields and exhibits
a mild Mediterranean climate with dry and hot summers, for more
information on this site click
`here <https://ameriflux.lbl.gov/sites/siteinfo/US-Tw3>`__.

.. code:: ipython3

    df = pd.read_csv('test_data.csv', index_col='date', parse_dates=True)
    df[['ws','wd']].head()




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>ws</th>
          <th>wd</th>
        </tr>
        <tr>
          <th>date</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>2013-05-24 12:30:00</td>
          <td>3.352754</td>
          <td>236.625093</td>
        </tr>
        <tr>
          <td>2013-05-24 13:00:00</td>
          <td>3.882154</td>
          <td>243.971055</td>
        </tr>
        <tr>
          <td>2013-05-24 13:30:00</td>
          <td>4.646089</td>
          <td>238.620934</td>
        </tr>
        <tr>
          <td>2013-05-24 14:00:00</td>
          <td>5.048825</td>
          <td>247.868815</td>
        </tr>
        <tr>
          <td>2013-05-24 14:30:00</td>
          <td>5.302946</td>
          <td>250.930258</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: ipython3

    # or another view of the summary statistics of wind data
    df[['ws','wd']].describe()




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>ws</th>
          <th>wd</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>count</td>
          <td>84988.000000</td>
          <td>84988.000000</td>
        </tr>
        <tr>
          <td>mean</td>
          <td>3.118813</td>
          <td>233.210960</td>
        </tr>
        <tr>
          <td>std</td>
          <td>2.032425</td>
          <td>84.893918</td>
        </tr>
        <tr>
          <td>min</td>
          <td>0.010876</td>
          <td>0.003150</td>
        </tr>
        <tr>
          <td>25%</td>
          <td>1.442373</td>
          <td>220.401528</td>
        </tr>
        <tr>
          <td>50%</td>
          <td>2.731378</td>
          <td>255.568402</td>
        </tr>
        <tr>
          <td>75%</td>
          <td>4.517145</td>
          <td>272.190239</td>
        </tr>
        <tr>
          <td>max</td>
          <td>14.733296</td>
          <td>359.997582</td>
        </tr>
      </tbody>
    </table>
    </div>



Create a ``WindRose`` instance
------------------------------

Using the loaded wind speed and direction data within a
``pandas.DataFrame`` we can initialize a ``Rosely.WindRose`` object
which provides simple methods for generating interactive wind rose
diagrams.

.. code:: ipython3

    WR = WindRose(df)

Alternatively the dataframe can be later assigned to a ``WindRose``
object,

.. code:: ipython3

    WR = WindRose()
    WR.df = df

Calculate wind statistics
-------------------------

A wind rose diagram is essentially a stacked histogram that is binned by
wind speed and freqeuncy for a set of wind directions. These
calculations are accomplished by the ``WindRose.calc_stats`` method
which allows for changing the number of default wind speed bins (equally
spaced) and whether or not the frequency is normalized to sum to 100 or
it is just the actual frequency of wind occurences (counts) in a certain
direction and speed bin.

By default the freqeuncy is normalized and the number of wind speed bins
is 9:

.. code:: ipython3

    WR.calc_stats()

To view the results of the wind statistics that will be used for the
wind rose later, view the ``WindRose.wind_df`` which is created after
running ``WindRose.calc_stats``:

.. code:: ipython3

    # view all statistics for winds coming from the North
    WR.wind_df.loc[WR.wind_df.direction=='N']




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>direction</th>
          <th>speed</th>
          <th>frequency</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>0</td>
          <td>N</td>
          <td>-0.00-1.65</td>
          <td>1.36</td>
        </tr>
        <tr>
          <td>1</td>
          <td>N</td>
          <td>1.65-3.28</td>
          <td>0.66</td>
        </tr>
        <tr>
          <td>2</td>
          <td>N</td>
          <td>3.28-4.92</td>
          <td>0.24</td>
        </tr>
        <tr>
          <td>3</td>
          <td>N</td>
          <td>4.92-6.55</td>
          <td>0.07</td>
        </tr>
        <tr>
          <td>4</td>
          <td>N</td>
          <td>6.55-8.19</td>
          <td>0.01</td>
        </tr>
        <tr>
          <td>5</td>
          <td>N</td>
          <td>8.19-9.83</td>
          <td>0.01</td>
        </tr>
        <tr>
          <td>6</td>
          <td>N</td>
          <td>9.83-11.46</td>
          <td>0.00</td>
        </tr>
        <tr>
          <td>184</td>
          <td>N</td>
          <td>-0.00-1.65</td>
          <td>1.32</td>
        </tr>
        <tr>
          <td>185</td>
          <td>N</td>
          <td>1.65-3.28</td>
          <td>1.19</td>
        </tr>
        <tr>
          <td>186</td>
          <td>N</td>
          <td>3.28-4.92</td>
          <td>0.59</td>
        </tr>
        <tr>
          <td>187</td>
          <td>N</td>
          <td>4.92-6.55</td>
          <td>0.27</td>
        </tr>
        <tr>
          <td>188</td>
          <td>N</td>
          <td>6.55-8.19</td>
          <td>0.15</td>
        </tr>
        <tr>
          <td>189</td>
          <td>N</td>
          <td>8.19-9.83</td>
          <td>0.06</td>
        </tr>
        <tr>
          <td>190</td>
          <td>N</td>
          <td>9.83-11.46</td>
          <td>0.04</td>
        </tr>
        <tr>
          <td>191</td>
          <td>N</td>
          <td>11.46-13.10</td>
          <td>0.01</td>
        </tr>
      </tbody>
    </table>
    </div>



**Note:** the winds speed bins in a certain direction may appear to be
duplicated above but they are not, what is happening is that
``WindRose.calc_stats`` bins each direction on a 16 point compass twice
for 11.25 degrees sections on both sides of the compass azimuth. So for
North there are two internal azimuth bins: from 348.75-360 degrees and
from 0-11.25 degrees. If you wanted to see the summed Northerly winds
frequencies within the 9 speed bins you could run:

.. code:: ipython3

    WR.wind_df.groupby(['direction','speed']).sum().loc['N']




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>frequency</th>
        </tr>
        <tr>
          <th>speed</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>-0.00-1.65</td>
          <td>2.68</td>
        </tr>
        <tr>
          <td>1.65-3.28</td>
          <td>1.85</td>
        </tr>
        <tr>
          <td>3.28-4.92</td>
          <td>0.83</td>
        </tr>
        <tr>
          <td>4.92-6.55</td>
          <td>0.34</td>
        </tr>
        <tr>
          <td>6.55-8.19</td>
          <td>0.16</td>
        </tr>
        <tr>
          <td>8.19-9.83</td>
          <td>0.07</td>
        </tr>
        <tr>
          <td>9.83-11.46</td>
          <td>0.04</td>
        </tr>
        <tr>
          <td>11.46-13.10</td>
          <td>0.01</td>
        </tr>
        <tr>
          <td>13.10-14.73</td>
          <td>NaN</td>
        </tr>
      </tbody>
    </table>
    </div>



Here is an example of not normalizing the freqeuncy (using raw counts
instead) and using 6 instead of 9 bins for speed. This example shows the
same grouped output for Northerly winds,

.. code:: ipython3

    WR.calc_stats(normed=False, bins=6)
    WR.wind_df.groupby(['direction','speed']).sum().loc['N']




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>frequency</th>
        </tr>
        <tr>
          <th>speed</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>-0.00-2.46</td>
          <td>3318.0</td>
        </tr>
        <tr>
          <td>2.46-4.92</td>
          <td>1232.0</td>
        </tr>
        <tr>
          <td>4.92-7.37</td>
          <td>366.0</td>
        </tr>
        <tr>
          <td>7.37-9.83</td>
          <td>121.0</td>
        </tr>
        <tr>
          <td>9.83-12.28</td>
          <td>38.0</td>
        </tr>
        <tr>
          <td>12.28-14.73</td>
          <td>NaN</td>
        </tr>
      </tbody>
    </table>
    </div>



Lastly, if the wind speed and wind direction columns in the dataframe
assigned to the ``WindRose`` object are not named ‘ws’ and ‘wd’
respectively, instead of renaming them ahead of time or inplace, you may
pass a dictionary that maps their names to the ``WindRose.calc_stats``
method. For example, lets purposely change the names in our input
dataframe to ‘wind_speed’ and ‘direction’:

.. code:: ipython3

    tmp_df = df[['ws','wd']]
    tmp_df.columns = ['wind_speed', 'direction']
    tmp_df.head()




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>wind_speed</th>
          <th>direction</th>
        </tr>
        <tr>
          <th>date</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>2013-05-24 12:30:00</td>
          <td>3.352754</td>
          <td>236.625093</td>
        </tr>
        <tr>
          <td>2013-05-24 13:00:00</td>
          <td>3.882154</td>
          <td>243.971055</td>
        </tr>
        <tr>
          <td>2013-05-24 13:30:00</td>
          <td>4.646089</td>
          <td>238.620934</td>
        </tr>
        <tr>
          <td>2013-05-24 14:00:00</td>
          <td>5.048825</td>
          <td>247.868815</td>
        </tr>
        <tr>
          <td>2013-05-24 14:30:00</td>
          <td>5.302946</td>
          <td>250.930258</td>
        </tr>
      </tbody>
    </table>
    </div>



.. code:: ipython3

    # and now reassign with this differently named dataframe to demonstrate
    WR.df = tmp_df
    # create renaming dictionary
    names = {
        'wind_speed':'ws',
        'direction': 'wd'
    }
    WR.calc_stats(normed=False, bins=6, variable_names=names)
    WR.wind_df.groupby(['direction','speed']).sum().loc['N']




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>frequency</th>
        </tr>
        <tr>
          <th>speed</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>-0.00-2.46</td>
          <td>3318.0</td>
        </tr>
        <tr>
          <td>2.46-4.92</td>
          <td>1232.0</td>
        </tr>
        <tr>
          <td>4.92-7.37</td>
          <td>366.0</td>
        </tr>
        <tr>
          <td>7.37-9.83</td>
          <td>121.0</td>
        </tr>
        <tr>
          <td>9.83-12.28</td>
          <td>38.0</td>
        </tr>
        <tr>
          <td>12.28-14.73</td>
          <td>NaN</td>
        </tr>
      </tbody>
    </table>
    </div>



The same results were achieved as above, however the column names used
for initial assignment are retained by the ``WindRose.df`` property:

.. code:: ipython3

    WR.df.head()




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>wind_speed</th>
          <th>direction</th>
        </tr>
        <tr>
          <th>date</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>2013-05-24 12:30:00</td>
          <td>3.352754</td>
          <td>236.625093</td>
        </tr>
        <tr>
          <td>2013-05-24 13:00:00</td>
          <td>3.882154</td>
          <td>243.971055</td>
        </tr>
        <tr>
          <td>2013-05-24 13:30:00</td>
          <td>4.646089</td>
          <td>238.620934</td>
        </tr>
        <tr>
          <td>2013-05-24 14:00:00</td>
          <td>5.048825</td>
          <td>247.868815</td>
        </tr>
        <tr>
          <td>2013-05-24 14:30:00</td>
          <td>5.302946</td>
          <td>250.930258</td>
        </tr>
      </tbody>
    </table>
    </div>



**Tip:** In this tutorial the full dataset of 30 minute windspeed was
used to create the statistics (above) and the diagrams (below), in
practice it may be important to view wind speed / direction during
certain time periods like day or night, or summer/winter seasons. This
is one of the main reasons for using ``pandas.DataFrame`` objects- they
have many tools for time series analysis, particularly temporal
aggregation and resampling. If you wanted to view the wind
statistics/plot for this site during day times defined (not quite
accurately) as 8:00 AM to 8:00 PM it is as simple as this:

.. code:: ipython3

    # reassign the wind data but sliced just for day hours we want
    WR.df = df[['ws','wd']].between_time('8:00', '16:00')
    # calculate the wind statistics again
    WR.calc_stats(normed=False, bins=6)
    WR.wind_df.groupby(['direction','speed']).sum().loc['N']




.. raw:: html

    <div>
    <style scoped>
        .dataframe tbody tr th:only-of-type {
            vertical-align: middle;
        }
    
        .dataframe tbody tr th {
            vertical-align: top;
        }
    
        .dataframe thead th {
            text-align: right;
        }
    </style>
    <table border="1" class="dataframe">
      <thead>
        <tr style="text-align: right;">
          <th></th>
          <th>frequency</th>
        </tr>
        <tr>
          <th>speed</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>0.03-2.49</td>
          <td>1234.0</td>
        </tr>
        <tr>
          <td>2.49-4.94</td>
          <td>966.0</td>
        </tr>
        <tr>
          <td>4.94-7.39</td>
          <td>308.0</td>
        </tr>
        <tr>
          <td>7.39-9.84</td>
          <td>103.0</td>
        </tr>
        <tr>
          <td>9.84-12.29</td>
          <td>35.0</td>
        </tr>
        <tr>
          <td>12.29-14.73</td>
          <td>NaN</td>
        </tr>
      </tbody>
    </table>
    </div>



Generate wind rose diagrams
---------------------------

The main purpose of ``Rosely`` is to simplyfy the generation of
beautiful, interactive wind rose diagrams by using
``plotly.express.bar_polar`` charts and ``pandas``. Once a ``WindRose``
object has been created and has been assigned a ``pandas.DataFrame``
with wind speed and wind direction you can skip calculating statistics
(falls back on default parameters for statistics) and jump right to
creating a wind rose diagram. For example:

.. code:: ipython3

    # create a new WindRose object from our example data with 'ws' and 'wd' columns
    WR = WindRose(df)
    WR.plot()


.. parsed-literal::

    Wind speed and direction statistics have not been calculated, Calculating them now using default parameters.


The two lines above saved the plot with default parameters (9 speed
bins) normalized frequency, and default ``Rosely`` color schemes to the
current working directory named ‘windrose.html’.

To view the default plot without saving,

.. code:: ipython3

    # try zooming, clicking on legend, etc.
    WR.plot(output_type='show')



.. raw:: html

            <script type="text/javascript">
            window.PlotlyConfig = {MathJaxConfig: 'local'};
            if (window.MathJax) {MathJax.Hub.Config({SVG: {font: "STIX-Web"}});}
            if (typeof require !== 'undefined') {
            require.undef("plotly");
            define('plotly', function(require, exports, module) {
                /**
    * plotly.js v1.49.4
    * Copyright 2012-2019, Plotly, Inc.
    * All rights reserved.
    * Licensed under the MIT license
    */
            });
            require(['plotly'], function(Plotly) {
                window._Plotly = Plotly;
            });
            }
            </script>




.. raw:: html

    <div>
    
    
                <div id="d6bd3d18-ce6b-49af-bcf4-441efbcc22a3" class="plotly-graph-div" style="height:600px; width:100%;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("d6bd3d18-ce6b-49af-bcf4-441efbcc22a3")) {
                        Plotly.newPlot(
                            'd6bd3d18-ce6b-49af-bcf4-441efbcc22a3',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-1.65<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-1.65", "marker": {"color": "#f0f921"}, "name": "speed=-0.00-1.65", "r": [1.36, 1.23, 1.04, 0.88, 0.71, 0.67, 0.65, 0.66, 0.68, 0.7, 0.69, 0.65, 0.67, 0.69, 0.74, 0.6, 0.57, 0.66, 0.7, 0.86, 1.09, 1.41, 1.65, 1.51, 1.17, 0.97, 0.88, 0.91, 0.92, 1.13, 1.26, 1.32], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=1.65-3.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=1.65-3.28", "marker": {"color": "#fdca26"}, "name": "speed=1.65-3.28", "r": [0.66, 0.4, 0.31, 0.19, 0.15, 0.14, 0.1, 0.12, 0.1, 0.16, 0.25, 0.4, 0.56, 0.58, 0.45, 0.27, 0.29, 0.29, 0.37, 0.63, 1.33, 2.9, 4.15, 3.95, 2.48, 1.42, 0.81, 0.7, 0.77, 1.14, 1.36, 1.19], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=3.28-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=3.28-4.92", "marker": {"color": "#fb9f3a"}, "name": "speed=3.28-4.92", "r": [0.24, 0.08, 0.03, 0.01, 0.01, 0.01, 0.0, 0.01, 0.01, 0.03, 0.06, 0.17, 0.41, 0.46, 0.2, 0.1, 0.08, 0.06, 0.09, 0.15, 0.6, 2.22, 5.6, 6.23, 2.0, 0.39, 0.13, 0.15, 0.26, 0.56, 0.71, 0.59], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-6.55<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-6.55", "marker": {"color": "#ed7953"}, "name": "speed=4.92-6.55", "r": [0.07, 0.01, 0.0, 0.0, 0.0, 0.03, 0.11, 0.3, 0.27, 0.07, 0.02, 0.02, 0.04, 0.04, 0.08, 0.26, 1.59, 4.41, 4.07, 0.94, 0.08, 0.01, 0.03, 0.15, 0.36, 0.5, 0.27], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=6.55-8.19<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=6.55-8.19", "marker": {"color": "#d8576b"}, "name": "speed=6.55-8.19", "r": [0.01, 0.0, 0.0, 0.0, 0.01, 0.05, 0.17, 0.11, 0.01, 0.0, 0.0, 0.01, 0.01, 0.02, 0.09, 0.52, 1.66, 1.26, 0.14, 0.01, 0.0, 0.0, 0.04, 0.24, 0.38, 0.15], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=8.19-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=8.19-9.83", "marker": {"color": "#bd3786"}, "name": "speed=8.19-9.83", "r": [0.01, 0.01, 0.0, 0.01, 0.09, 0.07, 0.01, 0.0, 0.0, 0.0, 0.05, 0.11, 0.31, 0.16, 0.01, 0.01, 0.08, 0.18, 0.06], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-11.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-11.46", "marker": {"color": "#9c179e"}, "name": "speed=9.83-11.46", "r": [0.0, 0.0, 0.01, 0.04, 0.03, 0.0, 0.0, 0.0, 0.0, 0.02, 0.01, 0.0, 0.01, 0.05, 0.04], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "SW", "WSW", "WSW", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=11.46-13.10<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=11.46-13.10", "marker": {"color": "#7201a8"}, "name": "speed=11.46-13.10", "r": [0.01, 0.02, 0.01, 0.0, 0.02, 0.0, 0.01], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "WSW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=13.10-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=13.10-14.73", "marker": {"color": "#46039f"}, "name": "speed=13.10-14.73", "r": [0.0, 0.01], "showlegend": true, "subplot": "polar", "theta": ["SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "margin": {"t": 60}, "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"data": {"bar": [{"error_x": {"color": "#f2f5fa"}, "error_y": {"color": "#f2f5fa"}, "marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "baxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"line": {"color": "#283442"}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"line": {"color": "#283442"}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#506784"}, "line": {"color": "rgb(17,17,17)"}}, "header": {"fill": {"color": "#2a3f5f"}, "line": {"color": "rgb(17,17,17)"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#f2f5fa", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#f2f5fa"}, "geo": {"bgcolor": "rgb(17,17,17)", "lakecolor": "rgb(17,17,17)", "landcolor": "rgb(17,17,17)", "showlakes": true, "showland": true, "subunitcolor": "#506784"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "dark"}, "paper_bgcolor": "rgb(17,17,17)", "plot_bgcolor": "rgb(17,17,17)", "polar": {"angularaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "radialaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "yaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "zaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}}, "shapedefaults": {"line": {"color": "#f2f5fa"}}, "sliderdefaults": {"bgcolor": "#C8D4E3", "bordercolor": "rgb(17,17,17)", "borderwidth": 1, "tickwidth": 0}, "ternary": {"aaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "baxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "caxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "title": {"x": 0.05}, "updatemenudefaults": {"bgcolor": "#506784", "borderwidth": 0}, "xaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}}}},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('d6bd3d18-ce6b-49af-bcf4-441efbcc22a3');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


Notice that these plots used the default statistics parameters, to use
other options be sure to call ``WindRose.calc_stats`` before
``WindRose.plot``. E.g. if we wanted 6 equally spaced bins with
freqeuncies represented as counts as opposed to percentages,

.. code:: ipython3

    WR.calc_stats(normed=False, bins=6)
    WR.plot(output_type='show')



.. raw:: html

    <div>
    
    
                <div id="d0555dd1-5824-4514-b638-3b7c851b9c98" class="plotly-graph-div" style="height:600px; width:100%;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("d0555dd1-5824-4514-b638-3b7c851b9c98")) {
                        Plotly.newPlot(
                            'd0555dd1-5824-4514-b638-3b7c851b9c98',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-2.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-2.46", "marker": {"color": "#f0f921"}, "name": "speed=-0.00-2.46", "r": [1554.0, 1303.0, 1086.0, 880.0, 722.0, 664.0, 625.0, 647.0, 650.0, 696.0, 747.0, 750.0, 824.0, 854.0, 844.0, 671.0, 647.0, 741.0, 801.0, 1048.0, 1566.0, 2481.0, 2981.0, 2689.0, 2016.0, 1539.0, 1206.0, 1154.0, 1188.0, 1541.0, 1716.0, 1764.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=2.46-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=2.46-4.92", "marker": {"color": "#fdca26"}, "name": "speed=2.46-4.92", "r": [365.0, 156.0, 86.0, 35.0, 21.0, 29.0, 17.0, 24.0, 23.0, 57.0, 98.0, 294.0, 578.0, 615.0, 335.0, 151.0, 145.0, 114.0, 184.0, 343.0, 999.0, 3075.0, 6702.0, 7250.0, 2787.0, 827.0, 347.0, 343.0, 474.0, 872.0, 1110.0, 867.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-7.37<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-7.37", "marker": {"color": "#fb9f3a"}, "name": "speed=4.92-7.37", "r": [68.0, 10.0, 1.0, 1.0, 3.0, 30.0, 119.0, 330.0, 280.0, 64.0, 23.0, 21.0, 40.0, 38.0, 74.0, 267.0, 1657.0, 4688.0, 4225.0, 884.0, 74.0, 12.0, 27.0, 150.0, 435.0, 608.0, 298.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=7.37-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=7.37-9.83", "marker": {"color": "#ed7953"}, "name": "speed=7.37-9.83", "r": [12.0, 5.0, 4.0, 1.0, 29.0, 139.0, 103.0, 9.0, 2.0, 2.0, 7.0, 6.0, 72.0, 233.0, 737.0, 442.0, 38.0, 1.0, 1.0, 18.0, 147.0, 288.0, 109.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-12.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-12.28", "marker": {"color": "#d8576b"}, "name": "speed=9.83-12.28", "r": [1.0, 1.0, 7.0, 46.0, 32.0, 2.0, 1.0, 3.0, 3.0, 14.0, 8.0, 2.0, 21.0, 40.0, 37.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "SW", "WSW", "WSW", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=12.28-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=12.28-14.73", "marker": {"color": "#bd3786"}, "name": "speed=12.28-14.73", "r": [3.0, 4.0, 2.0, 11.0], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "margin": {"t": 60}, "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"data": {"bar": [{"error_x": {"color": "#f2f5fa"}, "error_y": {"color": "#f2f5fa"}, "marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "baxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"line": {"color": "#283442"}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"line": {"color": "#283442"}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#506784"}, "line": {"color": "rgb(17,17,17)"}}, "header": {"fill": {"color": "#2a3f5f"}, "line": {"color": "rgb(17,17,17)"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#f2f5fa", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#f2f5fa"}, "geo": {"bgcolor": "rgb(17,17,17)", "lakecolor": "rgb(17,17,17)", "landcolor": "rgb(17,17,17)", "showlakes": true, "showland": true, "subunitcolor": "#506784"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "dark"}, "paper_bgcolor": "rgb(17,17,17)", "plot_bgcolor": "rgb(17,17,17)", "polar": {"angularaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "radialaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "yaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "zaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}}, "shapedefaults": {"line": {"color": "#f2f5fa"}}, "sliderdefaults": {"bgcolor": "#C8D4E3", "bordercolor": "rgb(17,17,17)", "borderwidth": 1, "tickwidth": 0}, "ternary": {"aaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "baxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "caxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "title": {"x": 0.05}, "updatemenudefaults": {"bgcolor": "#506784", "borderwidth": 0}, "xaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}}}},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('d0555dd1-5824-4514-b638-3b7c851b9c98');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


**Hint:** Assign the path to save the output file if ``output_type`` =
‘save’ using the ``out_file`` keyword argument.

The third option that can be assigned to ``output_type`` other than
‘save’ and ‘show’ is ‘return’. When ``output_type='return`` the
``WindRose.plot`` method returns the plot figure for further
modification or integration in other workflows like adding it into a
group of subplots.

Here is an example use of the ‘return’ option that modifies the wind
rose after it’s creation by ``Rosely`` by changing the background color
and margins:

.. code:: ipython3

    fig = WR.plot(output_type='return')
    fig.update_layout(
        margin=dict(l=20, r=20, t=20, b=20),
        paper_bgcolor="grey",
    )
    fig.show()



.. raw:: html

    <div>
    
    
                <div id="abc19787-c702-4f44-ad2d-c6c1af62126f" class="plotly-graph-div" style="height:600px; width:100%;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("abc19787-c702-4f44-ad2d-c6c1af62126f")) {
                        Plotly.newPlot(
                            'abc19787-c702-4f44-ad2d-c6c1af62126f',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-2.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-2.46", "marker": {"color": "#f0f921"}, "name": "speed=-0.00-2.46", "r": [1554.0, 1303.0, 1086.0, 880.0, 722.0, 664.0, 625.0, 647.0, 650.0, 696.0, 747.0, 750.0, 824.0, 854.0, 844.0, 671.0, 647.0, 741.0, 801.0, 1048.0, 1566.0, 2481.0, 2981.0, 2689.0, 2016.0, 1539.0, 1206.0, 1154.0, 1188.0, 1541.0, 1716.0, 1764.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=2.46-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=2.46-4.92", "marker": {"color": "#fdca26"}, "name": "speed=2.46-4.92", "r": [365.0, 156.0, 86.0, 35.0, 21.0, 29.0, 17.0, 24.0, 23.0, 57.0, 98.0, 294.0, 578.0, 615.0, 335.0, 151.0, 145.0, 114.0, 184.0, 343.0, 999.0, 3075.0, 6702.0, 7250.0, 2787.0, 827.0, 347.0, 343.0, 474.0, 872.0, 1110.0, 867.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-7.37<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-7.37", "marker": {"color": "#fb9f3a"}, "name": "speed=4.92-7.37", "r": [68.0, 10.0, 1.0, 1.0, 3.0, 30.0, 119.0, 330.0, 280.0, 64.0, 23.0, 21.0, 40.0, 38.0, 74.0, 267.0, 1657.0, 4688.0, 4225.0, 884.0, 74.0, 12.0, 27.0, 150.0, 435.0, 608.0, 298.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=7.37-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=7.37-9.83", "marker": {"color": "#ed7953"}, "name": "speed=7.37-9.83", "r": [12.0, 5.0, 4.0, 1.0, 29.0, 139.0, 103.0, 9.0, 2.0, 2.0, 7.0, 6.0, 72.0, 233.0, 737.0, 442.0, 38.0, 1.0, 1.0, 18.0, 147.0, 288.0, 109.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-12.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-12.28", "marker": {"color": "#d8576b"}, "name": "speed=9.83-12.28", "r": [1.0, 1.0, 7.0, 46.0, 32.0, 2.0, 1.0, 3.0, 3.0, 14.0, 8.0, 2.0, 21.0, 40.0, 37.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "SW", "WSW", "WSW", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=12.28-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=12.28-14.73", "marker": {"color": "#bd3786"}, "name": "speed=12.28-14.73", "r": [3.0, 4.0, 2.0, 11.0], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "margin": {"b": 20, "l": 20, "r": 20, "t": 20}, "paper_bgcolor": "grey", "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"data": {"bar": [{"error_x": {"color": "#f2f5fa"}, "error_y": {"color": "#f2f5fa"}, "marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "baxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"line": {"color": "#283442"}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"line": {"color": "#283442"}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#506784"}, "line": {"color": "rgb(17,17,17)"}}, "header": {"fill": {"color": "#2a3f5f"}, "line": {"color": "rgb(17,17,17)"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#f2f5fa", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#f2f5fa"}, "geo": {"bgcolor": "rgb(17,17,17)", "lakecolor": "rgb(17,17,17)", "landcolor": "rgb(17,17,17)", "showlakes": true, "showland": true, "subunitcolor": "#506784"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "dark"}, "paper_bgcolor": "rgb(17,17,17)", "plot_bgcolor": "rgb(17,17,17)", "polar": {"angularaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "radialaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "yaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "zaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}}, "shapedefaults": {"line": {"color": "#f2f5fa"}}, "sliderdefaults": {"bgcolor": "#C8D4E3", "bordercolor": "rgb(17,17,17)", "borderwidth": 1, "tickwidth": 0}, "ternary": {"aaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "baxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "caxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "title": {"x": 0.05}, "updatemenudefaults": {"bgcolor": "#506784", "borderwidth": 0}, "xaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}}}},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('abc19787-c702-4f44-ad2d-c6c1af62126f');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


Easy wind rose customizations
-----------------------------

``Rosely`` makes it simple to experiment with different wind rose
statistcs options but also plot color schemes, this section of the
tutorial highlights some useful options to the ``WindRose.plot`` method
for doing the latter.

First off there are three important keyword arguments to
``WindRose.plot`` that control the color schemes (``colors``,
``template``, and ``colors_reversed``):

1. ``colors`` is the name of the ``Plotly`` sequential color swath or a
   list of your own RGB or Hex colors to passfor the stacked histograms
   (the first color in the list will be the most inner color on the
   diagram and them moving outwards towards higher wind speeds).
2. ``template``, this is the name of the ``Plotly`` template that
   defines the background color and other visual appearences. You may
   also pass a custom ``Plotly.py`` template object.
3. ``colors_reversed`` simply allows for the automatic reversal of color
   sequences which may be useful because some color swaths range from
   light to dark while others range from dark to light tones.

A list of all provided colors (hint hover over them to view the Hex or
RGB values themselves):

.. code:: ipython3

    px.colors.sequential.swatches()



.. raw:: html

    <div>
    
    
                <div id="ea619f8c-acef-4663-96f1-bffe6457692a" class="plotly-graph-div" style="height:2600px; width:100%;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("ea619f8c-acef-4663-96f1-bffe6457692a")) {
                        Plotly.newPlot(
                            'ea619f8c-acef-4663-96f1-bffe6457692a',
                            [{"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(237, 229, 207)", "rgb(224, 194, 162)", "rgb(211, 156, 131)", "rgb(193, 118, 111)", "rgb(166, 84, 97)", "rgb(129, 55, 83)", "rgb(84, 31, 63)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Brwnyl", "Brwnyl", "Brwnyl", "Brwnyl", "Brwnyl", "Brwnyl", "Brwnyl"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(75, 41, 145)", "rgb(135, 44, 162)", "rgb(192, 54, 157)", "rgb(234, 79, 136)", "rgb(250, 120, 118)", "rgb(246, 169, 122)", "rgb(237, 217, 163)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Agsunset", "Agsunset", "Agsunset", "Agsunset", "Agsunset", "Agsunset", "Agsunset"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(252, 222, 156)", "rgb(250, 164, 118)", "rgb(240, 116, 110)", "rgb(227, 79, 111)", "rgb(220, 57, 119)", "rgb(185, 37, 122)", "rgb(124, 29, 111)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Sunsetdark", "Sunsetdark", "Sunsetdark", "Sunsetdark", "Sunsetdark", "Sunsetdark", "Sunsetdark"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(243, 203, 211)", "rgb(234, 169, 189)", "rgb(221, 136, 172)", "rgb(202, 105, 157)", "rgb(177, 77, 142)", "rgb(145, 53, 125)", "rgb(108, 33, 103)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Magenta", "Magenta", "Magenta", "Magenta", "Magenta", "Magenta", "Magenta"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(243, 231, 155)", "rgb(250, 196, 132)", "rgb(248, 160, 126)", "rgb(235, 127, 134)", "rgb(206, 102, 147)", "rgb(160, 89, 160)", "rgb(92, 83, 165)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Sunset", "Sunset", "Sunset", "Sunset", "Sunset", "Sunset", "Sunset"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(249, 221, 218)", "rgb(242, 185, 196)", "rgb(229, 151, 185)", "rgb(206, 120, 179)", "rgb(173, 95, 173)", "rgb(131, 75, 160)", "rgb(87, 59, 136)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Purpor", "Purpor", "Purpor", "Purpor", "Purpor", "Purpor", "Purpor"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(243, 224, 247)", "rgb(228, 199, 241)", "rgb(209, 175, 232)", "rgb(185, 152, 221)", "rgb(159, 130, 206)", "rgb(130, 109, 186)", "rgb(99, 88, 159)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Purp", "Purp", "Purp", "Purp", "Purp", "Purp", "Purp"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(176, 242, 188)", "rgb(137, 232, 172)", "rgb(103, 219, 165)", "rgb(76, 200, 163)", "rgb(56, 178, 163)", "rgb(44, 152, 160)", "rgb(37, 125, 152)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Tealgrn", "Tealgrn", "Tealgrn", "Tealgrn", "Tealgrn", "Tealgrn", "Tealgrn"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(209, 238, 234)", "rgb(168, 219, 217)", "rgb(133, 196, 201)", "rgb(104, 171, 184)", "rgb(79, 144, 166)", "rgb(59, 115, 143)", "rgb(42, 86, 116)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Teal", "Teal", "Teal", "Teal", "Teal", "Teal", "Teal"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247, 254, 174)", "rgb(183, 230, 165)", "rgb(124, 203, 162)", "rgb(70, 174, 160)", "rgb(8, 144, 153)", "rgb(0, 113, 139)", "rgb(4, 82, 117)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Bluyl", "Bluyl", "Bluyl", "Bluyl", "Bluyl", "Bluyl", "Bluyl"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(36, 86, 104)", "rgb(15, 114, 121)", "rgb(13, 143, 129)", "rgb(57, 171, 126)", "rgb(110, 197, 116)", "rgb(169, 220, 103)", "rgb(237, 239, 93)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Aggrnyl", "Aggrnyl", "Aggrnyl", "Aggrnyl", "Aggrnyl", "Aggrnyl", "Aggrnyl"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(211, 242, 163)", "rgb(151, 225, 150)", "rgb(108, 192, 139)", "rgb(76, 155, 130)", "rgb(33, 122, 121)", "rgb(16, 89, 101)", "rgb(7, 64, 80)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Emrld", "Emrld", "Emrld", "Emrld", "Emrld", "Emrld", "Emrld"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(210, 251, 212)", "rgb(165, 219, 194)", "rgb(123, 188, 176)", "rgb(85, 156, 158)", "rgb(58, 124, 137)", "rgb(35, 93, 114)", "rgb(18, 63, 90)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Darkmint", "Darkmint", "Darkmint", "Darkmint", "Darkmint", "Darkmint", "Darkmint"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(196, 230, 195)", "rgb(150, 210, 164)", "rgb(109, 188, 144)", "rgb(77, 162, 132)", "rgb(54, 135, 122)", "rgb(38, 107, 110)", "rgb(29, 79, 96)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Blugrn", "Blugrn", "Blugrn", "Blugrn", "Blugrn", "Blugrn", "Blugrn"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(228, 241, 225)", "rgb(180, 217, 204)", "rgb(137, 192, 182)", "rgb(99, 166, 160)", "rgb(68, 140, 138)", "rgb(40, 114, 116)", "rgb(13, 88, 95)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Mint", "Mint", "Mint", "Mint", "Mint", "Mint", "Mint"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(254, 246, 181)", "rgb(255, 221, 154)", "rgb(255, 194, 133)", "rgb(255, 166, 121)", "rgb(250, 138, 118)", "rgb(241, 109, 122)", "rgb(225, 83, 131)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Pinkyl", "Pinkyl", "Pinkyl", "Pinkyl", "Pinkyl", "Pinkyl", "Pinkyl"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(253, 224, 197)", "rgb(250, 203, 166)", "rgb(248, 181, 139)", "rgb(245, 158, 114)", "rgb(242, 133, 93)", "rgb(239, 106, 76)", "rgb(235, 74, 64)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Peach", "Peach", "Peach", "Peach", "Peach", "Peach", "Peach"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(236, 218, 154)", "rgb(239, 196, 126)", "rgb(243, 173, 106)", "rgb(247, 148, 93)", "rgb(249, 123, 87)", "rgb(246, 99, 86)", "rgb(238, 77, 90)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Oryel", "Oryel", "Oryel", "Oryel", "Oryel", "Oryel", "Oryel"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(246, 210, 169)", "rgb(245, 183, 142)", "rgb(241, 156, 124)", "rgb(234, 129, 113)", "rgb(221, 104, 108)", "rgb(202, 82, 104)", "rgb(177, 63, 100)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Redor", "Redor", "Redor", "Redor", "Redor", "Redor", "Redor"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(251, 230, 197)", "rgb(245, 186, 152)", "rgb(238, 138, 130)", "rgb(220, 113, 118)", "rgb(200, 88, 108)", "rgb(156, 63, 93)", "rgb(112, 40, 74)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Burgyl", "Burgyl", "Burgyl", "Burgyl", "Burgyl", "Burgyl", "Burgyl"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255, 198, 196)", "rgb(244, 163, 168)", "rgb(227, 129, 145)", "rgb(204, 96, 125)", "rgb(173, 70, 108)", "rgb(139, 48, 88)", "rgb(103, 32, 68)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1], "y": ["Burg", "Burg", "Burg", "Burg", "Burg", "Burg", "Burg"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(254, 245, 244)", "rgb(222, 224, 210)", "rgb(189, 206, 181)", "rgb(153, 189, 156)", "rgb(110, 173, 138)", "rgb(65, 157, 129)", "rgb(25, 137, 125)", "rgb(18, 116, 117)", "rgb(25, 94, 106)", "rgb(28, 72, 93)", "rgb(25, 51, 80)", "rgb(20, 29, 67)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo", "tempo"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(241, 236, 236)", "rgb(230, 209, 203)", "rgb(221, 182, 170)", "rgb(213, 156, 137)", "rgb(205, 129, 103)", "rgb(196, 102, 73)", "rgb(186, 74, 47)", "rgb(172, 44, 36)", "rgb(149, 19, 39)", "rgb(120, 14, 40)", "rgb(89, 13, 31)", "rgb(60, 9, 17)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["amp", "amp", "amp", "amp", "amp", "amp", "amp", "amp", "amp", "amp", "amp", "amp"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(254, 252, 205)", "rgb(239, 225, 156)", "rgb(221, 201, 106)", "rgb(194, 182, 59)", "rgb(157, 167, 21)", "rgb(116, 153, 5)", "rgb(75, 138, 20)", "rgb(35, 121, 36)", "rgb(11, 100, 44)", "rgb(18, 78, 43)", "rgb(25, 56, 34)", "rgb(23, 35, 18)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["speed", "speed", "speed", "speed", "speed", "speed", "speed", "speed", "speed", "speed", "speed", "speed"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(253, 237, 176)", "rgb(250, 205, 145)", "rgb(246, 173, 119)", "rgb(240, 142, 98)", "rgb(231, 109, 84)", "rgb(216, 80, 83)", "rgb(195, 56, 90)", "rgb(168, 40, 96)", "rgb(138, 29, 99)", "rgb(107, 24, 93)", "rgb(76, 21, 80)", "rgb(47, 15, 61)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["matter", "matter", "matter", "matter", "matter", "matter", "matter", "matter", "matter", "matter", "matter", "matter"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(214, 249, 207)", "rgb(186, 228, 174)", "rgb(156, 209, 143)", "rgb(124, 191, 115)", "rgb(85, 174, 91)", "rgb(37, 157, 81)", "rgb(7, 138, 78)", "rgb(13, 117, 71)", "rgb(23, 95, 61)", "rgb(25, 75, 49)", "rgb(23, 55, 35)", "rgb(17, 36, 20)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["algae", "algae", "algae", "algae", "algae", "algae", "algae", "algae", "algae", "algae", "algae", "algae"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(230, 240, 240)", "rgb(191, 221, 229)", "rgb(156, 201, 226)", "rgb(129, 180, 227)", "rgb(115, 154, 228)", "rgb(117, 127, 221)", "rgb(120, 100, 202)", "rgb(119, 74, 175)", "rgb(113, 50, 141)", "rgb(100, 31, 104)", "rgb(80, 20, 66)", "rgb(54, 14, 36)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["dense", "dense", "dense", "dense", "dense", "dense", "dense", "dense", "dense", "dense", "dense", "dense"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(253, 253, 204)", "rgb(206, 236, 179)", "rgb(156, 219, 165)", "rgb(111, 201, 163)", "rgb(86, 177, 163)", "rgb(76, 153, 160)", "rgb(68, 130, 155)", "rgb(62, 108, 150)", "rgb(62, 82, 143)", "rgb(64, 60, 115)", "rgb(54, 43, 77)", "rgb(39, 26, 44)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["deep", "deep", "deep", "deep", "deep", "deep", "deep", "deep", "deep", "deep", "deep", "deep"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0, 0, 0)", "rgb(16, 16, 16)", "rgb(38, 38, 38)", "rgb(59, 59, 59)", "rgb(81, 80, 80)", "rgb(102, 101, 101)", "rgb(124, 123, 122)", "rgb(146, 146, 145)", "rgb(171, 171, 170)", "rgb(197, 197, 195)", "rgb(224, 224, 223)", "rgb(254, 254, 253)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["gray", "gray", "gray", "gray", "gray", "gray", "gray", "gray", "gray", "gray", "gray", "gray"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(3, 5, 18)", "rgb(25, 25, 51)", "rgb(44, 42, 87)", "rgb(58, 60, 125)", "rgb(62, 83, 160)", "rgb(62, 109, 178)", "rgb(72, 134, 187)", "rgb(89, 159, 196)", "rgb(114, 184, 205)", "rgb(149, 207, 216)", "rgb(192, 229, 232)", "rgb(234, 252, 253)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["ice", "ice", "ice", "ice", "ice", "ice", "ice", "ice", "ice", "ice", "ice", "ice"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(51, 19, 23)", "rgb(79, 28, 33)", "rgb(108, 36, 36)", "rgb(135, 47, 32)", "rgb(157, 66, 25)", "rgb(174, 88, 20)", "rgb(188, 111, 19)", "rgb(199, 137, 22)", "rgb(209, 164, 32)", "rgb(217, 192, 44)", "rgb(222, 222, 59)", "rgb(224, 253, 74)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["solar", "solar", "solar", "solar", "solar", "solar", "solar", "solar", "solar", "solar", "solar", "solar"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(41, 24, 107)", "rgb(42, 35, 160)", "rgb(15, 71, 153)", "rgb(18, 95, 142)", "rgb(38, 116, 137)", "rgb(53, 136, 136)", "rgb(65, 157, 133)", "rgb(81, 178, 124)", "rgb(111, 198, 107)", "rgb(160, 214, 91)", "rgb(212, 225, 112)", "rgb(253, 238, 153)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["haline", "haline", "haline", "haline", "haline", "haline", "haline", "haline", "haline", "haline", "haline", "haline"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(3, 35, 51)", "rgb(13, 48, 100)", "rgb(53, 50, 155)", "rgb(93, 62, 153)", "rgb(126, 77, 143)", "rgb(158, 89, 135)", "rgb(193, 100, 121)", "rgb(225, 113, 97)", "rgb(246, 139, 69)", "rgb(251, 173, 60)", "rgb(246, 211, 70)", "rgb(231, 250, 90)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal", "thermal"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(232, 245, 171)", "rgb(220, 219, 137)", "rgb(209, 193, 107)", "rgb(199, 168, 83)", "rgb(186, 143, 66)", "rgb(170, 121, 60)", "rgb(151, 103, 58)", "rgb(129, 87, 56)", "rgb(104, 72, 53)", "rgb(80, 59, 46)", "rgb(57, 45, 37)", "rgb(34, 30, 27)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid", "turbid"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,255,204)", "rgb(255,237,160)", "rgb(254,217,118)", "rgb(254,178,76)", "rgb(253,141,60)", "rgb(252,78,42)", "rgb(227,26,28)", "rgb(189,0,38)", "rgb(128,0,38)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd", "YlOrRd"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,255,229)", "rgb(255,247,188)", "rgb(254,227,145)", "rgb(254,196,79)", "rgb(254,153,41)", "rgb(236,112,20)", "rgb(204,76,2)", "rgb(153,52,4)", "rgb(102,37,6)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr", "YlOrBr"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,255,217)", "rgb(237,248,177)", "rgb(199,233,180)", "rgb(127,205,187)", "rgb(65,182,196)", "rgb(29,145,192)", "rgb(34,94,168)", "rgb(37,52,148)", "rgb(8,29,88)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu", "YlGnBu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,255,229)", "rgb(247,252,185)", "rgb(217,240,163)", "rgb(173,221,142)", "rgb(120,198,121)", "rgb(65,171,93)", "rgb(35,132,67)", "rgb(0,104,55)", "rgb(0,69,41)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["YlGn", "YlGn", "YlGn", "YlGn", "YlGn", "YlGn", "YlGn", "YlGn", "YlGn"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,245,240)", "rgb(254,224,210)", "rgb(252,187,161)", "rgb(252,146,114)", "rgb(251,106,74)", "rgb(239,59,44)", "rgb(203,24,29)", "rgb(165,15,21)", "rgb(103,0,13)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Reds", "Reds", "Reds", "Reds", "Reds", "Reds", "Reds", "Reds", "Reds"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,247,243)", "rgb(253,224,221)", "rgb(252,197,192)", "rgb(250,159,181)", "rgb(247,104,161)", "rgb(221,52,151)", "rgb(174,1,126)", "rgb(122,1,119)", "rgb(73,0,106)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["RdPu", "RdPu", "RdPu", "RdPu", "RdPu", "RdPu", "RdPu", "RdPu", "RdPu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(103,0,31)", "rgb(178,24,43)", "rgb(214,96,77)", "rgb(244,165,130)", "rgb(253,219,199)", "rgb(247,247,247)", "rgb(209,229,240)", "rgb(146,197,222)", "rgb(67,147,195)", "rgb(33,102,172)", "rgb(5,48,97)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu", "RdBu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(252,251,253)", "rgb(239,237,245)", "rgb(218,218,235)", "rgb(188,189,220)", "rgb(158,154,200)", "rgb(128,125,186)", "rgb(106,81,163)", "rgb(84,39,143)", "rgb(63,0,125)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Purples", "Purples", "Purples", "Purples", "Purples", "Purples", "Purples", "Purples", "Purples"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,244,249)", "rgb(231,225,239)", "rgb(212,185,218)", "rgb(201,148,199)", "rgb(223,101,176)", "rgb(231,41,138)", "rgb(206,18,86)", "rgb(152,0,67)", "rgb(103,0,31)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["PuRd", "PuRd", "PuRd", "PuRd", "PuRd", "PuRd", "PuRd", "PuRd", "PuRd"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,247,251)", "rgb(236,226,240)", "rgb(208,209,230)", "rgb(166,189,219)", "rgb(103,169,207)", "rgb(54,144,192)", "rgb(2,129,138)", "rgb(1,108,89)", "rgb(1,70,54)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn", "PuBuGn"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,247,251)", "rgb(236,231,242)", "rgb(208,209,230)", "rgb(166,189,219)", "rgb(116,169,207)", "rgb(54,144,192)", "rgb(5,112,176)", "rgb(4,90,141)", "rgb(2,56,88)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["PuBu", "PuBu", "PuBu", "PuBu", "PuBu", "PuBu", "PuBu", "PuBu", "PuBu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,245,235)", "rgb(254,230,206)", "rgb(253,208,162)", "rgb(253,174,107)", "rgb(253,141,60)", "rgb(241,105,19)", "rgb(217,72,1)", "rgb(166,54,3)", "rgb(127,39,4)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Oranges", "Oranges", "Oranges", "Oranges", "Oranges", "Oranges", "Oranges", "Oranges", "Oranges"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,247,236)", "rgb(254,232,200)", "rgb(253,212,158)", "rgb(253,187,132)", "rgb(252,141,89)", "rgb(239,101,72)", "rgb(215,48,31)", "rgb(179,0,0)", "rgb(127,0,0)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["OrRd", "OrRd", "OrRd", "OrRd", "OrRd", "OrRd", "OrRd", "OrRd", "OrRd"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(255,255,255)", "rgb(240,240,240)", "rgb(217,217,217)", "rgb(189,189,189)", "rgb(150,150,150)", "rgb(115,115,115)", "rgb(82,82,82)", "rgb(37,37,37)", "rgb(0,0,0)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Greys", "Greys", "Greys", "Greys", "Greys", "Greys", "Greys", "Greys", "Greys"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,252,245)", "rgb(229,245,224)", "rgb(199,233,192)", "rgb(161,217,155)", "rgb(116,196,118)", "rgb(65,171,93)", "rgb(35,139,69)", "rgb(0,109,44)", "rgb(0,68,27)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Greens", "Greens", "Greens", "Greens", "Greens", "Greens", "Greens", "Greens", "Greens"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,252,240)", "rgb(224,243,219)", "rgb(204,235,197)", "rgb(168,221,181)", "rgb(123,204,196)", "rgb(78,179,211)", "rgb(43,140,190)", "rgb(8,104,172)", "rgb(8,64,129)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["GnBu", "GnBu", "GnBu", "GnBu", "GnBu", "GnBu", "GnBu", "GnBu", "GnBu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,252,253)", "rgb(224,236,244)", "rgb(191,211,230)", "rgb(158,188,218)", "rgb(140,150,198)", "rgb(140,107,177)", "rgb(136,65,157)", "rgb(129,15,124)", "rgb(77,0,75)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["BuPu", "BuPu", "BuPu", "BuPu", "BuPu", "BuPu", "BuPu", "BuPu", "BuPu"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,252,253)", "rgb(229,245,249)", "rgb(204,236,230)", "rgb(153,216,201)", "rgb(102,194,164)", "rgb(65,174,118)", "rgb(35,139,69)", "rgb(0,109,44)", "rgb(0,68,27)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["BuGn", "BuGn", "BuGn", "BuGn", "BuGn", "BuGn", "BuGn", "BuGn", "BuGn"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(247,251,255)", "rgb(222,235,247)", "rgb(198,219,239)", "rgb(158,202,225)", "rgb(107,174,214)", "rgb(66,146,198)", "rgb(33,113,181)", "rgb(8,81,156)", "rgb(8,48,107)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Blues", "Blues", "Blues", "Blues", "Blues", "Blues", "Blues", "Blues", "Blues"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(150,0,90)", "rgb(0,0,200)", "rgb(0,25,255)", "rgb(0,152,255)", "rgb(44,255,150)", "rgb(151,255,0)", "rgb(255,234,0)", "rgb(255,111,0)", "rgb(255,0,0)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Rainbow", "Rainbow", "Rainbow", "Rainbow", "Rainbow", "Rainbow", "Rainbow", "Rainbow", "Rainbow"]}, {"customdata": [0, 1, 2, 3, 4, 5], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0,0,131)", "rgb(0,60,170)", "rgb(5,255,255)", "rgb(255,255,0)", "rgb(250,0,0)", "rgb(128,0,0)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1], "y": ["Jet", "Jet", "Jet", "Jet", "Jet", "Jet"]}, {"customdata": [0, 1, 2, 3], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0,0,0)", "rgb(230,0,0)", "rgb(255,210,0)", "rgb(255,255,255)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1], "y": ["Hot", "Hot", "Hot", "Hot"]}, {"customdata": [0, 1, 2, 3, 4, 5], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0,0,0)", "rgb(30,0,100)", "rgb(120,0,100)", "rgb(160,90,0)", "rgb(230,200,0)", "rgb(255,250,220)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1], "y": ["Electric", "Electric", "Electric", "Electric", "Electric", "Electric"]}, {"customdata": [0, 1], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0,0,255)", "rgb(255,0,0)"]}, "orientation": "h", "type": "bar", "x": [1, 1], "y": ["Bluered", "Bluered"]}, {"customdata": [0, 1, 2, 3, 4], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["rgb(0,0,0)", "rgb(230,0,0)", "rgb(230,210,0)", "rgb(255,255,255)", "rgb(160,200,255)"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1], "y": ["Blackbody", "Blackbody", "Blackbody", "Blackbody", "Blackbody"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#0d0887", "#46039f", "#7201a8", "#9c179e", "#bd3786", "#d8576b", "#ed7953", "#fb9f3a", "#fdca26", "#f0f921"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Plasma", "Plasma", "Plasma", "Plasma", "Plasma", "Plasma", "Plasma", "Plasma", "Plasma", "Plasma"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#000004", "#180f3d", "#440f76", "#721f81", "#9e2f7f", "#cd4071", "#f1605d", "#fd9668", "#feca8d", "#fcfdbf"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Magma", "Magma", "Magma", "Magma", "Magma", "Magma", "Magma", "Magma", "Magma", "Magma"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#000004", "#1b0c41", "#4a0c6b", "#781c6d", "#a52c60", "#cf4446", "#ed6925", "#fb9b06", "#f7d13d", "#fcffa4"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Inferno", "Inferno", "Inferno", "Inferno", "Inferno", "Inferno", "Inferno", "Inferno", "Inferno", "Inferno"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#00224e", "#123570", "#3b496c", "#575d6d", "#707173", "#8a8678", "#a59c74", "#c3b369", "#e1cc55", "#fee838"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Cividis", "Cividis", "Cividis", "Cividis", "Cividis", "Cividis", "Cividis", "Cividis", "Cividis", "Cividis"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#440154", "#482878", "#3e4989", "#31688e", "#26828e", "#1f9e89", "#35b779", "#6ece58", "#b5de2b", "#fde725"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Viridis", "Viridis", "Viridis", "Viridis", "Viridis", "Viridis", "Viridis", "Viridis", "Viridis", "Viridis"]}, {"customdata": [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12], "hovertemplate": "%{y}[%{customdata}] = %{marker.color}<extra></extra>", "marker": {"color": ["#0508b8", "#1910d8", "#3c19f0", "#6b1cfb", "#981cfd", "#bf1cfd", "#dd2bfd", "#f246fe", "#fc67fd", "#fe88fc", "#fea5fd", "#febefe", "#fec3fe"]}, "orientation": "h", "type": "bar", "x": [1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1], "y": ["Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3", "Plotly3"]}],
                            {"bargap": 0.5, "barmode": "stack", "barnorm": "fraction", "height": 2600, "showlegend": false, "template": {"data": {"bar": [{"error_x": {"color": "#2a3f5f"}, "error_y": {"color": "#2a3f5f"}, "marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "#E5ECF6", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "baxis": {"endlinecolor": "#2a3f5f", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "#2a3f5f"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#EBF0F8"}, "line": {"color": "white"}}, "header": {"fill": {"color": "#C8D4E3"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#2a3f5f", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#2a3f5f"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "#E5ECF6", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "light"}, "paper_bgcolor": "white", "plot_bgcolor": "#E5ECF6", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "radialaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "#E5ECF6", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"line": {"color": "#2a3f5f"}}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}, "bgcolor": "#E5ECF6", "caxis": {"gridcolor": "white", "linecolor": "white", "ticks": ""}}, "title": {"x": 0.05}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "ticks": "", "zerolinecolor": "white", "zerolinewidth": 2}}}, "title": {"text": "_plotly_utils.colors.sequential"}, "xaxis": {"range": [-0.02, 1.02], "showgrid": false, "showticklabels": false}},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('ea619f8c-acef-4663-96f1-bffe6457692a');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


As for templates they are easily listed by the following:

.. code:: ipython3

    import plotly.io as pio
    pio.templates




.. parsed-literal::

    Templates configuration
    -----------------------
        Default template: 'plotly'
        Available templates:
            ['ggplot2', 'seaborn', 'plotly', 'plotly_white', 'plotly_dark',
             'presentation', 'xgridoff', 'none']



Now, let’s try out some of these colors and templates!

.. code:: ipython3

    WR.plot(output_type='show', template='seaborn', colors='Plotly3', width=600, height=600)



.. raw:: html

    <div>
    
    
                <div id="509890b1-d1f3-4c65-bf82-fe037eadeaaf" class="plotly-graph-div" style="height:600px; width:600px;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("509890b1-d1f3-4c65-bf82-fe037eadeaaf")) {
                        Plotly.newPlot(
                            '509890b1-d1f3-4c65-bf82-fe037eadeaaf',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-2.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-2.46", "marker": {"color": "#fec3fe"}, "name": "speed=-0.00-2.46", "r": [1554.0, 1303.0, 1086.0, 880.0, 722.0, 664.0, 625.0, 647.0, 650.0, 696.0, 747.0, 750.0, 824.0, 854.0, 844.0, 671.0, 647.0, 741.0, 801.0, 1048.0, 1566.0, 2481.0, 2981.0, 2689.0, 2016.0, 1539.0, 1206.0, 1154.0, 1188.0, 1541.0, 1716.0, 1764.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=2.46-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=2.46-4.92", "marker": {"color": "#febefe"}, "name": "speed=2.46-4.92", "r": [365.0, 156.0, 86.0, 35.0, 21.0, 29.0, 17.0, 24.0, 23.0, 57.0, 98.0, 294.0, 578.0, 615.0, 335.0, 151.0, 145.0, 114.0, 184.0, 343.0, 999.0, 3075.0, 6702.0, 7250.0, 2787.0, 827.0, 347.0, 343.0, 474.0, 872.0, 1110.0, 867.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-7.37<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-7.37", "marker": {"color": "#fea5fd"}, "name": "speed=4.92-7.37", "r": [68.0, 10.0, 1.0, 1.0, 3.0, 30.0, 119.0, 330.0, 280.0, 64.0, 23.0, 21.0, 40.0, 38.0, 74.0, 267.0, 1657.0, 4688.0, 4225.0, 884.0, 74.0, 12.0, 27.0, 150.0, 435.0, 608.0, 298.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=7.37-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=7.37-9.83", "marker": {"color": "#fe88fc"}, "name": "speed=7.37-9.83", "r": [12.0, 5.0, 4.0, 1.0, 29.0, 139.0, 103.0, 9.0, 2.0, 2.0, 7.0, 6.0, 72.0, 233.0, 737.0, 442.0, 38.0, 1.0, 1.0, 18.0, 147.0, 288.0, 109.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-12.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-12.28", "marker": {"color": "#fc67fd"}, "name": "speed=9.83-12.28", "r": [1.0, 1.0, 7.0, 46.0, 32.0, 2.0, 1.0, 3.0, 3.0, 14.0, 8.0, 2.0, 21.0, 40.0, 37.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "SW", "WSW", "WSW", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=12.28-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=12.28-14.73", "marker": {"color": "#f246fe"}, "name": "speed=12.28-14.73", "r": [3.0, 4.0, 2.0, 11.0], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "margin": {"t": 60}, "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"data": {"bar": [{"error_x": {"color": "rgb(36,36,36)"}, "error_y": {"color": "rgb(36,36,36)"}, "marker": {"line": {"color": "rgb(234,234,242)", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "rgb(234,234,242)", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "rgb(36,36,36)", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "rgb(36,36,36)"}, "baxis": {"endlinecolor": "rgb(36,36,36)", "gridcolor": "white", "linecolor": "white", "minorgridcolor": "white", "startlinecolor": "rgb(36,36,36)"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "parcoords"}], "scatter": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scattergeo"}], "scattergl": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "tickcolor": "rgb(36,36,36)", "ticklen": 8, "ticks": "outside", "tickwidth": 2}, "colorscale": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "rgb(231,231,240)"}, "line": {"color": "white"}}, "header": {"fill": {"color": "rgb(183,183,191)"}, "line": {"color": "white"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "rgb(67,103,167)"}, "colorscale": {"sequential": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]], "sequentialminus": [[0.0, "rgb(2,4,25)"], [0.06274509803921569, "rgb(24,15,41)"], [0.12549019607843137, "rgb(47,23,57)"], [0.18823529411764706, "rgb(71,28,72)"], [0.25098039215686274, "rgb(97,30,82)"], [0.3137254901960784, "rgb(123,30,89)"], [0.3764705882352941, "rgb(150,27,91)"], [0.4392156862745098, "rgb(177,22,88)"], [0.5019607843137255, "rgb(203,26,79)"], [0.5647058823529412, "rgb(223,47,67)"], [0.6274509803921569, "rgb(236,76,61)"], [0.6901960784313725, "rgb(242,107,73)"], [0.7529411764705882, "rgb(244,135,95)"], [0.8156862745098039, "rgb(245,162,122)"], [0.8784313725490196, "rgb(246,188,153)"], [0.9411764705882353, "rgb(247,212,187)"], [1.0, "rgb(250,234,220)"]]}, "colorway": ["rgb(76,114,176)", "rgb(221,132,82)", "rgb(85,168,104)", "rgb(196,78,82)", "rgb(129,114,179)", "rgb(147,120,96)", "rgb(218,139,195)", "rgb(140,140,140)", "rgb(204,185,116)", "rgb(100,181,205)"], "font": {"color": "rgb(36,36,36)"}, "geo": {"bgcolor": "white", "lakecolor": "white", "landcolor": "rgb(234,234,242)", "showlakes": true, "showland": true, "subunitcolor": "white"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "paper_bgcolor": "white", "plot_bgcolor": "rgb(234,234,242)", "polar": {"angularaxis": {"gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": ""}, "bgcolor": "rgb(234,234,242)", "radialaxis": {"gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "rgb(234,234,242)", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "showgrid": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"backgroundcolor": "rgb(234,234,242)", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "showgrid": true, "ticks": "", "zerolinecolor": "white"}, "zaxis": {"backgroundcolor": "rgb(234,234,242)", "gridcolor": "white", "gridwidth": 2, "linecolor": "white", "showbackground": true, "showgrid": true, "ticks": "", "zerolinecolor": "white"}}, "shapedefaults": {"fillcolor": "rgb(67,103,167)", "line": {"width": 0}, "opacity": 0.5}, "ternary": {"aaxis": {"gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": ""}, "baxis": {"gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": ""}, "bgcolor": "rgb(234,234,242)", "caxis": {"gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": ""}}, "xaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": "", "zerolinecolor": "white"}, "yaxis": {"automargin": true, "gridcolor": "white", "linecolor": "white", "showgrid": true, "ticks": "", "zerolinecolor": "white"}}}, "width": 600},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('509890b1-d1f3-4c65-bf82-fe037eadeaaf');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


.. code:: ipython3

    # with colors not reversed
    WR.plot(output_type='show', template='xgridoff', colors='turbid', colors_reversed=False)



.. raw:: html

    <div>
    
    
                <div id="9294b858-ea2b-4dcd-9457-8d373be96d69" class="plotly-graph-div" style="height:600px; width:100%;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("9294b858-ea2b-4dcd-9457-8d373be96d69")) {
                        Plotly.newPlot(
                            '9294b858-ea2b-4dcd-9457-8d373be96d69',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-2.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-2.46", "marker": {"color": "rgb(232, 245, 171)"}, "name": "speed=-0.00-2.46", "r": [1554.0, 1303.0, 1086.0, 880.0, 722.0, 664.0, 625.0, 647.0, 650.0, 696.0, 747.0, 750.0, 824.0, 854.0, 844.0, 671.0, 647.0, 741.0, 801.0, 1048.0, 1566.0, 2481.0, 2981.0, 2689.0, 2016.0, 1539.0, 1206.0, 1154.0, 1188.0, 1541.0, 1716.0, 1764.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=2.46-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=2.46-4.92", "marker": {"color": "rgb(220, 219, 137)"}, "name": "speed=2.46-4.92", "r": [365.0, 156.0, 86.0, 35.0, 21.0, 29.0, 17.0, 24.0, 23.0, 57.0, 98.0, 294.0, 578.0, 615.0, 335.0, 151.0, 145.0, 114.0, 184.0, 343.0, 999.0, 3075.0, 6702.0, 7250.0, 2787.0, 827.0, 347.0, 343.0, 474.0, 872.0, 1110.0, 867.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-7.37<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-7.37", "marker": {"color": "rgb(209, 193, 107)"}, "name": "speed=4.92-7.37", "r": [68.0, 10.0, 1.0, 1.0, 3.0, 30.0, 119.0, 330.0, 280.0, 64.0, 23.0, 21.0, 40.0, 38.0, 74.0, 267.0, 1657.0, 4688.0, 4225.0, 884.0, 74.0, 12.0, 27.0, 150.0, 435.0, 608.0, 298.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=7.37-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=7.37-9.83", "marker": {"color": "rgb(199, 168, 83)"}, "name": "speed=7.37-9.83", "r": [12.0, 5.0, 4.0, 1.0, 29.0, 139.0, 103.0, 9.0, 2.0, 2.0, 7.0, 6.0, 72.0, 233.0, 737.0, 442.0, 38.0, 1.0, 1.0, 18.0, 147.0, 288.0, 109.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-12.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-12.28", "marker": {"color": "rgb(186, 143, 66)"}, "name": "speed=9.83-12.28", "r": [1.0, 1.0, 7.0, 46.0, 32.0, 2.0, 1.0, 3.0, 3.0, 14.0, 8.0, 2.0, 21.0, 40.0, 37.0], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "SW", "WSW", "WSW", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=12.28-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=12.28-14.73", "marker": {"color": "rgb(170, 121, 60)"}, "name": "speed=12.28-14.73", "r": [3.0, 4.0, 2.0, 11.0], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "margin": {"t": 60}, "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"layout": {"xaxis": {"showgrid": false}}}},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('9294b858-ea2b-4dcd-9457-8d373be96d69');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


This example not only shows different color schemes but that you can
pass additional useful keyword arguments that are accepted by
``plotly.express.bar_polar`` such as ``title``, and ``width`` to
``WindRose.plot``. It also demonstrates that HTML can be embedded into
the plot title and an example of prefiltering the wind time series to
before calculating wind statistics, in this case to create a wind rose
for the winter months only.

.. code:: ipython3

    # reassign the wind data but sliced just for Dec-Mar
    WR.df = df[['ws','wd']].loc[df.index.month.isin([12,1,2,3])]
    # calculate the wind statistics (only necessary because not using default n bins)
    WR.calc_stats(normed=True, bins=6)
    WR.plot(
        output_type='show', 
        colors='Greens', 
        template='plotly_dark', 
        colors_reversed=False,
        width=600,
        height=600,
        title='Eddy Flux Site on Twitchell Island, CA <br>Wind measured Dec-Mar<br><a href="https://ameriflux.lbl.gov/sites/siteinfo/US-Tw3">Visit site</a>'
    )



.. raw:: html

    <div>
    
    
                <div id="037806de-3f3f-4bbc-8e10-1e73fdbedde3" class="plotly-graph-div" style="height:600px; width:600px;"></div>
                <script type="text/javascript">
                    require(["plotly"], function(Plotly) {
                        window.PLOTLYENV=window.PLOTLYENV || {};
    
                    if (document.getElementById("037806de-3f3f-4bbc-8e10-1e73fdbedde3")) {
                        Plotly.newPlot(
                            '037806de-3f3f-4bbc-8e10-1e73fdbedde3',
                            [{"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=-0.00-2.46<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=-0.00-2.46", "marker": {"color": "rgb(247,252,245)"}, "name": "speed=-0.00-2.46", "r": [3.5, 2.82, 2.38, 1.88, 1.47, 1.38, 1.3, 1.32, 1.32, 1.47, 1.49, 1.58, 1.84, 1.86, 1.55, 1.22, 0.98, 1.01, 1.12, 1.28, 1.84, 2.68, 3.21, 3.22, 2.26, 1.74, 1.59, 1.92, 2.09, 2.64, 3.49, 3.71], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=2.46-4.92<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=2.46-4.92", "marker": {"color": "rgb(229,245,224)"}, "name": "speed=2.46-4.92", "r": [0.66, 0.37, 0.21, 0.1, 0.07, 0.09, 0.06, 0.09, 0.06, 0.16, 0.28, 0.78, 1.62, 1.86, 0.92, 0.4, 0.27, 0.16, 0.25, 0.33, 0.56, 1.02, 2.68, 3.39, 1.62, 0.64, 0.42, 0.48, 0.65, 1.21, 1.77, 1.52], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "NE", "NE", "ENE", "ENE", "E", "E", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=4.92-7.37<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=4.92-7.37", "marker": {"color": "rgb(199,233,192)"}, "name": "speed=4.92-7.37", "r": [0.12, 0.03, 0.0, 0.01, 0.1, 0.4, 1.06, 0.92, 0.21, 0.07, 0.06, 0.11, 0.09, 0.12, 0.12, 0.45, 0.89, 1.05, 0.32, 0.04, 0.02, 0.05, 0.21, 0.63, 1.11, 0.5], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "ENE", "ESE", "ESE", "SE", "SE", "SSE", "SSE", "S", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=7.37-9.83<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=7.37-9.83", "marker": {"color": "rgb(161,217,155)"}, "name": "speed=7.37-9.83", "r": [0.03, 0.01, 0.01, 0.0, 0.1, 0.48, 0.38, 0.03, 0.01, 0.01, 0.02, 0.01, 0.0, 0.07, 0.27, 0.12, 0.03, 0.0, 0.0, 0.04, 0.29, 0.57, 0.18], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "NNE", "ESE", "SE", "SE", "SSE", "SSE", "S", "SSW", "SSW", "SW", "SW", "WSW", "WSW", "W", "W", "WNW", "NW", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=9.83-12.28<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=9.83-12.28", "marker": {"color": "rgb(116,196,118)"}, "name": "speed=9.83-12.28", "r": [0.0, 0.0, 0.03, 0.17, 0.12, 0.01, 0.0, 0.01, 0.0, 0.08, 0.13, 0.1], "showlegend": true, "subplot": "polar", "theta": ["N", "NNE", "SE", "SE", "SSE", "SSE", "S", "W", "NW", "NNW", "NNW", "N"], "type": "barpolar"}, {"hoverlabel": {"namelength": 0}, "hovertemplate": "speed=12.28-14.73<br>frequency=%{r}<br>direction=%{theta}", "legendgroup": "speed=12.28-14.73", "marker": {"color": "rgb(65,171,93)"}, "name": "speed=12.28-14.73", "r": [0.01, 0.01, 0.01, 0.04], "showlegend": true, "subplot": "polar", "theta": ["SE", "SE", "SSE", "NNW"], "type": "barpolar"}],
                            {"barmode": "relative", "barnorm": "", "height": 600, "legend": {"tracegroupgap": 0}, "polar": {"angularaxis": {"direction": "clockwise", "rotation": 90}, "domain": {"x": [0.0, 0.98], "y": [0.0, 1.0]}}, "template": {"data": {"bar": [{"error_x": {"color": "#f2f5fa"}, "error_y": {"color": "#f2f5fa"}, "marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "bar"}], "barpolar": [{"marker": {"line": {"color": "rgb(17,17,17)", "width": 0.5}}, "type": "barpolar"}], "carpet": [{"aaxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "baxis": {"endlinecolor": "#A2B1C6", "gridcolor": "#506784", "linecolor": "#506784", "minorgridcolor": "#506784", "startlinecolor": "#A2B1C6"}, "type": "carpet"}], "choropleth": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "choropleth"}], "contour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "contour"}], "contourcarpet": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "contourcarpet"}], "heatmap": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmap"}], "heatmapgl": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "heatmapgl"}], "histogram": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "histogram"}], "histogram2d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2d"}], "histogram2dcontour": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "histogram2dcontour"}], "mesh3d": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "type": "mesh3d"}], "parcoords": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "parcoords"}], "scatter": [{"marker": {"line": {"color": "#283442"}}, "type": "scatter"}], "scatter3d": [{"line": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatter3d"}], "scattercarpet": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattercarpet"}], "scattergeo": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattergeo"}], "scattergl": [{"marker": {"line": {"color": "#283442"}}, "type": "scattergl"}], "scattermapbox": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scattermapbox"}], "scatterpolar": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolar"}], "scatterpolargl": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterpolargl"}], "scatterternary": [{"marker": {"colorbar": {"outlinewidth": 0, "ticks": ""}}, "type": "scatterternary"}], "surface": [{"colorbar": {"outlinewidth": 0, "ticks": ""}, "colorscale": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "type": "surface"}], "table": [{"cells": {"fill": {"color": "#506784"}, "line": {"color": "rgb(17,17,17)"}}, "header": {"fill": {"color": "#2a3f5f"}, "line": {"color": "rgb(17,17,17)"}}, "type": "table"}]}, "layout": {"annotationdefaults": {"arrowcolor": "#f2f5fa", "arrowhead": 0, "arrowwidth": 1}, "colorscale": {"diverging": [[0, "#8e0152"], [0.1, "#c51b7d"], [0.2, "#de77ae"], [0.3, "#f1b6da"], [0.4, "#fde0ef"], [0.5, "#f7f7f7"], [0.6, "#e6f5d0"], [0.7, "#b8e186"], [0.8, "#7fbc41"], [0.9, "#4d9221"], [1, "#276419"]], "sequential": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]], "sequentialminus": [[0.0, "#0d0887"], [0.1111111111111111, "#46039f"], [0.2222222222222222, "#7201a8"], [0.3333333333333333, "#9c179e"], [0.4444444444444444, "#bd3786"], [0.5555555555555556, "#d8576b"], [0.6666666666666666, "#ed7953"], [0.7777777777777778, "#fb9f3a"], [0.8888888888888888, "#fdca26"], [1.0, "#f0f921"]]}, "colorway": ["#636efa", "#EF553B", "#00cc96", "#ab63fa", "#FFA15A", "#19d3f3", "#FF6692", "#B6E880", "#FF97FF", "#FECB52"], "font": {"color": "#f2f5fa"}, "geo": {"bgcolor": "rgb(17,17,17)", "lakecolor": "rgb(17,17,17)", "landcolor": "rgb(17,17,17)", "showlakes": true, "showland": true, "subunitcolor": "#506784"}, "hoverlabel": {"align": "left"}, "hovermode": "closest", "mapbox": {"style": "dark"}, "paper_bgcolor": "rgb(17,17,17)", "plot_bgcolor": "rgb(17,17,17)", "polar": {"angularaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "radialaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "scene": {"xaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "yaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}, "zaxis": {"backgroundcolor": "rgb(17,17,17)", "gridcolor": "#506784", "gridwidth": 2, "linecolor": "#506784", "showbackground": true, "ticks": "", "zerolinecolor": "#C8D4E3"}}, "shapedefaults": {"line": {"color": "#f2f5fa"}}, "sliderdefaults": {"bgcolor": "#C8D4E3", "bordercolor": "rgb(17,17,17)", "borderwidth": 1, "tickwidth": 0}, "ternary": {"aaxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "baxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}, "bgcolor": "rgb(17,17,17)", "caxis": {"gridcolor": "#506784", "linecolor": "#506784", "ticks": ""}}, "title": {"x": 0.05}, "updatemenudefaults": {"bgcolor": "#506784", "borderwidth": 0}, "xaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}, "yaxis": {"automargin": true, "gridcolor": "#283442", "linecolor": "#506784", "ticks": "", "zerolinecolor": "#283442", "zerolinewidth": 2}}}, "title": {"text": "Eddy Flux Site on Twitchell Island, CA <br>Wind measured Dec-Mar<br><a href=\"https://ameriflux.lbl.gov/sites/siteinfo/US-Tw3\">Visit site</a>"}, "width": 600},
                            {"responsive": true}
                        ).then(function(){
    
    var gd = document.getElementById('037806de-3f3f-4bbc-8e10-1e73fdbedde3');
    var x = new MutationObserver(function (mutations, observer) {{
            var display = window.getComputedStyle(gd).display;
            if (!display || display === 'none') {{
                console.log([gd, 'removed!']);
                Plotly.purge(gd);
                observer.disconnect();
            }}
    }});
    
    // Listen for the removal of the full notebook cells
    var notebookContainer = gd.closest('#notebook-container');
    if (notebookContainer) {{
        x.observe(notebookContainer, {childList: true});
    }}
    
    // Listen for the clearing of the current output cell
    var outputEl = gd.closest('.output');
    if (outputEl) {{
        x.observe(outputEl, {childList: true});
    }}
    
                            })
                    };
                    });
                </script>
            </div>


As we can see the winter wind system is substantially different from the
average long-term wind which may be expected due to seasonal storm
systems or temporally varying larger scale atmospheric circulations.