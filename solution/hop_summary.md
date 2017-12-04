
# Heroes of Pymoli Data Analysis
* First Observation
* Second Observation
* Third Observation


```python
# import dependencies and mark options
from os import path
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings('ignore')
```


```python
# file path variable
json_file = path.join('raw_data', 'purchase_data.json')
```


```python
# read JSON file and preview it's contents
df = pd.read_json(json_file)
```

## Player Count


```python
# total number of players
player_ct = len(df['SN'].unique())

# generate the wee-bit player count datafram
total_player_ct_df = pd.DataFrame({'Total Players': player_ct}, index=['HOP Summary Value'])
total_player_ct_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>HOP Summary Value</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



## Unique Player DataFrame Generator
In order to get accurate summaries on any data that doesn't explicitly include purchasing items than it is imperative to use a unique dataframe because some users are duplicated and for that reason some data may be redundant.


```python
# unique player gender extractor
unique_players = df['SN'].unique()

unique_plyr_age = [list(df['Age'].loc[df['SN'] == str(i)]) for i in unique_players]
unique_plyr_age = [item[0] for item in unique_plyr_age]

unique_gender = [list(df['Gender'].loc[df['SN'] == str(i)]) for i in unique_players]
unique_gender = [item[0] for item in unique_gender]

# create a dataframe out of unique_index list
unique_player_df = pd.DataFrame({
    'uPlayer SN': unique_players,
    'uPlayer Gender': unique_gender,
    'uPlayer Age': unique_plyr_age
})
```

## Purchasing Analysis (Total)


```python
# purchase analysis
itemID_unique_ct = len(df['Item ID'].unique())
itemName_unique_ct = len(df['Item Name'].unique())

avg_item_cost = df['Price'].mean()
avg_item_cost = "${:.2f}".format(avg_item_cost)

num_purchases = len(df)

total_rev = df['Price'].sum()
total_rev = "${:.2f}".format(total_rev)

# generate purchasing analysis datafram
purchase_analysis_df = pd.DataFrame({
    'Number of Unique Items': itemName_unique_ct,
    'Average Price': avg_item_cost,
    'Number of Purchases': num_purchases,
    'Total Revenue': total_rev
}, index=['HOP Summary Value'], columns=[
    'Number of Unique Items',
    'Average Price',
    'Number of Purchases',
    'Total Revenue'
])
purchase_analysis_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>HOP Summary Value</th>
      <td>179</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



## Gender Demographics


```python
# gender demographics
male_ct = len(unique_player_df.loc[unique_player_df['uPlayer Gender'] == 'Male'])
female_ct = len(unique_player_df.loc[unique_player_df['uPlayer Gender'] == 'Female'])
other_gender_ct = len(unique_player_df.loc[unique_player_df['uPlayer Gender'] == 'Other / Non-Disclosed'])

male_perc = "{:.2f}%".format((male_ct/player_ct)*100)
female_perc = "{:.2f}%".format((female_ct/player_ct)*100)
other_perc = "{:.2f}%".format((other_gender_ct/player_ct)*100)

# generate gender anaylsis dataframe
gender_analysis_df = pd.DataFrame({
    'Percentage of Players': [male_perc, female_perc, other_perc],
    'Total Count': [male_ct, female_ct, other_gender_ct]
}, index=['Male', 'Female', 'Other / Non-Disclosed'])

gender_analysis_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>81.15%</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>17.45%</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40%</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Gender)
I am pretty sure the normalized total values are incorrect


```python
# purchasing gender count
p_male_ct = len(df.loc[df['Gender']=='Male'])
p_female_ct = len(df.loc[df['Gender']=='Female'])
p_other_gender_ct = len(df.loc[df['Gender']=='Other / Non-Disclosed'])

# avg gender purchase price
gender_avg_groupby_df = df.groupby(by='Gender').mean()
male_avg_pp = gender_avg_groupby_df['Price']['Male']
female_avg_pp = gender_avg_groupby_df['Price']['Female']
other_gender_avg_pp = gender_avg_groupby_df['Price']['Other / Non-Disclosed']

# total purchase value
gender_sum_groupby_df = df.groupby(by='Gender').sum()
male_sum_pp = gender_sum_groupby_df['Price']['Male']
female_sum_pp = gender_sum_groupby_df['Price']['Female']
other_gender_sum_pp = gender_sum_groupby_df['Price']['Other / Non-Disclosed']

# normalized totals
male_norm_totals = male_avg_pp * p_male_ct
female_norm_totals = female_avg_pp * p_female_ct
other_gender_norm_totals = other_gender_avg_pp * p_other_gender_ct

# format figures
format_list = [
    male_avg_pp, female_avg_pp, other_gender_avg_pp,
    male_sum_pp, female_sum_pp, other_gender_sum_pp,
    male_norm_totals, female_norm_totals, other_gender_norm_totals
]

