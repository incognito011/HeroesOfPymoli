
# Heroes Of Pymoli Data Analysis

* Of the 576 active players, the vast majority are male (84%). There also exists, a smaller, but notable proportion of female players (14%).

* Our peak age demographic falls between 20-24 with secondary groups falling between 15-19 and 25-29. Within the peak demographic group most purchases are made by 20 year olds at 12.0% of purchases, followed by 23 and 22 at 8.5%


```python
# Dependencies and Setup
import pandas as pd
import numpy as np

# Raw data file
file_to_load = "Resources/purchase_data.csv"

# Read purchasing file and store into pandas data frame
purchase_data = pd.read_csv(file_to_load)
```

# Player Count


```python
# Calculate the number of total players in the DataFrame
total_players = len(purchase_data["SN"].unique())

# Place all of the data found into a count DataFrame
count_table = pd.DataFrame({"Total Players": [total_players]})
count_table
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
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>576</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
# Calculate the number of unique items in the DataFrame
num_items = len(purchase_data["Item ID"].unique())


# Calculate the average item price
avg_price = purchase_data["Price"].mean()


# Calculate the number of purchases
num_purchases = purchase_data["Purchase ID"].count()


# Calculate the total revenue
total_revenue = purchase_data["Price"].sum()

# Place all of the data found into a summary DataFrame
summary_table = pd.DataFrame({"Number of Unique Items": [num_items],
                             "Average Price": [avg_price],
                             "Number of Purchases": [num_purchases],
                             "Total Revenue": [total_revenue]
                             })
# Use Map to format money columns
summary_table["Average Price"] = summary_table["Average Price"].map("${:.2f}".format)
summary_table["Total Revenue"] = summary_table["Total Revenue"].map("${:.2f}".format)

summary_table
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
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$3.05</td>
      <td>780</td>
      <td>183</td>
      <td>$2379.77</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
# create a list of non duplicate screen names
no_dup_sn = purchase_data.drop_duplicates(["SN"], keep ='last')

# count gender
count_gender = no_dup_sn["Gender"].value_counts().reset_index()
count_gender["Persentage of Players"] = count_gender["Gender"] / total_players * 100
count_gender.rename(columns = {"index": "Gender", "Gender": "Total Count"}, inplace = True)
count_gender["Persentage of Players"] = count_gender["Persentage of Players"].map("{:.2f}%".format)
count_gender


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
      <th>Gender</th>
      <th>Total Count</th>
      <th>Persentage of Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Male</td>
      <td>484</td>
      <td>84.03%</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Female</td>
      <td>81</td>
      <td>14.06%</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>11</td>
      <td>1.91%</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
# counts purchases by gender
pur_ct_gen = pd.DataFrame(purchase_data.groupby("Gender")["Gender"].count().reset_index(name = "Purchase Count"))

# total purchases by gender
pur_tot_gen = pd.DataFrame(purchase_data.groupby("Gender")["Price"].sum().reset_index(name = "Total Purchase Value"))

# avg purchase price by gender
pur_avg_gen = pd.DataFrame(purchase_data.groupby("Gender")["Price"].mean().reset_index(name = "Average Purchase Price"))

# merge first two tables
merge_table1 = pd.merge(pur_ct_gen, pur_tot_gen , on="Gender")
merge_table1["Normalized Totals"] = merge_table1["Total Purchase Value"] / merge_table1["Purchase Count"]

# merge last table
merge_table = pd.merge(merge_table1, pur_avg_gen , on="Gender")
# format
merge_table["Total Purchase Value"] = merge_table["Total Purchase Value"].map("${:.2f}".format)
merge_table["Average Purchase Price"] = merge_table["Average Purchase Price"].map("${:.2f}".format)
merge_table["Normalized Totals"] = merge_table["Normalized Totals"].map("${:.2f}".format)

merge_table

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
      <th>Gender</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
      <th>Average Purchase Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Female</td>
      <td>113</td>
      <td>$361.94</td>
      <td>$3.20</td>
      <td>$3.20</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Male</td>
      <td>652</td>
      <td>$1967.64</td>
      <td>$3.02</td>
      <td>$3.02</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Other / Non-Disclosed</td>
      <td>15</td>
      <td>$50.19</td>
      <td>$3.35</td>
      <td>$3.35</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Age)


```python
# get data for age groups
age_groups= pd.DataFrame(purchase_data)

