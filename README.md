# **Manufacture-defect**
## **1. Purpose and outcome:**

#### **Purpose:**

This project purpose is to identify defect patterns and cost of each type of defects.

#### **Outcome**:

The result of this project is to analyze defect patterns and trends in manufacturing processes, Identify areas for process optimization to reduce defect rates.

## **2. Dataset**

This data was published on Kaggle and contains simulated data related to manufacturing defects observed during quality control processes.

It includes information such as defect type, detection date, location within the product, severity level, inspection method used, and repair costs.

This dataset can be used for analyzing defect patterns, improving quality control processes, and assessing the impact of defects on product quality and production costs.

**Structure:**

- **defect_id**: Unique identifier for each defect.

- **product_id**: Identifier for the product associated with the defect.

- **defect_type**: Type or category of the defect (e.g., cosmetic, functional, structural).

- **defect_description**: Description of the defect.

- **defect_date**: Date when the defect was detected.

- **defect_location**: Location within the product where the defect was found (e.g., surface, component).
- **severity**: Severity level of the defect (e.g., minor, moderate, critical).

- **inspection_method**: Method used to detect the defect (e.g., visual inspection, automated testing).

- **repair_action**: Action taken to repair or address the defect.

- **repair_cost**: Cost incurred to repair the defect (in local currency).

## **3. Data Import**

Import library

    import pandas as pd
    import numpy as np
    import seaborn as sns
    import matplotlib.pyplot as plt
    import statsmodels.api as sm

Loading dataset from Google docs

[In]
```
defect_data='1ivKij1HC8xH_ck-UB2JuGgJlE74WjctAu8ltFskk11k'
url='https://docs.google.com/spreadsheets/d/' + defect_data + '/export?format=xlsx'
df_defect=pd.read_excel(url)

df_defect.head()  # test the dataset loaded
```
[Out]

|    |   defect_id |   product_id | defect_type   | defect_date         | defect_location   | severity   | inspection_method   |   repair_cost |
|---:|------------:|-------------:|:--------------|:--------------------|:------------------|:-----------|:--------------------|--------------:|
|  0 |           1 |           15 | Structural    | 2024-06-06 00:00:00 | Component         | Minor      | Visual Inspection   |        245.47 |
|  1 |           2 |            6 | Functional    | 2024-04-26 00:00:00 | Component         | Minor      | Visual Inspection   |         26.87 |
|  2 |           3 |           84 | Structural    | 2024-02-15 00:00:00 | Internal          | Minor      | Automated Testing   |        835.81 |
|  3 |           4 |           10 | Functional    | 2024-03-28 00:00:00 | Internal          | Critical   | Automated Testing   |        444.47 |
|  4 |           5 |           14 | Cosmetic      | 2024-04-26 00:00:00 | Component         | Minor      | Manual Testing      |        823.64 |

## **4. Data Cleaning**

### **Checking structure of dataset**

Show the columns of the dataframe and their types:

[In]
```
df_defect.info()
```
[Out]

