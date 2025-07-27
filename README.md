```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
```
```
#This tells pandas to read the data
Aviation_Data = pd.read_csv("/content/Aviation_Data.csv", low_memory=False)
#Aviation_Data
```
This gives information about the data Key Observations: the dataset has 90,348 rows and 31 columns.
Most columns such as Schedule, Latitude, Longitude, Aircraft.Category, etc. have a lot of missing values.
Most columns are of type object, and 5 are numerical (float64).
```
#This is to check for missing values
Aviation_Data.isnull().sum().sort_values(ascending=False)

```
#Calculating percentage of columns with missing values
Aviation_Data.isna().mean() * 100
```
A good number of columns have missing values e.g Latitude, Longitude, Aircraft.Category, FAR.Description, Schedule, Air.carrier.

Droping of With more than 60% Missing Data since they are likely not usable
```
Aviation_Data.drop(['Schedule', 'Air.carrier', 'FAR.Description', 'Aircraft.Category', 'Latitude', 'Longitude'], axis=1, inplace=True)
To Confirm Remaining Columns Check again which columns still have missing data
```
Aviation_Data.isnull().sum().sort_values(ascending=False)
Aviation_Data.isna().mean() * 100
```
Further, I drop columns with more than 40% missing value i.e Airport.Code and Airport.Name since they are likely less significant
```
Aviation_Data.drop(['Airport.Code', 'Airport.Name'], axis=1, inplace=True)
```
Data Imputation
Filling missing with 0.0 since no data might mean no injuries
```
Aviation_Data['Total.Fatal.Injuries'] = Aviation_Data['Total.Fatal.Injuries'].fillna(0.0)
Aviation_Data['Total.Serious.Injuries'] = Aviation_Data['Total.Serious.Injuries'].fillna(0.0)
Aviation_Data['Total.Minor.Injuries'] = Aviation_Data['Total.Minor.Injuries'].fillna(0.0)
Aviation_Data['Total.Uninjured'] = Aviation_Data['Total.Uninjured'].fillna(0.0)
Aviation_Data
```
```
#Aviation_Data.columns
#Aviation_Data.describe()
```
Categorical Columns – fill with "Unknown"
```
Aviation_Data['Engine.Type'] = Aviation_Data['Engine.Type'].fillna("Unknown")
Aviation_Data['Purpose.of.flight'] = Aviation_Data['Purpose.of.flight'].fillna("Unknown")
Aviation_Data['Weather.Condition'] = Aviation_Data['Weather.Condition'].fillna("Unknown")
Aviation_Data['Aircraft.damage'] = Aviation_Data['Aircraft.damage'].fillna("Unknown")
Aviation_Data['Registration.Number'] = Aviation_Data['Registration.Number'].fillna("Unknown")
Aviation_Data['Injury.Severity'] = Aviation_Data['Injury.Severity'].fillna("Unknown")
Aviation_Data['Country'] = Aviation_Data['Country'].fillna("Unknown")
Aviation_Data['Amateur.Built'] = Aviation_Data['Amateur.Built'].fillna("Unknown")
Aviation_Data['Model'] = Aviation_Data['Model'].fillna("Unknown")
Aviation_Data['Make'] = Aviation_Data['Make'].fillna("Unknown")
Aviation_Data['Location'] = Aviation_Data['Location'].fillna("Unknown")
Aviation_Data['Report.Status'] = Aviation_Data['Report.Status'].fillna("Unknown")
Aviation_Data['Broad.phase.of.flight'] = Aviation_Data['Broad.phase.of.flight'].fillna("Unknown")
print(Aviation_Data['Event.Date'].head(10))
```
Data type for Event.Time is object
```
print(Aviation_Data['Event.Date'].sample(10))
```
Event.Date is a column of interest and still has missing values
Replace the missing values with 2000-01-01
```
# Step 1: Convert to datetime and let invalid ones to NaT
Aviation_Data['Event.Date'] = pd.to_datetime(Aviation_Data['Event.Date'], errors='coerce')
```
```
# Step 2: Replace only the NaT values with placeholder
Aviation_Data['Event.Date'] = Aviation_Data['Event.Date'].fillna(pd.Timestamp('2000-01-01'))
```
```
print("Replaced missing dates:", (Aviation_Data['Event.Date'] == '2000-01-01').sum())
```
Replaced missing dates: 1461
There are 1461 replaced values
```
print(Aviation_Data['Event.Date'].sample(10))
```
To check for inconsistencies in aircraft Model
```
Cleaned_df=pd.read_csv("/content/Cleaned_Aviation_Data_Final.csv", low_memory=False)
Cleaned_df.head()
```
```
Cleaned_df["Make"].value_counts()
```
```
# Your code here
# Strip leading/trailing spaces
Cleaned_df["Make"] = Cleaned_df["Make"].str.strip()
# Replace 'Marvel' with 'Marvel Comics'
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Cessna", "CESSNA")
# Replace any remaining 'DC Comics' variants with consistent name
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Piper", "PIPER")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Bell", "BELL")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Beech", "BEECH")
Cleaned_df["Make"] = Cleaned_df["Make"].replace("Boeing", "BOEING")
```
Replacing unk with UNK
'''
Aviation_Data['Weather.Condition'] = (
    Aviation_Data['Weather.Condition']
    .str.lower()
    .str.replace(r'\bstrip\b', '', regex=True)
    .str.replace(r'\s+', ' ', regex=True)
    .str.strip()
    .replace({'unk.*': 'UNK'}, regex=True)
)
'''
Cleaned_df['Weather.Condition'].value_counts()
Cleaned_df["Weather.Condition"] = Cleaned_df["Weather.Condition"].replace("Unknown", "UNK")
Cleaned_df["Weather.Condition"] = Cleaned_df["Weather.Condition"].replace("Unk", "UNK")
Cleaned_df["Weather.Condition"]
```
```
Cleaned_df["Weather.Condition"].value_counts().head(20)
```
```
Cleaned_df["Make"].value_counts().head(20)
```
```
Cleaned_df.to_csv("Cleaned_Aviation_Data_Final.csv", index=False)
```
Further correction of inconsistencies

This indicates that there more accidents when the weather condition was VMC ie. weather that allows pilots to fly using visual reference such as land mark. fewer accidents occured when weather condition was not known(UNK)
Visualization of important Columns
```
# Accidents over time
# Create 'Year' column from 'Event.Date'
Aviation_Data['Year'] = Aviation_Data['Event.Date'].dt.year

