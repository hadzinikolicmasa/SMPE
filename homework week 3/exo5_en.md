# Analysis of the risk of failure of the O-rings on the Challenger shuttle

On January 27, 1986, the day before the takeoff of the shuttle _Challenger_, had
a three-hour teleconference was held between 
Morton Thiokol (the manufacturer of one of the engines) and NASA. The
discussion focused on the consequences of the
temperature at take-off of 31°F (just below
0°C) for the success of the flight and in particular on the performance of the
O-rings used in the engines. Indeed, no test
had been performed at this temperature.

The following study takes up some of the analyses carried out that
night with the objective of assessing the potential influence of
the temperature and pressure to which the O-rings are subjected
on their probability of malfunction. Our starting point is 
the results of the experiments carried out by NASA engineers
during the six years preceding the launch of the shuttle
Challenger.

## Loading the data
We start by loading this data:


```python
import numpy as np
import pandas as pd
data = pd.read_csv("shuttle.csv")
data
```




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
      <th>Date</th>
      <th>Count</th>
      <th>Temperature</th>
      <th>Pressure</th>
      <th>Malfunction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>4/12/81</td>
      <td>6</td>
      <td>66</td>
      <td>50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>11/12/81</td>
      <td>6</td>
      <td>70</td>
      <td>50</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3/22/82</td>
      <td>6</td>
      <td>69</td>
      <td>50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11/11/82</td>
      <td>6</td>
      <td>68</td>
      <td>50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4/04/83</td>
      <td>6</td>
      <td>67</td>
      <td>50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6/18/82</td>
      <td>6</td>
      <td>72</td>
      <td>50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>8/30/83</td>
      <td>6</td>
      <td>73</td>
      <td>100</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>11/28/83</td>
      <td>6</td>
      <td>70</td>
      <td>100</td>
      <td>0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2/03/84</td>
      <td>6</td>
      <td>57</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>4/06/84</td>
      <td>6</td>
      <td>63</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>10</th>
      <td>8/30/84</td>
      <td>6</td>
      <td>70</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>11</th>
      <td>10/05/84</td>
      <td>6</td>
      <td>78</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>11/08/84</td>
      <td>6</td>
      <td>67</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1/24/85</td>
      <td>6</td>
      <td>53</td>
      <td>200</td>
      <td>2</td>
    </tr>
    <tr>
      <th>14</th>
      <td>4/12/85</td>
      <td>6</td>
      <td>67</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>15</th>
      <td>4/29/85</td>
      <td>6</td>
      <td>75</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>16</th>
      <td>6/17/85</td>
      <td>6</td>
      <td>70</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>17</th>
      <td>7/29/85</td>
      <td>6</td>
      <td>81</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>18</th>
      <td>8/27/85</td>
      <td>6</td>
      <td>76</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>19</th>
      <td>10/03/85</td>
      <td>6</td>
      <td>79</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>20</th>
      <td>10/30/85</td>
      <td>6</td>
      <td>75</td>
      <td>200</td>
      <td>2</td>
    </tr>
    <tr>
      <th>21</th>
      <td>11/26/85</td>
      <td>6</td>
      <td>76</td>
      <td>200</td>
      <td>0</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1/12/86</td>
      <td>6</td>
      <td>58</td>
      <td>200</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



The data set shows us the date of each test, the number of O-rings (there are 6 on the main launcher), the temperature (in Fahrenheit) and pressure (in psi), and finally the number of identified malfunctions.

## Graphical inspection
Flights without incidents do not provide any information
on the influence of temperature or pressure on malfunction.
We thus focus on the experiments in which at least one O-ring
was defective.


```python
data = data[data.Malfunction>0]
data
```




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
      <th>Date</th>
      <th>Count</th>
      <th>Temperature</th>
      <th>Pressure</th>
      <th>Malfunction</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>11/12/81</td>
      <td>6</td>
      <td>70</td>
      <td>50</td>
      <td>1</td>
    </tr>
    <tr>
      <th>8</th>
      <td>2/03/84</td>
      <td>6</td>
      <td>57</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>4/06/84</td>
      <td>6</td>
      <td>63</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>10</th>
      <td>8/30/84</td>
      <td>6</td>
      <td>70</td>
      <td>200</td>
      <td>1</td>
    </tr>
    <tr>
      <th>13</th>
      <td>1/24/85</td>
      <td>6</td>
      <td>53</td>
      <td>200</td>
      <td>2</td>
    </tr>
    <tr>
      <th>20</th>
      <td>10/30/85</td>
      <td>6</td>
      <td>75</td>
      <td>200</td>
      <td>2</td>
    </tr>
    <tr>
      <th>22</th>
      <td>1/12/86</td>
      <td>6</td>
      <td>58</td>
      <td>200</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



We have a high temperature variability but
the pressure is almost always 200, which should
simplify the analysis.

How does the frequency of failure vary with temperature?


