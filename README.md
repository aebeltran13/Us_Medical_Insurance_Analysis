# U.S. Medical Insurance Costs

## Project Goals
The objective of this project is to conduct a descriptive and exploratory analysis on the US Medical Insurance Costs dataset with the aim of identifying underlying patterns that may have a bearing on the accumulation of insurance charges.

## Data Description
The US Medical Insurance Costs consists of seven columns.
- Age
- Sex
- BMI
- Number of Children
- Smoker Boolean
- Region
- Charges

## Analysis
In order to gain a better understanding of the US Insurance cost dataset, I conducted both descriptive and exploratory analyses. Descriptive analysis involved summarizing and visualizing key features of the dataset, including central tendencies, variability, and distribution of variables. Exploratory analysis involved digging deeper into the data to uncover potential relationships, patterns, and anomalies that could shed light on factors that contribute to insurance charges. The results of these analyses provide valuable insights into the underlying dynamics of the dataset and can inform future decision-making processes.

## Implementation

### Loading the Dataset


```python
import csv

# Initialize empty list to store data
data_list = []

# Open the CSV file and read data into list
with open('insurance.csv', mode='r') as csv_file:
    csv_reader = csv.DictReader(csv_file)
    for row in csv_reader:
        # Convert string values to their appropriate data type if needed
        # ! We dont do this to sex and region because those values will remain as strings
        row['age'] = int(row['age'])
        row['bmi'] = float(row['bmi'])
        row['children'] = int(row['children'])
        row['smoker'] = True if row['smoker'] == 'yes' else False
        row['charges'] = float(row['charges'])
        
        # Append current row to the list
        data_list.append(row)
```

### Descriptive Analysis

To facilitate easy access to the total number of rows in the dataset, a variable will be created to store this information.


```python
total = len(data_list)
```

To facilitate efficient data manipulation and obtain statistical metrics, separate variables will be created for each numeric column of the dataset.


```python
# Extract the numeric columns from data_list
ages = [row['age'] for row in data_list]
bmis = [row['bmi'] for row in data_list]
num_children = [row['children'] for row in data_list]
num_charge = [row['charges'] for row in data_list]
```

#### Averages


```python
def get_avg(numbers):
    return sum(numbers)/total

def get_std_dev(numbers, avg):
    # Calculate the sum of the squared differences from the mean
    squared_differences = [(x - avg) ** 2 for x in numbers]
    sum_squared_differences = sum(squared_differences)

    # Calculate the variance and standard deviation
    variance = sum_squared_differences / len(numbers)
    stdev = variance ** 0.5

    return stdev

age_avg = get_avg(ages)
bmi_avg = get_avg(bmis)
children_avg = get_avg(num_children)
charge_avg = get_avg(num_charge)

age_std_dev = get_std_dev(ages, age_avg)
bmi_std_dev = get_std_dev(bmis, bmi_avg)
children_std_dev = get_std_dev(num_children, children_avg)
charge_std_dev = get_std_dev(num_charge, charge_avg)

print(f"Age avg: {round(age_avg, 2)} | std dev: {round(age_std_dev, 2)}")
print(f"BMI avg: {round(bmi_avg, 2)} | std dev: {round(bmi_std_dev, 2)}")
print(f"Children avg: {round(children_avg, 0)} | std dev: {round(children_std_dev, 2)}")
print(f"Charges avg: {round(charge_avg,2)}  | std dev: {round(charge_std_dev, 2)}")
```

    Age avg: 39.21 | std dev: 14.04
    BMI avg: 30.66 | std dev: 6.1
    Children avg: 1.0 | std dev: 1.21
    Charges avg: 13270.42  | std dev: 12105.48
    

#### Ranges


```python
def median(numbers):
    sorted_nums = sorted(numbers)
    n = len(sorted_nums)
    midpoint = n // 2
    if n % 2 == 1:
        return sorted_nums[midpoint]
    else:
        lo = midpoint - 1
        hi = midpoint
        return (sorted_nums[lo] + sorted_nums[hi]) / 2

ages_median = median(ages)
bmis_median = median(bmis)
children_median = median(num_children)
charges_median = median(num_charge)

print(f"Age\n Min: {min(ages)} | Max: {max(ages)} | Median: {int(ages_median)}")
print(f"BMI\n Min: {min(bmis)} | Max: {max(bmis)} | Median: {int(bmis_median)}")
print(f"Children\n Min: {min(num_children)} | Max: {max(num_children)} | Median: {int(children_median)}")
print(f"Charges\n Min: {min(num_charge)} | Max: {max(num_charge)} | Median: {int(charges_median)}")
```

    Age
     Min: 18 | Max: 64 | Median: 39
    BMI
     Min: 15.96 | Max: 53.13 | Median: 30
    Children
     Min: 0 | Max: 5 | Median: 1
    Charges
     Min: 1121.8739 | Max: 63770.42801 | Median: 9382
    

