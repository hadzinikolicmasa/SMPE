# Doe shiny app report

I realized too late that I have to change username, so instead of doing the experiment under user_h7100, I did it under user_a7710. (I appologize)

First, as we can see, there are some input sets in the beggining that are same so I probably clicked on "run" many times because I didn't know what was I suppose to do.

When I finally realized what is the task, I did a few experiments on my own and I tried to find the corellation between inputs and output by myself. For input variables, I used border values (either around 0 or around 1) as well as the values in the middle and I observed the changes in the output value. I didn't conclude anything interesting, maybe because I gave up too soon, but I thought it was possible that some of the input variables were related and it was difficult to come to the conclusion manually.

I wrote the script to generate 200 sets with random number for all 11 inputs. The goal of this is to generate the output out of this random sets and then find a corelation between variables.


```python
import random
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt


def generate_random():
     return [round(random.uniform(0, 1), 2) for _ in range(11)]

output_file = 'output.txt'

with open(output_file, 'w') as output:
    for i in range(200):
        random_numbers = generate_random()
        output.write(','.join(map(str, random_numbers)) + '\n')
```


```python
file_path = 'results.txt'
data = pd.read_csv(file_path)

data = data.iloc[:, 1:]

print(data)
```

           x1    x2    x3    x4    x5    x6    x7    x8    x9   x10   x11  \
    0    0.75  0.32  0.07  0.94  0.02  0.12  0.67  0.47  0.49  1.00  0.72   
    1    0.90  0.32  0.77  0.94  0.73  0.14  0.75  0.83  0.33  0.32  0.02   
    2    0.41  0.22  0.03  0.55  0.37  0.80  0.08  0.00  0.00  0.69  0.35   
    3    0.38  0.90  0.24  0.34  0.63  0.81  0.42  0.46  0.88  0.17  0.89   
    4    0.51  0.97  0.18  0.66  0.64  0.88  0.37  0.11  0.32  0.94  0.17   
    ..    ...   ...   ...   ...   ...   ...   ...   ...   ...   ...   ...   
    195  0.40  0.49  0.85  0.09  0.49  0.97  0.17  0.61  0.60  0.40  0.54   
    196  0.83  0.86  0.96  1.00  0.39  0.69  0.73  0.14  0.89  0.84  0.52   
    197  0.11  0.71  0.65  0.25  0.65  0.37  0.72  0.37  0.06  0.50  0.41   
    198  0.94  0.93  0.56  0.50  0.06  0.59  0.52  0.53  0.98  0.63  0.45   
    199  0.24  0.68  0.28  0.26  0.60  0.77  0.39  0.42  0.41  0.97  0.45   
    
                y  
    0    2.476506  
    1    1.269759  
    2    1.924789  
    3   -0.142488  
    4    1.652566  
    ..        ...  
    195  0.242589  
    196  1.139174  
    197  1.122512  
    198 -0.765141  
    199  0.529800  
    
    [200 rows x 12 columns]


Here we can see the highest possible value of the output that I got :


```python
index_highest_y = data['y'].idxmax()
row_with_highest_y = data.loc[index_highest_y]

print("The highest output value found: ")
print(row_with_highest_y)
```

    The highest output value found: 
    x1     0.730000
    x2     0.140000
    x3     0.380000
    x4     0.630000
    x5     0.720000
    x6     0.230000
    x7     0.270000
    x8     0.320000
    x9     0.000000
    x10    0.680000
    x11    0.530000
    y      3.293627
    Name: 125, dtype: float64


I made a correlation matrix that helps to better observe the dependency between the input variables and the output value.


```python
correlation_matrix = data.corr()

plt.figure(figsize=(12, 12))
sns.heatmap(correlation_matrix, annot=True, cmap="twilight_shifted")
plt.title("Correlation matrix")
plt.show()
```


    
![Matrix](data/output_10_0.png)
 


From this correlation matrix, we can see that x9 has a strong negative correlation, meaning that when the values of x9 decrease, the value of y increase. Other than that, x1 and x4 show a weak positive correalation which may have some but not significant impact.

To observe this, we will run the experiment with both x9 = 0 and x9 = 1, other values will remain the same . These are the results : 

x1=0.73	
x2=0.14	
x3=0.38	
x4=0.63	
x5=0.72	
x6=0.23	
x7=0.27	
x8=0.32	
##### x9=0	
x10=0.68	
x11=0.53
_______________
y=3.29560786609134


x1=0.73	
x2=0.14	
x3=0.38	
x4=0.63	
x5=0.72	
x6=0.23	
x7=0.27	
x8=0.32	
##### x9=1
x10=0.68	
x11=0.53
_______________
y=1.29481442071534

We can see that value of x9 significantly changes the output.

Similary, when we change variable x2, we can see that there are not any big changes which is expected because the correlation is very weak, only -0.065.

But, observing variabe x7, we can see that it does impact the output but the correlation in the matrix is very low. These are the results of the experiment:

x1=0.73	
x2=0.14	
x3=0.38	
x4=0.63	
x5=0.72	
x6=0.23	
##### x7=0
x8=0.32	
x9=0
x10=0.68	
x11=0.53
_______________
y=3.34184458423556


x1=0.73	
x2=0.14	
x3=0.38	
x4=0.63	
x5=0.72	
x6=0.23	
##### x7=1
x8=0.32	
x9=0
x10=0.68	
x11=0.53
_______________
y=3.15670840712951

This weak correlation that is statistically significant suggests that that particular exposure has an impact on the outcome variable, but that there are other important determinants as well. Also, we can conclude that there is non linear connection between x7 and y.

Doing this experiment, I found by myself a new highest value for y which is 3.34184458423556.




```python

```