```python
%matplotlib inline
pd.set_option('mode.chained_assignment',None) # this removes a useless warning from pandas
import matplotlib.pyplot as plt

data["Frequency"]=data.Malfunction/data.Count
data.plot(x="Temperature",y="Frequency",kind="scatter",ylim=[0,1])
plt.grid(True)
```


![png](output_8_0.png)


At first glance, the dependence does not look very important, but let's try to
estimate the impact of temperature $t$ on the probability of O-ring malfunction.

## Estimation of the temperature influence

Suppose that each of the six O-rings is damaged with the same
probability and independently of the others and that this probability
depends only on the temperature. If $p(t)$ is this probability, the
number $D$ of malfunctioning O-rings during a flight at
temperature $t$ follows a binomial law with parameters $n=6$ and
$p=p(t)$. To link $p(t)$ to $t$, we will therefore perform a
logistic regression.


```python
import statsmodels.api as sm

data["Success"]=data.Count-data.Malfunction
data["Intercept"]=1

logmodel=sm.GLM(data['Frequency'], data[['Intercept','Temperature']], family=sm.families.Binomial(sm.families.links.logit)).fit()

logmodel.summary()
```




<table class="simpletable">
<caption>Generalized Linear Model Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>      <td>Frequency</td>    <th>  No. Observations:  </th>  <td>     7</td>  
</tr>
<tr>
  <th>Model:</th>                 <td>GLM</td>       <th>  Df Residuals:      </th>  <td>     5</td>  
</tr>
<tr>
  <th>Model Family:</th>       <td>Binomial</td>     <th>  Df Model:          </th>  <td>     1</td>  
</tr>
<tr>
  <th>Link Function:</th>        <td>logit</td>      <th>  Scale:             </th> <td>  1.0000</td> 
</tr>
<tr>
  <th>Method:</th>               <td>IRLS</td>       <th>  Log-Likelihood:    </th> <td> -2.5250</td> 
</tr>
<tr>
  <th>Date:</th>           <td>Sat, 13 Apr 2019</td> <th>  Deviance:          </th> <td> 0.22231</td> 
</tr>
<tr>
  <th>Time:</th>               <td>19:12:05</td>     <th>  Pearson chi2:      </th>  <td> 0.236</td>  
</tr>
<tr>
  <th>No. Iterations:</th>         <td>4</td>        <th>  Covariance Type:   </th> <td>nonrobust</td>
</tr>
</table>
<table class="simpletable">
<tr>
       <td></td>          <th>coef</th>     <th>std err</th>      <th>z</th>      <th>P>|z|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>Intercept</th>   <td>   -1.3895</td> <td>    7.828</td> <td>   -0.178</td> <td> 0.859</td> <td>  -16.732</td> <td>   13.953</td>
</tr>
<tr>
  <th>Temperature</th> <td>    0.0014</td> <td>    0.122</td> <td>    0.012</td> <td> 0.991</td> <td>   -0.238</td> <td>    0.240</td>
</tr>
</table>



The most likely estimator of the temperature parameter is 0.0014
and the standard error of this estimator is 0.122, in other words we
cannot distinguish any particular impact and we must take our
estimates with caution.

## Estimation of the probability of O-ring malfunction

The expected temperature on the take-off day is 31°F. Let's try to
estimate the probability of O-ring malfunction at
this temperature from the model we just built:


```python
%matplotlib inline
data_pred = pd.DataFrame({'Temperature': np.linspace(start=30, stop=90, num=121), 'Intercept': 1})
data_pred['Frequency'] = logmodel.predict(data_pred[['Intercept','Temperature']])
data_pred.plot(x="Temperature",y="Frequency",kind="line",ylim=[0,1])
plt.scatter(x=data["Temperature"],y=data["Frequency"])
plt.grid(True)
```


![png](output_14_0.png)


As expected from the initial data, the
temperature has no significant impact on the probability of failure of the
O-rings. It will be about 0.2, as in the tests
where we had a failure of at least one joint. Let's get back
to the initial dataset to estimate the probability of failure:


```python
data = pd.read_csv("shuttle.csv")
print(np.sum(data.Malfunction)/np.sum(data.Count))
```

    0.06521739130434782


This probability is thus about $p=0.065$. Knowing that there is
a primary and a secondary O-ring on each of the three parts of the
launcher, the probability of failure of both joints of a launcher
is $p^2 \approx 0.00425$. The probability of failure of any one of the
launchers is $1-(1-p^2)^3 \approx 1.2%$.  That would really be
bad luck.... Everything is under control, so the takeoff can happen
tomorrow as planned.

But the next day, the Challenger shuttle exploded and took away
with her the seven crew members. The public was shocked and in
the subsequent investigation, the reliability of the
O-rings was questioned. Beyond the internal communication problems
of NASA, which have a lot to do with this fiasco, the previous analysis
includes (at least) a small problem.... Can you find it?
You are free to modify this analysis and to look at this dataset
from all angles in order to to explain what's wrong.


```python

```