# Establish bins for ages
age_bins = [0, 9.90, 14.90, 19.90, 24.90, 29.90, 34.90, 39.90, 99999]
group_names = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

age_groups["View Group"] = pd.cut(age_groups["Age"], age_bins, labels=group_names)

# Creating a group based off of the bins

# Purchase Count by age
age_purchases = pd.DataFrame(age_groups.groupby("View Group")["Purchase ID"].count().reset_index(name = "Purchase Count"))


# Average purchase price by age
age_avg_pur = pd.DataFrame(age_groups.groupby("View Group")["Price"].mean().reset_index(name = "Average Purchase Price"))


# Total Purchase Value by age
age_total_val = pd.DataFrame(age_groups.groupby("View Group")["Price"].sum().reset_index(name = "Total Purchase Value"))


# merge first two tables
merge_age_tbl1 = pd.merge(age_purchases, age_total_val , on="View Group")
merge_age_tbl1["Normalized Totals"] = merge_age_tbl1["Total Purchase Value"] / merge_age_tbl1["Purchase Count"]

# merge last table
merge_age_tbl = pd.merge(merge_age_tbl1, age_avg_pur , on="View Group")

# format
merge_age_tbl["Total Purchase Value"] = merge_age_tbl["Total Purchase Value"].map("${:.2f}".format)
merge_age_tbl["Average Purchase Price"] = merge_age_tbl["Average Purchase Price"].map("${:.2f}".format)
merge_age_tbl["Normalized Totals"] = merge_age_tbl["Normalized Totals"].map("${:.2f}".format)

merge_age_tbl

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
      <th>View Group</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
      <th>Average Purchase Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>&lt;10</td>
      <td>23</td>
      <td>$77.13</td>
      <td>$3.35</td>
      <td>$3.35</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10-14</td>
      <td>28</td>
      <td>$82.78</td>
      <td>$2.96</td>
      <td>$2.96</td>
    </tr>
    <tr>
      <th>2</th>
      <td>15-19</td>
      <td>136</td>
      <td>$412.89</td>
      <td>$3.04</td>
      <td>$3.04</td>
    </tr>
    <tr>
      <th>3</th>
      <td>20-24</td>
      <td>365</td>
      <td>$1114.06</td>
      <td>$3.05</td>
      <td>$3.05</td>
    </tr>
    <tr>
      <th>4</th>
      <td>25-29</td>
      <td>101</td>
      <td>$293.00</td>
      <td>$2.90</td>
      <td>$2.90</td>
    </tr>
    <tr>
      <th>5</th>
      <td>30-34</td>
      <td>73</td>
      <td>$214.00</td>
      <td>$2.93</td>
      <td>$2.93</td>
    </tr>
    <tr>
      <th>6</th>
      <td>35-39</td>
      <td>41</td>
      <td>$147.67</td>
      <td>$3.60</td>
      <td>$3.60</td>
    </tr>
    <tr>
      <th>7</th>
      <td>40+</td>
      <td>13</td>
      <td>$38.24</td>
      <td>$2.94</td>
      <td>$2.94</td>
    </tr>
  </tbody>
</table>
</div>



## Top Spenders


```python
#Purchase Count by screen name
sn_purchases = pd.DataFrame(purchase_data.groupby("SN")["Purchase ID"].count().reset_index(name = "Purchase Count"))


#Average purchase price by screen name
sn_avg_pur = pd.DataFrame(purchase_data.groupby("SN")["Price"].mean().reset_index(name = "Average Purchase Price"))


# Total Purchase Value by screen name
sn_total_val = pd.DataFrame(purchase_data.groupby("SN")["Price"].sum().reset_index(name = "Total Purchase Value"))

# merge first two tables
merge_sn_tbl1 = pd.merge(sn_purchases, sn_avg_pur , on="SN")

# merge last table
merge_sn_tbl = pd.merge(merge_sn_tbl1, sn_total_val , on="SN")
merge_sn_tbl = merge_sn_tbl.sort_values("Total Purchase Value", ascending=False)

# format
merge_sn_tbl["Total Purchase Value"] = merge_sn_tbl["Total Purchase Value"].map("${:.2f}".format)
merge_sn_tbl["Average Purchase Price"] = merge_sn_tbl["Average Purchase Price"].map("${:.2f}".format)

# print top 5 
merge_sn_tbl.head(5)

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
      <th>SN</th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>360</th>
      <td>Lisosia93</td>
      <td>5</td>
      <td>$3.79</td>
      <td>$18.96</td>
    </tr>
    <tr>
      <th>246</th>
      <td>Idastidru52</td>
      <td>4</td>
      <td>$3.86</td>
      <td>$15.45</td>
    </tr>
    <tr>
      <th>106</th>
      <td>Chamjask73</td>
      <td>3</td>
      <td>$4.61</td>
      <td>$13.83</td>
    </tr>
    <tr>
      <th>275</th>
      <td>Iral74</td>
      <td>4</td>
      <td>$3.40</td>
      <td>$13.62</td>
    </tr>
    <tr>
      <th>281</th>
      <td>Iskadarya95</td>
      <td>3</td>
      <td>$4.37</td>
      <td>$13.10</td>
    </tr>
  </tbody>
</table>
</div>



## Most Popular Items


```python
# Create items table
items_table = purchase_data[["Item ID", "Item Name", "Price"]]