#### Ratios


```python
# Initialize an empty dictionary to store the counts
region_counts = {}

# Iterate over the data_list and count the rows for each region
for row in data_list:
    region = row['region']
    if region in region_counts:
        region_counts[region] += 1
    else:
        region_counts[region] = 1

# Print the counts for each region
for region, count in region_counts.items():
    print(f"{region}: {count}")
```

    southwest: 325
    southeast: 364
    northwest: 325
    northeast: 324
    


```python
# Intitialize an empty dictionary to store the counts of smokers
smoker_counts = {}

# Iterate over the data_list and count the rows for each region
for row in data_list:
    smoker = row['smoker']
    if smoker in smoker_counts:
        smoker_counts[smoker] += 1
    else:
        smoker_counts[smoker] = 1
        
# Print the counts for smokers vs non-smokers
for smoker, count in smoker_counts.items():
    print(f"Smoker\n{smoker}: {count}")
```

    Smoker
    True: 274
    Smoker
    False: 1064
    


```python
# Initialize an empty dictionary to store the counts
gender_counts = {}

# Iterate over the data_list and count the rows for each gender
for row in data_list:
    gender = row['sex']
    if gender in gender_counts:
        gender_counts[gender] += 1
    else:
        gender_counts[gender] = 1

# Print the counts for each gender
for gender, count in gender_counts.items():
    print(f"{gender}: {count}")
```

    female: 662
    male: 676
    

### Exploratory Analysis


```python
# Initialize an empty variable storing the average values of smoker vs non-smoker debts
smoker_debt_sum = 0
non_smoker_debt_sum = 0

for row in data_list:
    if row['smoker'] is True:
        smoker_debt_sum += row['charges']
    else:
        non_smoker_debt_sum += row['charges']
        
smoker_debt_avg = smoker_debt_sum / smoker_counts[True]
non_smoker_debt_avg = non_smoker_debt_sum / smoker_counts[False]

print(f"Smoker average charges: {round(smoker_debt_avg,2)}")
print(f"Non Smoker average charges: {round(non_smoker_debt_avg, 2)}")

smoker_debt_avg_difference = smoker_debt_avg/non_smoker_debt_avg
print(f"Smoker average debt is {round(smoker_debt_avg_difference,2)} times bigger than the Non Smoker average. ")
```

    Smoker average charges: 32050.23
    Non Smoker average charges: 8434.27
    Smoker average debt is 3.8 times bigger than the Non Smoker average. 
    

This shows that people who smoke are usually more likely to build up a large medical debt.


```python
# Initialize an empty dictionary to store the sum of charges and count for each number of children
charges_by_children = {}

# Iterate over the data_list and accumulate the charges for each number of children
for row in data_list:
    children = row['children']
    charge = row['charges']
    if children in charges_by_children:
        charges_by_children[children]['sum'] += charge
        charges_by_children[children]['count'] += 1
    else:
        charges_by_children[children] = {'sum': charge, 'count': 1}

# Calculate and print the average charges for each number of children
for children, data in charges_by_children.items():
    average_charge = data['sum'] / data['count']
    print(f"Number of Children: {children} - Average Charges: {round(average_charge,2)}")
```

    Number of Children: 0 - Average Charges: 12365.98
    Number of Children: 1 - Average Charges: 12731.17
    Number of Children: 3 - Average Charges: 15355.32
    Number of Children: 2 - Average Charges: 15073.56
    Number of Children: 5 - Average Charges: 8786.04
    Number of Children: 4 - Average Charges: 13850.66
    