![image](https://github.com/user-attachments/assets/998b0fab-e7a1-43ee-bfdc-8205dd8a2683)

### **Checking number of Missing values & Duplicate Rows**

Checking data missing

[In]
```
df_defect.isna().sum()
```
[Out]

|                   |   0 |
|:------------------|----:|
| defect_id         |   0 |
| product_id        |   0 |
| defect_type       |   0 |
| defect_date       |   0 |
| defect_location   |   0 |
| severity          |   0 |
| inspection_method |   0 |
| repair_cost       |   0 |

Cheking data is duplicated

[In]
```
df_defect.duplicated().sum()
```
[Out]

```
0
```

Checking data is null:

[In]
```
df_defect.isnull().sum().to_markdown()
```

[Out]

|                   |   0 |
|:------------------|----:|
| defect_id         |   0 |
| product_id        |   0 |
| defect_type       |   0 |
| defect_date       |   0 |
| defect_location   |   0 |
| severity          |   0 |
| inspection_method |   0 |
| repair_cost       |   0 |


Checking data is unique:

[In]

```
df_defect.nunique()

```

[Out]

|                   |    0 |
|:------------------|-----:|
| defect_id         | 1000 |
| product_id        |  100 |
| defect_type       |    3 |
| defect_date       |  182 |
| defect_location   |    3 |
| severity          |    3 |
| inspection_method |    3 |
| repair_cost       |  996 |

### **Checking consistency of data**

Checking "objected" columns is unique

[In]
```
for col in ['defect_type', 'defect_location', 'severity', 'inspection_method']:
  print(f"column: '{col}': {df_defect[col].unique()}")
```

[Out]

![image](https://github.com/user-attachments/assets/1cb738d3-80a5-4fec-a47d-b22f99eb370f)

Change format of defect_date to yyyy-mm-dd

[In]
```
df_defect['defect_date']=pd.to_datetime(df_defect['defect_date'])
df_defect.head()
```

[Out]

|    |   defect_id |   product_id | defect_type   | defect_date         | defect_location   | severity   | inspection_method   |   repair_cost |
|---:|------------:|-------------:|:--------------|:--------------------|:------------------|:-----------|:--------------------|--------------:|
|  0 |           1 |           15 | Structural    | 2024-06-06 00:00:00 | Component         | Minor      | Visual Inspection   |        245.47 |
|  1 |           2 |            6 | Functional    | 2024-04-26 00:00:00 | Component         | Minor      | Visual Inspection   |         26.87 |
|  2 |           3 |           84 | Structural    | 2024-02-15 00:00:00 | Internal          | Minor      | Automated Testing   |        835.81 |
|  3 |           4 |           10 | Functional    | 2024-03-28 00:00:00 | Internal          | Critical   | Automated Testing   |        444.47 |
|  4 |           5 |           14 | Cosmetic      | 2024-04-26 00:00:00 | Component         | Minor      | Manual Testing      |        823.64 |


Add new column "month" to database for further used:

[In]
```
df_defect['month']=df_defect['defect_date'].dt.strftime('%Y-%m')
df_defect.head()

```
[Out]

|    |   defect_id |   product_id | defect_type   | defect_date         | defect_location   | severity   | inspection_method   |   repair_cost | month   |
|---:|------------:|-------------:|:--------------|:--------------------|:------------------|:-----------|:--------------------|--------------:|:--------|
|  0 |           1 |           15 | Structural    | 2024-06-06 00:00:00 | Component         | Minor      | Visual Inspection   |        245.47 | 2024-06 |
|  1 |           2 |            6 | Functional    | 2024-04-26 00:00:00 | Component         | Minor      | Visual Inspection   |         26.87 | 2024-04 |
|  2 |           3 |           84 | Structural    | 2024-02-15 00:00:00 | Internal          | Minor      | Automated Testing   |        835.81 | 2024-02 |
|  3 |           4 |           10 | Functional    | 2024-03-28 00:00:00 | Internal          | Critical   | Automated Testing   |        444.47 | 2024-03 |
|  4 |           5 |           14 | Cosmetic      | 2024-04-26 00:00:00 | Component         | Minor      | Manual Testing      |        823.64 | 2024-04 |

Change dtype of columns from type 'Object' to type 'Category'

[In]
```
df_defect[['defect_type','defect_location','severity','inspection_method']] = df_defect[['defect_type','defect_location','severity','inspection_method']].astype('category')
df_defect.info()
```

[Out]

![image](https://github.com/user-attachments/assets/4a49f08d-5b0d-40b4-a490-7d8ebb90e809)


### **Summary Statistics**

Show a descriptive statistics of the numeric columns

[In]
```
df_defect[['defect_id','product_id','repair_cost']].describe()
```

[Out]

|       |   defect_id |   product_id |   repair_cost |
|:------|------------:|-------------:|--------------:|
| count |    1000     |    1000      |      1000     |
| mean  |     500.5   |      50.837  |       507.627 |
| std   |     288.819 |      29.4809 |       289.624 |
| min   |       1     |       1      |        10.22  |
| 25%   |     250.75  |      26      |       270.903 |
| 50%   |     500.5   |      51      |       506.43  |
| 75%   |     750.25  |      77      |       759.065 |
| max   |    1000     |     100      |       999.64  |


Show a descriptive statistics of the non-numeric columns

[In]
```
df_defect[['defect_type', 'defect_location', 'severity', 'inspection_method']].describe()
```

[Out]

|        | defect_type   | defect_location   | severity   | inspection_method   |
|:-------|:--------------|:------------------|:-----------|:--------------------|
| count  | 1000          | 1000              | 1000       | 1000                |
| unique | 3             | 3                 | 3          | 3                   |
| top    | Structural    | Surface           | Minor      | Manual Testing      |
| freq   | 352           | 353               | 358        | 352                 |


## **5. Exploratory Data Analysis**

### **Monthly Defects Occurred**

Create column chart to show total defect of each month, line chart to show the fluctuation of defect by month

```
# Group by month and count the number of defects
defect_count_by_month = df_defect.groupby('month')['defect_id'].count().reset_index()

# Sort the data by month
defect_count_by_month = defect_count_by_month.sort_values('month')

# Create the bar chart
plt.figure(figsize=(8, 6))
ax = sns.barplot(x='month', y='defect_id',hue='month', legend=False, data=defect_count_by_month, palette='viridis')

# Add data labels to the bars
for p in ax.patches:
    ax.annotate(f'{p.get_height()}', (p.get_x() + p.get_width() / 2., p.get_height()),
                ha='center', va='center', xytext=(0, 10), textcoords='offset points')

# Set chart title and labels
plt.title('Total Defects per Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Number of Defects', fontsize=14)

# Create the line chart
plt.figure(figsize=(8, 6))
ax = sns.lineplot(x='month', y='defect_id', data=defect_count_by_month, marker='o')

# Add data labels to the points

for x, y in zip(defect_count_by_month['month'], defect_count_by_month['defect_id']):
    plt.text(x, y, f'{y:.2f}', ha='center', va='bottom')

# Set chart title and labels
plt.title('Total defect per Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Number of Defects', fontsize=14)


plt.xticks(rotation=45)  # Rotate x-axis labels for better readability

plt.show()
```
[Out]

![image](https://github.com/user-attachments/assets/6e7898d1-3f68-4b9c-9946-b3bf58cbda63)

![image](https://github.com/user-attachments/assets/2a091305-94b2-4994-a413-f3381bff6b43)


### **Monthly Repair cost**

Create column chart to show total repair cost of each month, line chart to show the fluctuation of repair cost by month

[In]
```
# Group by month and sum the repair cost
repair_cost_by_month = df_defect.groupby('month')['repair_cost'].sum().reset_index()

# Sort the data by month
repair_cost_by_month = repair_cost_by_month.sort_values('month')

# Create the bar chart
plt.figure(figsize=(8, 6))
ax = sns.barplot(x='month', y='repair_cost', hue='month', legend=False, data=repair_cost_by_month, palette='viridis')

# Add data labels to the bars
for p in ax.patches:
    ax.annotate(f'{p.get_height():.2f}', (p.get_x() + p.get_width() / 2., p.get_height()),
                ha='center', va='center', xytext=(0, 10), textcoords='offset points')

# Set chart title and labels
plt.title('Total Repair Cost per Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Total Repair Cost', fontsize=14)

# Create the line chart
plt.figure(figsize=(8, 6))
ax = sns.lineplot(x='month', y='repair_cost', data=repair_cost_by_month, marker='o')

# Add data labels to the points

for x, y in zip(repair_cost_by_month['month'], repair_cost_by_month['repair_cost']):
    plt.text(x, y, f'{y:.2f}', ha='center', va='bottom')

# Set chart title and labels
plt.title('Repair Cost per Month', fontsize=16)
plt.xlabel('Month', fontsize=14)
plt.ylabel('Total Repair Cost', fontsize=14)

plt.xticks(rotation=45)  # Rotate x-axis labels for better readability

plt.show()
```

[Out]

![image](https://github.com/user-attachments/assets/5495d6f3-60b2-4013-aa6f-7911f72328b9)

![image](https://github.com/user-attachments/assets/0c497d9b-0f4c-461f-9f85-11ab2c097928)


### **Inspection Method, Type, location and Severity Level of Defects**

Create column chart to show the distribution of defect by defect type, defect location, and severity level:

[In]
```
fig, axs = plt.subplots(2, 2, figsize=(10,8))
sns.set_style('darkgrid')

# Inspection Method
sns.countplot(x=df_defect['inspection_method'], hue=df_defect['inspection_method'], legend=False, palette='dark', ax=axs[1,1])

axs[1, 1].set_title('Number of Inspection Methods', fontsize = 13, fontweight = 'bold', color = 'darkblue')
axs[1, 1].set_xlabel('Inspection Methods',fontsize = 11, fontweight = 'bold')

# Defect Type
sns.countplot(x=df_defect['defect_type'], hue=df_defect['defect_type'], legend=False, palette='viridis', ax=axs[0,0])
axs[0, 0].set_title('Number of Defect Type', fontsize = 13, fontweight = 'bold', color = 'darkblue')
axs[0, 0].set_xlabel('Defect Type',fontsize = 11, fontweight = 'bold')

# Defect Location
sns.countplot(x=df_defect['defect_location'],hue=df_defect['defect_location'], legend=False, palette='husl', ax=axs[0,1])
axs[0, 1].set_title('Number of Defect Location', fontsize = 13, fontweight = 'bold', color = 'darkblue')
axs[0, 1].set_xlabel('Defect Location',fontsize = 11, fontweight = 'bold')

# Severity
sns.countplot(x=df_defect['severity'], hue=df_defect['severity'], legend=False, palette='colorblind', ax=axs[1,0])
axs[1, 0].set_title('Number of Severity', fontsize = 13, fontweight = 'bold', color = 'darkblue')
axs[1, 0].set_xlabel('Severity',fontsize = 11, fontweight = 'bold')

plt.tight_layout()
```

[Out]

![image](https://github.com/user-attachments/assets/00307952-5fc3-4346-accf-f84111a9da9e)

### **6. Calculate Correlation**

Create dataframe to calculate repair cost group by category defect_type, defect_location, severity level by inspection methods.

[In]
```
category_count = df_defect.groupby(['defect_type','severity','defect_location','inspection_method'])['repair_cost'].sum().unstack()
category_count

```

[Out]

|                                         |   Automated Testing |   Manual Testing |   Visual Inspection |
|:----------------------------------------|--------------------:|-----------------:|--------------------:|
| ('Cosmetic', 'Component', 'Critical')   |             5065.1  |          6317.62 |             5799.37 |
| ('Cosmetic', 'Component', 'Minor')      |             5033.12 |          7604.65 |            10020.6  |
| ('Cosmetic', 'Component', 'Moderate')   |             6545.01 |          4867.92 |             5163.57 |
| ('Cosmetic', 'Internal', 'Critical')    |             5949.23 |          3716.45 |             4902.45 |
| ('Cosmetic', 'Internal', 'Minor')       |             2715.66 |          8822.51 |             4249.15 |
| ('Cosmetic', 'Internal', 'Moderate')    |             3813    |          7687.11 |             4531.89 |
| ('Cosmetic', 'Surface', 'Critical')     |             5514.93 |          8265.16 |             4645.12 |
| ('Cosmetic', 'Surface', 'Minor')        |             6384.3  |          5674.1  |             7122.67 |
| ('Cosmetic', 'Surface', 'Moderate')     |             4449.76 |          3326.67 |            10610.6  |
| ('Functional', 'Component', 'Critical') |             7593.2  |          9441.8  |             6012.16 |
| ('Functional', 'Component', 'Minor')    |             5184.64 |          5468.59 |             4702.54 |
| ('Functional', 'Component', 'Moderate') |             6805.24 |          4383.65 |            10189.5  |
| ('Functional', 'Internal', 'Critical')  |             8275.06 |          8174.14 |             6290.79 |
| ('Functional', 'Internal', 'Minor')     |             3715.22 |          6832.03 |             8671.25 |
| ('Functional', 'Internal', 'Moderate')  |             2768.2  |          8083.24 |             6385.65 |
| ('Functional', 'Surface', 'Critical')   |             4706.45 |          7971.66 |             3982.65 |
| ('Functional', 'Surface', 'Minor')      |             7247.61 |          7257.65 |             3722.7  |
| ('Functional', 'Surface', 'Moderate')   |             4903.47 |          8353.34 |             4783.15 |
| ('Structural', 'Component', 'Critical') |             2988.29 |          4909    |             7940.65 |
| ('Structural', 'Component', 'Minor')    |             5657.8  |          8126.9  |             8872.19 |
| ('Structural', 'Component', 'Moderate') |             7118.71 |          6048.24 |             5837.54 |
| ('Structural', 'Internal', 'Critical')  |             6421.75 |          3764.74 |             8053.39 |
| ('Structural', 'Internal', 'Minor')     |             9004.41 |          9837.73 |             6303.93 |
| ('Structural', 'Internal', 'Moderate')  |             3655.8  |          4175.53 |             5127.02 |
| ('Structural', 'Surface', 'Critical')   |             7319.33 |          9790.41 |             4644.35 |
| ('Structural', 'Surface', 'Minor')      |             5762.41 |         11500.3  |             8672.36 |
| ('Structural', 'Surface', 'Moderate')   |             4438.18 |          4777.16 |             6175.74 |


Create heatmap of repair cost by category

[In]
```
plt.figure(figsize=(10, 8))
sns.heatmap(category_count, annot=True, fmt=".2f", cmap="coolwarm")
plt.title('Heatmap of Repair Cost by Category', fontsize=16)
plt.xlabel('Inspection Method', fontsize=14)
plt.ylabel('Defect Type, Severity, Defect Location', fontsize=14)
plt.show()
```
[Out]

![image](https://github.com/user-attachments/assets/7227e1c1-a310-4890-b3cc-5853523c20b8)

**Key Insights**

**High-Cost Areas:**

Cosmetic-Component-Minor under Visual Inspection shows a high cost (10020.56).

Functional-Component-Critical under Manual Testing also has a high cost (9441.80).

Structural-Surface-Minor under Manual Testing is notably high (11500.29).

**Low-Cost Areas:**

Cosmetic-Internal-Minor under Automated Testing shows a lower cost (2715.66).

Functional-Internal-Moderate under Automated Testing is relatively low (2768.20).

**Inspection Method Trends:**

Manual Testing generally shows higher costs across various defect types compared to Automated Testing and Visual Inspection.

Automated Testing tends to have lower costs, indicating it might be more cost-effective for certain defect types.

**Calculate and interpret correlations between variables in the dataset**

One hot coding for non numeric columns

[In]
```
df_defect_encode = pd.get_dummies(df_defect, columns=['defect_type', 'defect_location', 'severity', 'inspection_method'])
df_defect_encode.head()

```
[Out]

|    |   defect_id |   product_id | defect_date         |   repair_cost | month   | defect_type_Cosmetic   | defect_type_Functional   | defect_type_Structural   | defect_location_Component   | defect_location_Internal   | defect_location_Surface   | severity_Critical   | severity_Minor   | severity_Moderate   | inspection_method_Automated Testing   | inspection_method_Manual Testing   | inspection_method_Visual Inspection   |
|---:|------------:|-------------:|:--------------------|--------------:|:--------|:-----------------------|:-------------------------|:-------------------------|:----------------------------|:---------------------------|:--------------------------|:--------------------|:-----------------|:--------------------|:--------------------------------------|:-----------------------------------|:--------------------------------------|
|  0 |           1 |           15 | 2024-06-06 00:00:00 |        245.47 | 2024-06 | False                  | False                    | True                     | True                        | False                      | False                     | False               | True             | False               | False                                 | False                              | True                                  |
|  1 |           2 |            6 | 2024-04-26 00:00:00 |         26.87 | 2024-04 | False                  | True                     | False                    | True                        | False                      | False                     | False               | True             | False               | False                                 | False                              | True                                  |
|  2 |           3 |           84 | 2024-02-15 00:00:00 |        835.81 | 2024-02 | False                  | False                    | True                     | False                       | True                       | False                     | False               | True             | False               | True                                  | False                              | False                                 |
|  3 |           4 |           10 | 2024-03-28 00:00:00 |        444.47 | 2024-03 | False                  | True                     | False                    | False                       | True                       | False                     | True                | False            | False               | True                                  | False                              | False                                 |
|  4 |           5 |           14 | 2024-04-26 00:00:00 |        823.64 | 2024-04 | True                   | False                    | False                    | True                        | False                      | False                     | False               | True             | False               | False                                 | True                               | False                                 |

Remove object columns for correlated calculation

[In]
```
del df_defect_encode['month']
df_defect_encode.info()
```

[Out]

![image](https://github.com/user-attachments/assets/969e6634-4835-4932-abf8-8bdd266408bc)

Calculate correlation:

[In]
```
df_defect_encode.corr()
```

[Out]

|                                     |   defect_id |   product_id |   defect_date |   repair_cost |   defect_type_Cosmetic |   defect_type_Functional |   defect_type_Structural |   defect_location_Component |   defect_location_Internal |   defect_location_Surface |   severity_Critical |   severity_Minor |   severity_Moderate |   inspection_method_Automated Testing |   inspection_method_Manual Testing |   inspection_method_Visual Inspection |
|:------------------------------------|------------:|-------------:|--------------:|--------------:|-----------------------:|-------------------------:|-------------------------:|----------------------------:|---------------------------:|--------------------------:|--------------------:|-----------------:|--------------------:|--------------------------------------:|-----------------------------------:|--------------------------------------:|
| defect_id                           |  1          |  -0.00134273 |  -0.0275657   |    0.0131028  |             0.059348   |              -0.00406513 |              -0.0533911  |                 -0.0022466  |                0.0140275   |               -0.0114998  |         -0.0258694  |       0.0164891  |         0.00927722  |                            0.0232628  |                       -0.0406472   |                            0.0184026  |
| product_id                          | -0.00134273 |   1          |  -0.0338405   |    0.00464685 |             0.0247042  |               0.0220998  |              -0.0458061  |                 -0.0331491  |               -0.00135767  |                0.0338404  |         -0.0552113  |       0.0100771  |         0.0458562   |                            0.0330812  |                        0.0422356   |                           -0.073934   |
| defect_date                         | -0.0275657  |  -0.0338405  |   1           |   -0.0102164  |            -0.00992066 |               0.0159791  |              -0.00623935 |                 -0.00473033 |               -0.00293253  |                0.00750448 |         -0.0304884  |       0.0292211  |         0.000778718 |                            0.0119917  |                       -0.0600214   |                            0.04858    |
| repair_cost                         |  0.0131028  |   0.00464685 |  -0.0102164   |    1          |             0.0145103  |              -0.00131375 |              -0.0127368  |                  0.0605127  |               -0.00755467  |               -0.0519733  |         -0.004285   |       0.0175563  |        -0.0138444   |                           -0.0130743  |                        0.0469684   |                           -0.034482   |
| defect_type_Cosmetic                |  0.059348   |   0.0247042  |  -0.00992066  |    0.0145103  |             1          |              -0.478894   |              -0.492861   |                 -0.0172392  |               -0.019418    |                0.0358783  |          0.0188408  |      -0.0208642  |         0.0024307   |                            0.00581126 |                       -0.0125426   |                            0.00698729 |
| defect_type_Functional              | -0.00406513 |   0.0220998  |   0.0159791   |   -0.00131375 |            -0.478894   |               1          |              -0.527816   |                  0.0292306  |                0.0324935   |               -0.0604134  |          0.00498895 |      -0.0368469  |         0.0331405   |                           -0.00778086 |                        0.0206654   |                           -0.0132297  |
| defect_type_Structural              | -0.0533911  |  -0.0458061  |  -0.00623935  |   -0.0127368  |            -0.492861   |              -0.527816   |               1          |                 -0.0122928  |               -0.0134189   |                0.0251661  |         -0.0231736  |       0.0567075  |        -0.0351991   |                            0.00208954 |                       -0.00834736  |                            0.00635234 |
| defect_location_Component           | -0.0022466  |  -0.0331491  |  -0.00473033  |    0.0605127  |            -0.0172392  |               0.0292306  |              -0.0122928  |                  1          |               -0.478186    |               -0.513705   |         -0.0251591  |      -0.00315054 |         0.028929    |                           -0.00383775 |                       -0.0301602   |                            0.0338541  |
| defect_location_Internal            |  0.0140275  |  -0.00135767 |  -0.00293253  |   -0.00755467 |            -0.019418   |               0.0324935  |              -0.0134189  |                 -0.478186   |                1           |               -0.50787    |          0.0368458  |      -0.00410153 |        -0.0333244   |                            0.0218586  |                        3.58793e-05 |                           -0.0209627  |
| defect_location_Surface             | -0.0114998  |   0.0338404  |   0.00750448  |   -0.0519733  |             0.0358783  |              -0.0604134  |               0.0251661  |                 -0.513705   |               -0.50787     |                1          |         -0.0113174  |       0.00709695 |         0.00417969  |                           -0.0175893  |                        0.0295474   |                           -0.0127272  |
| severity_Critical                   | -0.0258694  |  -0.0552113  |  -0.0304884   |   -0.004285   |             0.0188408  |               0.00498895 |              -0.0231736  |                 -0.0251591  |                0.0368458   |               -0.0113174  |          1          |      -0.527635   |        -0.472497    |                            0.0236779  |                        0.00792594  |                           -0.0305997  |
| severity_Minor                      |  0.0164891  |   0.0100771  |   0.0292211   |    0.0175563  |            -0.0208642  |              -0.0368469  |               0.0567075  |                 -0.00315054 |               -0.00410153  |                0.00709695 |         -0.527635   |       1          |        -0.499361    |                           -0.0243129  |                        0.0392375   |                           -0.0159867  |
| severity_Moderate                   |  0.00927722 |   0.0458562  |   0.000778718 |   -0.0138444  |             0.0024307  |               0.0331405  |              -0.0351991  |                  0.028929   |               -0.0333244   |                0.00417969 |         -0.472497   |      -0.499361   |         1           |                            0.00107511 |                       -0.048793    |                            0.0477956  |
| inspection_method_Automated Testing |  0.0232628  |   0.0330812  |   0.0119917   |   -0.0130743  |             0.00581126 |              -0.00778086 |               0.00208954 |                 -0.00383775 |                0.0218586   |               -0.0175893  |          0.0236779  |      -0.0243129  |         0.00107511  |                            1          |                       -0.479054    |                           -0.478004   |
| inspection_method_Manual Testing    | -0.0406472  |   0.0422356  |  -0.0600214   |    0.0469684  |            -0.0125426  |               0.0206654  |              -0.00834736 |                 -0.0301602  |                3.58793e-05 |                0.0295474  |          0.00792594 |       0.0392375  |        -0.048793    |                           -0.479054   |                        1           |                           -0.54202    |
| inspection_method_Visual Inspection |  0.0184026  |  -0.073934   |   0.04858     |   -0.034482   |             0.00698729 |              -0.0132297  |               0.00635234 |                  0.0338541  |               -0.0209627   |               -0.0127272  |         -0.0305997  |      -0.0159867  |         0.0477956   |                           -0.478004   |                       -0.54202     |                            1          |

[In]
```
corr_matrix = df_defect_encode.corr()

plt.figure(figsize=(15,5))
sns.heatmap(corr_matrix,annot=True, cmap='coolwarm')
plt.title('Manufacture defects')
plt.show()
```

[Out]

![image](https://github.com/user-attachments/assets/8729d920-783a-4348-82c2-1266c611c8b7)

**Key Correlations:**

**Defect Types and Inspection Methods:**

***Cosmetic Defects:***

Manual Testing: Shows a moderate positive correlation, indicating that manual testing is somewhat effective in identifying cosmetic defects.

Visual Inspection: Also shows a moderate positive correlation, suggesting that visual inspection is useful for detecting cosmetic issues.

***Functional Defects:***

Automated Testing: Displays a strong negative correlation, indicating that automated testing is highly effective in identifying functional defects.

Manual Testing: Shows a weak correlation, suggesting that manual testing is less effective for functional defects.

***Structural Defects:***

Automated Testing: Shows a strong positive correlation, indicating that automated testing is effective in identifying structural defects.

Visual Inspection: Displays a moderate positive correlation, suggesting that visual inspection is also useful for detecting structural issues.

**Defect Locations:**

***Internal Defects:***

Automated Testing: Shows a strong positive correlation, indicating that automated testing is effective in identifying internal defects.

Manual Testing: Displays a weak correlation, suggesting that manual testing is less effective for internal defects.

***Surface Defects:***

Visual Inspection: Shows a strong positive correlation, indicating that visual inspection is highly effective in identifying surface defects.

Automated Testing: Displays a moderate positive correlation, suggesting that automated testing is also useful for detecting surface issues.

**Severity Levels:**

***Critical Defects:***

Automated Testing: Shows a strong positive correlation, indicating that automated testing is crucial for identifying critical defects.

Manual Testing: Displays a weak correlation, suggesting that manual testing is less effective for critical defects.

***Minor Defects:***

Visual Inspection: Shows a moderate positive correlation, indicating that visual inspection is useful for identifying minor defects.

Automated Testing: Displays a weak correlation, suggesting that automated testing is less effective for minor defects.

