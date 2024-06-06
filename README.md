# Hospitals
This repository visualizes the relationship between patient experience and timeliness of visits based on hospital ownership.

## Description

This project reads data from a CSV file and generates visualizations to analyze how different types of hospital ownership affect patient experience and timeliness of care. It includes scatter plots, pie charts, and bar charts to represent the data.

## File

- `hospitals.csv`: The dataset containing hospital information.

## Dependencies

- numpy
- matplotlib


## hospitals.py
```python
import numpy as np
import matplotlib.pyplot as plt
import csv

FILE = "hospitals.csv"

OWNERSHIP = 9
EXPERIENCE = 16
TIMELINESS = 18

def read_csv(file):
    '''Function: read_csv
       Parameters: file, a string
       Returns: 2d list of strings
       Does: iterates over csv file, turning each column into item in 2d list
    '''
    lst = []
    with open(file, "r") as infile:
        csvfile = csv.reader(infile)
        next(csvfile)
        for line in csvfile:  
            lst.append(line)
    return lst

def get_col(data, col):
    ''' Function: get_col
        Parameters: 2d list of anything, a column number (int)
        Returns: one column of the 2d list, turned into a list of its own
        Does: loops over the 2d list 
        - appends a value at the given location onto a new list
    '''
    lst = []
    for row in data:
        lst.append(row[col])
    return lst

def count_ownership(lst):
    ''' Function: count_ownership
        Parameters: 1d list of ownership 
        Returns: count of each ownership type
        Does: iterates through 1d list, counting different ownership types
        and determining the prevalence of each 
    '''
    count_prop = 0
    count_gov_fed = 0
    count_vol_priv = 0
    count_gov_loc = 0
    count_vol_other = 0
    for hospital in lst:
        if hospital == "Proprietary":
            count_prop += 1
        elif hospital == "Government - Federal":
            count_gov_fed += 1
        elif hospital == "Voluntary non-profit - Private":
            count_vol_priv += 1
        elif hospital == "Government - Local":
            count_gov_loc += 1
        elif hospital == "Voluntary non-profit - Other":
            count_vol_other += 1
    return count_vol_other, count_gov_loc, count_vol_priv, count_gov_fed, count_prop

def main():
    # Gather data - open file and append data to lists
    csvfile = read_csv(FILE)
    experience = get_col(csvfile, EXPERIENCE)
    experience = [int(num) for num in experience]
    timeliness = get_col(csvfile, TIMELINESS)
    timeliness = [int(num) for num in timeliness]
    ownership = get_col(csvfile, OWNERSHIP)

    # Computation - iterate through ownership list and confirm ownership count
    other = ownership.count("Voluntary non-profit - Other")
    local = ownership.count("Government - Local")
    private = ownership.count("Voluntary non-profit - Private")
    federal = ownership.count("Government - Federal")
    proprietary = ownership.count("Proprietary")
    print(other)
    print(local)
    print(private)
    print(federal)
    print(proprietary)
    
    # Communication - create scatterplot with trendline
    colormap = {"Voluntary non-profit - Other": "royalblue", 
                "Government - Local": "darkorange",
                "Voluntary non-profit - Private": "forestgreen",
                "Government - Federal": "red",
                "Proprietary": "mediumorchid"}

    colors = [colormap[category] for category in ownership]
    plt.scatter(timeliness, experience, c=colors)
    
    # Trendline
    z = np.polyfit(timeliness, experience, 1)
    p = np.poly1d(z)
    plt.plot(timeliness, p(timeliness), color="black", linewidth=1, linestyle='--')
    
    # Legend
    markers = [plt.Line2D([0,0],[0,0],color=color, marker='o', linestyle='') 
               for color in colormap.values()]
    plt.legend(markers, colormap.keys(), numpoints=1, fontsize='small')
    
    # Labels 
    plt.xlabel("Timeliness of Care (0-100)")
    plt.ylabel("Patient Experience (0-100)")
    plt.title("Patient Experience and Timeliness of Care Based on " 
              "Hospital Ownership in MA")
    plt.show()
    
    # Communication - create pie chart of hospital ownership
    
    # Run function
    count_vol_other, count_gov_loc, count_vol_priv, count_gov_fed, count_prop = count_ownership(ownership)
    
    # Assign values and colors to ownership type
    values = np.array([count_vol_other, count_gov_loc, count_vol_priv, 
                       count_gov_fed, count_prop])
    labels = ["Voluntary non-profit - Other", 
              "Government - Local",
              "Voluntary non-profit - Private",
              "Government - Federal",
              "Proprietary"]
    colors = ["royalblue", "darkorange","forestgreen","red", "mediumorchid"]
    # Separation
    explode = (0, 0, 0.1, 0, 0)
    
    # Implementation 
    # Add legend and title
    fig1, ax1 = plt.subplots()
    plt.pie(values, explode=explode, labels=labels, colors=colors,
            autopct='%1.1f%%', shadow=False, startangle=20)
    plt.legend(bbox_to_anchor=(0, 0.15))
    plt.title("Distribution of Ownership for 64 Hospitals in MA")
    plt.show()
    
    # Communication - create horizontal bar chart 
    plt.barh("Voluntary non-profit - Other", other, color="royalblue")
    plt.barh("Government - Local", local, color="darkorange")
    plt.barh("Voluntary non-profit - Private", private, color="forestgreen")
    plt.barh("Government - Federal", federal, color="red")
    plt.barh("Proprietary", proprietary, color="mediumorchid")
    
    # Add labels and title
    plt.xlabel("Number of Hospitals")
    plt.ylabel("Type of Hospital Ownership")
    plt.title("Number of Hospitals For Each Type of Ownership in MA")
    plt.show()
    
    # Print function
    print(count_vol_other, count_gov_loc, count_vol_priv, count_gov_fed, count_prop)

if __name__ == "__main__":
    main()