plt.figure(figsize=(14, 6))
Aviation_Data['Year'].value_counts().sort_index().plot()
plt.title('Number of Accidents per Year')
plt.xlabel('Year')
plt.ylabel('Accident Count')
plt.show()
```
ere were no accidents between 1950 and 1980. However, in 1980 they plummeted upto 3500 and thereafter the number of Accidents delined gradually to about 2000 in the year 2020

Analysing Data to Answer the Business Problem

Investigation by make and model

Count of injuries severity by aircraft

Key performance index for the best make and model
Deductions
Most accidents were caused by CESSNA 152 and 172 and models followed by PIPER
Risk Severity Analysis

The following are Manufacturers with reliable safety records.i.e Aircraft with lowest fatality and damage rates. I therefore recommend the investor to them

1.INTERNATIONAL INC
2. AMERICAN BLIMP CORP 
3. AMERICAN LEGEND AIRCRAFT CO 
4.ARROW FALCON EXPORTERS INC 
5.AVID AIRCRAFT 
6. BENNETT
7. BOEING COMPANY
8. BRITISH AIRCRAFT CORP. (BAC) 
9. BUCKER FLUGZEUGBAU
10. CENTRAIR
Investigation by make and model
Count of injuries severity and aircrat model
Risk by Type of Operation
Key Performance Index for the best make and model
```
Aviation_Data.drop(['Publication.Date', 'Event.Id', 'Accident.Number'], axis=1, inplace=True)
```
```
Aviation_Data.isnull().sum().sort_values(ascending=False)
```
```
Aviation_Data['Number.of.Engines'] = Aviation_Data['Engine.Type'].fillna("Unknown")
```
Data is ready for Analysis after cleaning
``
Aviation_Data.isnull().sum().sort_values(ascending=False)
```
```
Aviation_Data.to_csv("Cleaned_Aviation_Data.csv", index=False)
```
