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

Import needed library

    # import needed library:
    import pandas as pd
    import numpy as np
    import seaborn as sns
    import matplotlib.pyplot as plt
    import statsmodels.api as sm

Loading dataset from Google docs

[In 1]```
defect_data='1ivKij1HC8xH_ck-UB2JuGgJlE74WjctAu8ltFskk11k'
url='https://docs.google.com/spreadsheets/d/' + defect_data + '/export?format=xlsx'
df_defect=pd.read_excel(url)
df_defect.head()
```
[Out 1]
