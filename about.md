---
layout: about
image: /assets/img/blog/hydejack-9.jpg
description: >
  A boutique Jekyll theme for hackers, nerds, and academics,
  with a focus on personal sites that are meant to impress.
hide_description: true
---
# Project Overview
<!--author-->

## Historical Background
{:.lead}
 1. this list will be replaced by the toc
 {:toc .large-only}


![Screenshot](assets/img/dahuting.jpg){:.lead width="1920" height="1080" loading="lazy"}
Eastern Han dynasty tombs of Zhang Boya and his wife in Henan Province
{:.figcaption}
![600x300](/assets/img/history.jpg "Large example image")
Chronology of Early Chinese Empires
{:.figcaption}
## Motivation
![800x400](/assets/img/project5.jpg "Large example image")
## Why Using Digital Technology?
![800x400](/assets/img/project3.jpg "Large example image")
## Reference and Method
![800x400](/assets/img/project4.jpg "Large example image")
![800x400](/assets/img/haha.png "Large example image")
## How I collected and organized Data
#### ① Merge all sheets and create a dataframe "Project"
![800x400](/assets/img/vscode1.jpg "Large example image")
** **
```html
<!-- file: `project.ipynb` -->
<script>
tomb = pd.read_excel('project.xlsx', sheet_name='main')
county = pd.read_excel('project.xlsx', sheet_name='county')
period = pd.read_excel('project.xlsx', sheet_name='period')
tomb_county=tomb.merge(county,left_on="county", right_on="county", how="left")
tomb_county_period=tomb_county.merge(period,left_on="period", right_on="period", how="left")  
project=tomb_county_period
</script>
```
#### ② Extract and Save the columns "County" into a CSV file
** **
```html
<!-- file: `project.ipynb` -->
<script>
county_df = project[['county', 'county_ft', 'latitude_y', 'longitude_y']].drop_duplicates().sort_values(by=['county', 'county_ft', 'latitude_y', 'longitude_y'])
county_df.to_csv('county.csv', index=False)
</script>
```
#### ③  Calculate the Distance between Han Tomb and Respective County
** **
```html
<!-- file: `project.ipynb` -->
<script>
from haversine import haversine, Unit   
loc_tomb=project[['tomb_id','latitude_x','longitude_x']]
loc_county=project[['tomb_id','latitude_y','longitude_y']]
 
distance = []
for i in range(len(loc_tomb)):
    tomb = (loc_tomb.iloc[i,1], loc_tomb.iloc[i,2])
    county = (loc_county.iloc[i,1], loc_county.iloc[i,2])
    distance.append(haversine(tomb, county, unit='km'))
    
project['distance'] = distance
project['distance'] = project['distance'].round(2)
</script>
```
#### ④ Create a dataframe "Project_all" → Save to "project_all.csv" with Added Column [distance]
** **
```html
<!-- file: `project.ipynb` -->
<script>
project.to_csv('project_all.csv', index=False)
</script>
```
![800x400](/assets/img/vscode2.jpg "Large example image")
## Results 1: Graphs by matplotlib
![800x400](/assets/img/graph.png "Large example image")
** **
```html
<!-- file: `project.ipynb` -->
<script>
import numpy as np  
import matplotlib.pyplot as plt
bins = [0, 5, 10, 15, 20, float('inf')]
def plot_distance_distribution(period, color):
    period_tombs = project.query(f'period == "{period}"')[['tomb_id', 'distance']]
    period_tombs['distance_category'] = pd.cut(period_tombs['distance'], bins=bins, labels=['0-5km', '5-10km', '10-15km', '15-20km', '20+km'], right=False)
    category_counts = period_tombs.groupby('distance_category').size().reindex(['0-5km', '5-10km', '10-15km', '15-20km', '20+km']).fillna(0)
    plt.stackplot(category_counts.index, category_counts.values, labels=[f'{period}'], alpha=0.5, color=color)
# Plot line graph for each period, specifying colors
plot_distance_distribution('Western Han', 'skyblue')
plot_distance_distribution('Xin', 'yellow')
plot_distance_distribution('Eastern Han', 'pink')
plt.xlabel('Distance')
plt.ylabel('Number of Tombs')
plt.title('Distribution of Tombs')
plt.legend()  # Show legend to differentiate between periods
plt.show()
</script>
```
## Results 2: Mapping by using QGIS
#### Process ① Add Layer of the dataframe "project_all.csv"
![800x400](/assets/img/project/Slide1.jpeg "Large example image")
#### Process ② Filter Data and Create Separate Layers for Western, Xin, and Eastern Han Tombs
![800x400](/assets/img/project/Slide2.jpeg "Large example image")
#### Process ③ Create Gradated Symbols for Each Layer Based on the Distance from County Seats
![800x400](/assets/img/project/Slide3.jpeg "Large example image")
### Challenge: Difficulty in Observing Distribution and Shifts
![800x400](/assets/img/all.png "Large example image")
### Solution: Select a County and Zoom-In → Create a Static Map
![800x400](/assets/img/all_henan.png "Large example image")
## Result 3: Creating an Interactive map
![800x400](/assets/img/project6.jpg "Large example image")
## Future Improvements
![800x400](/assets/img/project7.jpg "Large example image")