```python
# Initialize an empty dictionary to store the sum of charges and count for each gender
charges_by_gender = {}

# Iterate over the data_list and accumulate the charges for each gender
for row in data_list:
    gender = row['sex']
    charge = row['charges']
    if gender in charges_by_gender:
        charges_by_gender[gender]['sum'] += charge
        charges_by_gender[gender]['count'] += 1
    else:
        charges_by_gender[gender] = {'sum': charge, 'count': 1}

# Calculate and print the average charges for each gender
for gender, data in charges_by_gender.items():
    average_charge = data['sum'] / data['count']
    print(f"Gender: {gender} - Average Charges: {round(average_charge,2)}")
```

    Gender: female - Average Charges: 12569.58
    Gender: male - Average Charges: 13956.75
    


```python
# Define the BMI ranges
bmi_ranges = {
    "Underweight": (0, 18.5),
    "Normal Weight": (18.5, 24.9),
    "Overweight": (25, 29.9),
    "Obese": (30, float("inf"))
}

# Initialize an empty dictionary to store the sum of charges and count for each BMI range
charges_by_bmi_range = {bmi_range: {"sum": 0, "count": 0} for bmi_range in bmi_ranges}

# Iterate over the data_list and accumulate the charges for each BMI range
for row in data_list:
    bmi = row['bmi']
    charge = row['charges']
    
    for bmi_range, (min_bmi, max_bmi) in bmi_ranges.items():
        if min_bmi <= bmi < max_bmi:
            charges_by_bmi_range[bmi_range]['sum'] += charge
            charges_by_bmi_range[bmi_range]['count'] += 1
            break

# Calculate and print the average charges for each BMI range
for bmi_range, data in charges_by_bmi_range.items():
    average_charge = data['sum'] / data['count']
    print(f"BMI Range: {bmi_range} - Average Charges: {round(average_charge,2)}")
```

    BMI Range: Underweight - Average Charges: 8852.2
    BMI Range: Normal Weight - Average Charges: 10379.5
    BMI Range: Overweight - Average Charges: 11017.6
    BMI Range: Obese - Average Charges: 15552.34
    


```python
# Initialize an empty dictionary to store the sum of charges and count for each region
charges_by_region = {}

# Iterate over the data_list and accumulate the charges for each region
for row in data_list:
    region = row['region']
    charge = row['charges']
    if region in charges_by_region:
        charges_by_region[region]['sum'] += charge
        charges_by_region[region]['count'] += 1
    else:
        charges_by_region[region] = {'sum': charge, 'count': 1}

# Calculate and print the average charges for each region
for region, data in charges_by_region.items():
    average_charge = data['sum'] / data['count']
    print(f"Region: {region} - Average Charges: {round(average_charge,2)}")
```

    Region: southwest - Average Charges: 12346.94
    Region: southeast - Average Charges: 14735.41
    Region: northwest - Average Charges: 12417.58
    Region: northeast - Average Charges: 13406.38
    

## Conclusion

Based on the analysis conducted on the US Medical Insurance Costs dataset, several key findings have emerged regarding the factors influencing medical insurance charges. The investigation focused on variables such as smoking, BMI, gender, number of children, and region. The results indicate that smoking and BMI are significant contributors to higher medical insurance charges, whereas gender, number of children, and region do not exhibit substantial effects.

Firstly, smoking emerged as a major factor affecting medical insurance charges. Individuals classified as smokers were found to incur significantly higher insurance costs compared to non-smokers. This finding emphasizes the importance of smoking cessation programs and policies to mitigate the financial burden associated with medical insurance.

Secondly, BMI (Body Mass Index) demonstrated a strong correlation with medical insurance charges. Higher BMI values were found to be associated with increased insurance costs. This highlights the significance of maintaining a healthy weight and adopting lifestyle choices that promote overall well-being, thereby potentially reducing insurance charges.

In contrast, the analysis revealed that gender, number of children, and region did not exhibit substantial impacts on medical insurance charges. These variables showed limited or no discernible correlation with the cost of insurance coverage. It is worth noting that this analysis is based on the specific dataset examined, and further research may be required to investigate the influence of these factors in different contexts or datasets.

Overall, the findings suggest that addressing smoking habits and promoting healthy BMI levels can play a crucial role in managing and potentially reducing medical insurance charges. By prioritizing interventions and policies targeting smoking cessation and weight management, individuals and healthcare systems can work towards mitigating the financial burden associated with medical insurance, ultimately fostering better health outcomes and improved affordability for insurance beneficiaries.