# Create the GroupBy object based on the "Item ID" and "Item Name" column
items_group = pd.DataFrame(items_table.groupby(["Item ID", "Item Name", "Price"])["Item ID"].count().reset_index(name = "Purchase Count"))
items_group["Total Purchase Value"] = items_group["Purchase Count"] * items_group["Price"]

# Sort Items from high to low
items_sort = items_group.sort_values("Purchase Count", ascending=False)

# format
items_sort["Total Purchase Value"] = items_sort["Total Purchase Value"].map("${:.2f}".format)
items_sort["Price"] = items_sort["Price"].map("${:.2f}".format)

# Reorganizing the columns using double brackets
items_sort = items_sort[["Item ID", "Item Name", "Purchase Count", "Price","Total Purchase Value"]]


items_sort.head(5)

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
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>177</th>
      <td>178</td>
      <td>Oathbreaker, Last Hope of the Breaking Storm</td>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>144</th>
      <td>145</td>
      <td>Fiery Glass Crusader</td>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>108</td>
      <td>Extraction, Quickblade Of Trembling Hands</td>
      <td>9</td>
      <td>$3.53</td>
      <td>$31.77</td>
    </tr>
    <tr>
      <th>81</th>
      <td>82</td>
      <td>Nirvana</td>
      <td>9</td>
      <td>$4.90</td>
      <td>$44.10</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>Pursuit, Cudgel of Necromancy</td>
      <td>8</td>
      <td>$1.02</td>
      <td>$8.16</td>
    </tr>
  </tbody>
</table>
</div>



## Most Profitable Items


```python
# Sort Items from high to low
items_sort_profit = items_group.sort_values("Total Purchase Value", ascending=False)

# format
items_sort_profit["Total Purchase Value"] = items_sort_profit["Total Purchase Value"].map("${:.2f}".format)
items_sort_profit["Price"] = items_sort_profit["Price"].map("${:.2f}".format)

# Reorganizing the columns using double brackets
items_sort_profit = items_sort_profit[["Item ID", "Item Name", "Purchase Count", "Price","Total Purchase Value"]]


items_sort_profit.head(5)

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
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Purchase Count</th>
      <th>Price</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>177</th>
      <td>178</td>
      <td>Oathbreaker, Last Hope of the Breaking Storm</td>
      <td>12</td>
      <td>$4.23</td>
      <td>$50.76</td>
    </tr>
    <tr>
      <th>81</th>
      <td>82</td>
      <td>Nirvana</td>
      <td>9</td>
      <td>$4.90</td>
      <td>$44.10</td>
    </tr>
    <tr>
      <th>144</th>
      <td>145</td>
      <td>Fiery Glass Crusader</td>
      <td>9</td>
      <td>$4.58</td>
      <td>$41.22</td>
    </tr>
    <tr>
      <th>91</th>
      <td>92</td>
      <td>Final Critic</td>
      <td>8</td>
      <td>$4.88</td>
      <td>$39.04</td>
    </tr>
    <tr>
      <th>102</th>
      <td>103</td>
      <td>Singed Scalpel</td>
      <td>8</td>
      <td>$4.35</td>
      <td>$34.80</td>
    </tr>
  </tbody>
</table>
</div>