formatted = ["${:.2f}".format(item) for item in format_list]


# generate the purchasing gender analysis dataframe
p_gender_analaysis_df = pd.DataFrame({
    'Purchase Count': [p_male_ct, p_female_ct, p_other_gender_ct],
    'Average Purchase Price': [formatted[0], formatted[1], formatted[2]],
    'Total Purchase Value': [formatted[3], formatted[4], formatted[5]],
    'Normalized Totals': [formatted[6], formatted[7], formatted[8]]
}, index=['Male','Female', 'Other / Non-Disclosed'], columns=[
    'Purchase Count',
    'Average Purchase Price',
    'Total Purchase Value',
    'Normalized Totals'
])
p_gender_analaysis_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$1867.68</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$382.91</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$35.74</td>
    </tr>
  </tbody>
</table>
</div>



### bins and labels for age feature


```python
# locate the max and min for player ages
u_min = unique_player_df['uPlayer Age'].min()
u_max = unique_player_df['uPlayer Age'].max()

# dynamic bin generator
bins = list(np.linspace(u_min - 1,u_max + 1,num=(u_max-u_min)/4, dtype=int))

# dynamic label generator
labels = ["<{}".format(bins[1])]
for k in range(len(bins)):
    labels.append("{0}-{1}".format(bins[k-1], bins[k]))
del labels[1:3]
del labels[-1]
labels.append("{}+".format(bins[-2]))
```

## Age Demographics


```python
# bin cut made to the unique_player_df 
unique_player_df['uPlayer Age Range'] = pd.cut(list(unique_player_df['uPlayer Age']), bins=bins, labels=labels)

# generate a new dataframe using the groupby method on players' ages
age_range_groupby_df = unique_player_df.groupby(by='uPlayer Age Range').count()

age_range_groupby_df = age_range_groupby_df[['uPlayer Age', 'uPlayer Gender']]
age_range_groupby_df = age_range_groupby_df.rename(columns={
    'uPlayer Age': 'Percentage of Players',
    'uPlayer Gender': 'Total Count'
})
age_perc = (age_range_groupby_df['Percentage of Players'] / player_ct)*100
age_range_groupby_df['Percentage of Players'] = age_perc.map("{:.2f}%".format)
del age_range_groupby_df.index.name
age_range_groupby_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;11</th>
      <td>4.71%</td>
      <td>27</td>
    </tr>
    <tr>
      <th>11-16</th>
      <td>11.87%</td>
      <td>68</td>
    </tr>
    <tr>
      <th>16-21</th>
      <td>26.88%</td>
      <td>154</td>
    </tr>
    <tr>
      <th>21-26</th>
      <td>36.30%</td>
      <td>208</td>
    </tr>
    <tr>
      <th>26-31</th>
      <td>9.42%</td>
      <td>54</td>
    </tr>
    <tr>
      <th>31-36</th>
      <td>6.46%</td>
      <td>37</td>
    </tr>
    <tr>
      <th>36-41</th>
      <td>3.84%</td>
      <td>22</td>
    </tr>
    <tr>
      <th>41+</th>
      <td>0.52%</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



## Purchasing Analysis (Age)
I am pretty sure the normalized totals values are incorrect


```python
df['Player Age Range'] = pd.cut(df['Age'], bins=bins, labels=labels)
p_age_range_groupby_ct_df = df.groupby(by='Player Age Range').count()
p_age_range_groupby_mn_df = df.groupby(by='Player Age Range').mean()
p_age_range_groupby_sm_df = df.groupby(by='Player Age Range').sum()
p_age_range_groupby_std_df = df.groupby(by='Player Age Range').std()

normalized = (p_age_range_groupby_sm_df['Price'] - p_age_range_groupby_mn_df['Price'])/p_age_range_groupby_std_df['Price']

# filter rows
p_age_range_groupby_mn_df = p_age_range_groupby_mn_df['Price'].map("${:.2f}".format)
p_age_range_groupby_sm_df = p_age_range_groupby_sm_df['Price'].map("${:.2f}".format)
normalized = normalized.map("${:.2f}".format)


p_age_range_groupby_ct_df = p_age_range_groupby_ct_df[['Age']]
p_age_range_groupby_ct_df = p_age_range_groupby_ct_df.rename(columns={'Age': 'Purchase Count'})
p_age_range_groupby_ct_df['Average Purchase Price'] = p_age_range_groupby_mn_df
p_age_range_groupby_ct_df['Total Purchase Value'] = p_age_range_groupby_sm_df
p_age_range_groupby_ct_df['Normalized Totals'] = normalized



