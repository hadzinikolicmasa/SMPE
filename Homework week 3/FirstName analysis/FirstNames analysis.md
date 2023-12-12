# Download raw data


```R
file = "dpt2021_csv.zip"
if(!file.exists(file)){
  download.file("https://www.insee.fr/fr/statistiques/fichier/2540004/dpt2021_csv.zip",
	destfile=file)
}
unzip(file)
```

# Import the libraries and build the dataset


```R
library(tidyverse)
library(dplyr)
library(ggplot2)

FirstNames <- read_delim("dpt2021.csv",delim=";")
head(FirstNames)
tail(FirstNames)
```

    [1mRows: [22m[34m3784673[39m [1mColumns: [22m[34m5[39m
    
    [36m──[39m [1mColumn specification[22m [36m────────────────────────────────────────────────────────[39m
    [1mDelimiter:[22m ";"
    [31mchr[39m (3): preusuel, annais, dpt
    [32mdbl[39m (2): sexe, nombre
    
    
    [36mℹ[39m Use [30m[47m[30m[47m`spec()`[47m[30m[49m[39m to retrieve the full column specification for this data.
    [36mℹ[39m Specify the column types or set [30m[47m[30m[47m`show_col_types = FALSE`[47m[30m[49m[39m to quiet this message.
    



<table class="dataframe">
<caption>A tibble: 6 × 5</caption>
<thead>
	<tr><th scope=col>sexe</th><th scope=col>preusuel</th><th scope=col>annais</th><th scope=col>dpt</th><th scope=col>nombre</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>02</td><td> 7</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>04</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>05</td><td> 8</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>06</td><td>23</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>07</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>08</td><td> 4</td></tr>
</tbody>
</table>




<table class="dataframe">
<caption>A tibble: 6 × 5</caption>
<thead>
	<tr><th scope=col>sexe</th><th scope=col>preusuel</th><th scope=col>annais</th><th scope=col>dpt</th><th scope=col>nombre</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>2</td><td>ZYA  </td><td>2018</td><td>59</td><td>  3</td></tr>
	<tr><td>2</td><td>ZYA  </td><td>2021</td><td>35</td><td>  5</td></tr>
	<tr><td>2</td><td>ZYA  </td><td>XXXX</td><td>XX</td><td>278</td></tr>
	<tr><td>2</td><td>ZYNA </td><td>2013</td><td>93</td><td>  3</td></tr>
	<tr><td>2</td><td>ZYNA </td><td>XXXX</td><td>XX</td><td> 68</td></tr>
	<tr><td>2</td><td>ZYNEB</td><td>XXXX</td><td>XX</td><td>125</td></tr>
</tbody>
</table>



# Cleaning the data

For better understanding, we will change the names of the columns from french to english.


```R
colnames(FirstNames) <- c("Sex", "Firstname", "Year", "Departement", "Number")
head(FirstNames)

```


<table class="dataframe">
<caption>A tibble: 6 × 5</caption>
<thead>
	<tr><th scope=col>Sex</th><th scope=col>Firstname</th><th scope=col>Year</th><th scope=col>Departement</th><th scope=col>Number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>02</td><td> 7</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>04</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>05</td><td> 8</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>06</td><td>23</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>07</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>08</td><td> 4</td></tr>
</tbody>
</table>



From the subset shown above, in certain rows we can notice values XX and XXXX for the attributes of age and departement.


```R
count_cols <- sum(FirstNames$Year == "XXXX" | FirstNames$Departement == "XX")
print(count_cols)

```

    [1] 38479


Also, as the number of such rows is not too big compared to our dataset and in order to avoid difficulties later in the analysis, we will drop those rows.


```R
FirstNames <- FirstNames %>%
  filter(Year != "XXXX")
FirstNames <- FirstNames %>%
  filter(Departement != "XX")

tail(FirstNames)
```


<table class="dataframe">
<caption>A tibble: 6 × 5</caption>
<thead>
	<tr><th scope=col>Sex</th><th scope=col>Firstname</th><th scope=col>Year</th><th scope=col>Departement</th><th scope=col>Number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>2</td><td>ZYA </td><td>2013</td><td>44 </td><td>4</td></tr>
	<tr><td>2</td><td>ZYA </td><td>2013</td><td>59 </td><td>3</td></tr>
	<tr><td>2</td><td>ZYA </td><td>2017</td><td>974</td><td>3</td></tr>
	<tr><td>2</td><td>ZYA </td><td>2018</td><td>59 </td><td>3</td></tr>
	<tr><td>2</td><td>ZYA </td><td>2021</td><td>35 </td><td>5</td></tr>
	<tr><td>2</td><td>ZYNA</td><td>2013</td><td>93 </td><td>3</td></tr>
</tbody>
</table>



Since the Year column is charachter type, we will convert it to the numeric type so we can analyze data easier later.


```R
FirstNames$Year <- as.numeric(FirstNames$Year)
head(FirstNames)
```


<table class="dataframe">
<caption>A tibble: 6 × 5</caption>
<thead>
	<tr><th scope=col>Sex</th><th scope=col>Firstname</th><th scope=col>Year</th><th scope=col>Departement</th><th scope=col>Number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>02</td><td> 7</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>04</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>05</td><td> 8</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>06</td><td>23</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>07</td><td> 9</td></tr>
	<tr><td>1</td><td>_PRENOMS_RARES</td><td>1900</td><td>08</td><td> 4</td></tr>
</tbody>
</table>



All of these following questions may need a preliminary analysis of the data, feel free to present answers and justifications in your own order and structure your report as it should be for a scientific report.

1. Choose a firstname and analyse its frequency along time. Compare several firstnames frequency

We will analyze the frequency along time of the firstname "PAOLA". 


```R
paola <- subset(FirstNames, Firstname == 'PAOLA') %>%
  select(Year, Number) %>%
  group_by(Year) %>%
  summarise(sum_number = sum(Number, na.rm = TRUE))

plot <- ggplot(data = paola, aes(x = Year, y = sum_number)) +
  geom_line(linetype = "dashed", color = "black") +
  labs(title = "Frequency of name PAOLA along time",
       x = "Year",
       y = "Frequency") +
  theme_minimal()
print(plot)
```


    
![png](output_15_0.png)
    


From the graph above, we can see that until 60's, name PAOLA was not common at all. After that, the name increases rapidly and  reaches its peak around the year of 1975. After the peak, there is a rapid decrease which is followed by gradual growth that starts in the begginging of the 80's.

Now we will compare frequency of the following Firstnames : "KELLY", "PAOLA", and "ANITA".


```R
names_to_compare <- c("KELLY", "PAOLA", "ANITA")

selected_name_data <- FirstNames %>%
  filter(Firstname %in% names_to_compare) %>%
  group_by(Firstname, Year) %>%
  summarise(sum_number = sum(Number, na.rm = TRUE),.groups = 'drop') 

plot <- ggplot(selected_name_data, aes(x = Year, y = sum_number, color = Firstname)) +
  geom_line() +
  labs(title = "Sum of Number for Selected First Names Over Time",
       x = "Year",
       y = "Sum of Number") +
  theme_minimal()

print(plot)

```


    
![png](output_18_0.png)
    


From the graph above, we can conclude that both ANITA and KELLY were popular in some period of time and their distrubution remainds of a normal distribution. ANITA reaches its peak around 60's while KELLY around the year of 2000. On the other side, PAOLA was never quite common compared to the others but we can see that its frequency slightly increases in 60's and again in 2000's.

2. Establish by gender the most given firstname by year. Analyse the evolution of the most frequent firstname.


First, we will drop rows with "PRENOMS_RARES" because it doesn't give us any adequate information about the frequency of the names and then we will count the total nummber of each name in every year. Then we will determinate the most common name in each year and the results can be shown in the tabels below.


```R
male_names_by_year <- FirstNames %>%
  filter(Firstname != "_PRENOMS_RARES") %>%
  filter(Sex == 1) %>%
  group_by(Year, Firstname) %>%
  summarise(sum_number = sum(Number), .groups = 'drop')

most_given_male <- male_names_by_year %>%
  group_by(Year) %>%
  slice(which.max(sum_number))

head(most_given_male)
tail(most_given_male)
```


<table class="dataframe">
<caption>A grouped_df: 6 × 3</caption>
<thead>
	<tr><th scope=col>Year</th><th scope=col>Firstname</th><th scope=col>sum_number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1900</td><td>JEAN</td><td>14097</td></tr>
	<tr><td>1901</td><td>JEAN</td><td>15634</td></tr>
	<tr><td>1902</td><td>JEAN</td><td>16364</td></tr>
	<tr><td>1903</td><td>JEAN</td><td>16535</td></tr>
	<tr><td>1904</td><td>JEAN</td><td>16944</td></tr>
	<tr><td>1905</td><td>JEAN</td><td>17998</td></tr>
</tbody>
</table>




<table class="dataframe">
<caption>A grouped_df: 6 × 3</caption>
<thead>
	<tr><th scope=col>Year</th><th scope=col>Firstname</th><th scope=col>sum_number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>2016</td><td>GABRIEL</td><td>5875</td></tr>
	<tr><td>2017</td><td>GABRIEL</td><td>5442</td></tr>
	<tr><td>2018</td><td>GABRIEL</td><td>5422</td></tr>
	<tr><td>2019</td><td>GABRIEL</td><td>4987</td></tr>
	<tr><td>2020</td><td>LÉO    </td><td>4494</td></tr>
	<tr><td>2021</td><td>GABRIEL</td><td>4972</td></tr>
</tbody>
</table>



The graphical representation below can describe it more closely.


```R
most_given_male <- most_given_male %>%
  filter(Year %% 5 == 0)

ggplot(most_given_male, aes(x = factor(Year), y = sum_number, fill = Firstname)) +
  geom_col(position = "dodge") +
  labs(title = "Most common male names over years",
       x = "Year",
       y = "Frequency") +
  theme(axis.text.x.bottom = element_text(angle = 90))

```


    
![png](output_24_0.png)
    


We will do the same for female names.


```R
female_names_by_year <- FirstNames %>%
  filter(Firstname != "_PRENOMS_RARES") %>%
  filter(Sex == 2 ) %>%
  group_by(Year, Firstname) %>%
  summarise(sum_number = sum(Number), .groups = 'drop')


most_given_female <- female_names_by_year %>%
  group_by(Year) %>%
  slice(which.max(sum_number))

head(most_given_female)
tail(most_given_female)


```


<table class="dataframe">
<caption>A grouped_df: 6 × 3</caption>
<thead>
	<tr><th scope=col>Year</th><th scope=col>Firstname</th><th scope=col>sum_number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>1900</td><td>MARIE</td><td>48713</td></tr>
	<tr><td>1901</td><td>MARIE</td><td>52150</td></tr>
	<tr><td>1902</td><td>MARIE</td><td>51857</td></tr>
	<tr><td>1903</td><td>MARIE</td><td>50424</td></tr>
	<tr><td>1904</td><td>MARIE</td><td>50131</td></tr>
	<tr><td>1905</td><td>MARIE</td><td>48981</td></tr>
</tbody>
</table>




<table class="dataframe">
<caption>A grouped_df: 6 × 3</caption>
<thead>
	<tr><th scope=col>Year</th><th scope=col>Firstname</th><th scope=col>sum_number</th></tr>
	<tr><th scope=col>&lt;dbl&gt;</th><th scope=col>&lt;chr&gt;</th><th scope=col>&lt;dbl&gt;</th></tr>
</thead>
<tbody>
	<tr><td>2016</td><td>EMMA</td><td>4723</td></tr>
	<tr><td>2017</td><td>EMMA</td><td>4815</td></tr>
	<tr><td>2018</td><td>EMMA</td><td>4372</td></tr>
	<tr><td>2019</td><td>EMMA</td><td>3953</td></tr>
	<tr><td>2020</td><td>JADE</td><td>3816</td></tr>
	<tr><td>2021</td><td>JADE</td><td>3798</td></tr>
</tbody>
</table>




```R
most_given_female <- most_given_female %>%
  filter(Year %% 5 == 0)

ggplot(most_given_female, aes(x = factor(Year), y = sum_number, fill = Firstname)) +
  geom_col(position = "dodge") +
  labs(title = "Most common female names over years",
       x = "Year",
       y = "Frequency") +
  theme(axis.text.x.bottom = element_text(angle = 90))

```


    
![png](output_27_0.png)
    


3. Optional : Which department has a larger variety of names along time ? Is there some sort of geographical correlation with the data?


```R

```