del p_age_range_groupby_ct_df.index.name
p_age_range_groupby_ct_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;11</th>
      <td>41</td>
      <td>$3.01</td>
      <td>$123.38</td>
      <td>$108.71</td>
    </tr>
    <tr>
      <th>11-16</th>
      <td>92</td>
      <td>$2.81</td>
      <td>$258.10</td>
      <td>$231.31</td>
    </tr>
    <tr>
      <th>16-21</th>
      <td>204</td>
      <td>$2.88</td>
      <td>$588.40</td>
      <td>$525.26</td>
    </tr>
    <tr>
      <th>21-26</th>
      <td>275</td>
      <td>$2.96</td>
      <td>$814.07</td>
      <td>$714.52</td>
    </tr>
    <tr>
      <th>26-31</th>
      <td>79</td>
      <td>$2.98</td>
      <td>$235.61</td>
      <td>$200.83</td>
    </tr>
    <tr>
      <th>31-36</th>
      <td>49</td>
      <td>$3.08</td>
      <td>$150.78</td>
      <td>$146.21</td>
    </tr>
    <tr>
      <th>36-41</th>
      <td>37</td>
      <td>$2.90</td>
      <td>$107.35</td>
      <td>$92.61</td>
    </tr>
    <tr>
      <th>41+</th>
      <td>3</td>
      <td>$2.88</td>
      <td>$8.64</td>
      <td>$6.69</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
top_spender_sn_df = df.groupby(by='SN').sum().sort_values(['Price'], ascending=False)[['Price']]

top_spender_ct_df = df.groupby(by='SN').count()
top_spender_mn_df = df.groupby(by='SN').mean()


# list comprehensions for additional columns
tp_index = list(top_spender_sn_df.index)
tp_count = [top_spender_ct_df['Price'][element] for element in tp_index]
tp_mean = [top_spender_mn_df['Price'][element] for element in tp_index]

# top_spender dataframe build
top_spender_sn_df['Purchase Count'] = tp_count
top_spender_sn_df['Average Purchase Price'] = tp_mean

top_spender_sn_df = top_spender_sn_df.rename(columns={'Price':'Total Purchase Value'})
top_spender_sn_df = top_spender_sn_df[[
    'Purchase Count',
    'Average Purchase Price',
    'Total Purchase Value'
]]

# format dataframe
top_spender_sn_df['Average Purchase Price'] = top_spender_sn_df['Average Purchase Price'].map("${:.2f}".format)
top_spender_sn_df['Total Purchase Value'] = top_spender_sn_df['Total Purchase Value'].map("${:.2f}".format)

top_spender_sn_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
popular_items_id_df = df.groupby(by=['Item ID','Item Name']).count().sort_values(['Price'], ascending=False)

popular_items_sm_df = df.groupby(by=['Item ID','Item Name']).sum()
popular_items_mn_df = df.groupby(by=['Item ID','Item Name']).mean()

# list comprehensions for additional columns
pop_index = list(popular_items_id_df.index)
pop_sum = [popular_items_sm_df['Price'][element] for element in pop_index]
pop_mean = [popular_items_mn_df['Price'][element] for element in pop_index]

# popular_items dataframe build
popular_items_id_df['Item Price'] = pop_mean
popular_items_id_df['Total Purchase Value'] = pop_sum

popular_items_id_df = popular_items_id_df.rename(columns={'Price':'Purchase Count'})
popular_items_id_df = popular_items_id_df[[
    'Purchase Count',
    'Item Price',
    'Total Purchase Value'
]]

# format dataframe
popular_items_id_df['Item Price'] = popular_items_id_df['Item Price'].map("${:.2f}".format)
popular_items_id_df['Total Purchase Value'] = popular_items_id_df['Total Purchase Value'].map("${:.2f}".format)

popular_items_id_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
profitable_items_id_df = df.groupby(by=['Item ID','Item Name']).sum().sort_values(['Price'], ascending=False)

profitable_items_ct_df = df.groupby(by=['Item ID','Item Name']).count()
profitable_items_mn_df = df.groupby(by=['Item ID','Item Name']).mean()

# list comprehensions for additional columns
prof_index = list(profitable_items_id_df.index)
prof_count = [profitable_items_ct_df['Price'][element] for element in prof_index]
prof_mean = [profitable_items_mn_df['Price'][element] for element in prof_index]

# popular_items dataframe build
profitable_items_id_df['Purchase Count'] = prof_count
profitable_items_id_df['Item Price'] = prof_mean

profitable_items_id_df = profitable_items_id_df.rename(columns={'Price':'Total Purchase Value'})
profitable_items_id_df = profitable_items_id_df[[
    'Purchase Count',
    'Item Price',
    'Total Purchase Value'
]]

# format dataframe
profitable_items_id_df['Item Price'] = profitable_items_id_df['Item Price'].map("${:.2f}".format)
profitable_items_id_df['Total Purchase Value'] = profitable_items_id_df['Total Purchase Value'].map("${:.2f}".format)

profitable_items_id_df.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


