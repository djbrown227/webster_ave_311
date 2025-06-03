# Mapping Neglect: A 311 Data Template for Community-Driven Change

### How to Use NYC Open Data to Surface Persistent Problems in Your Neighborhood

New Yorkers know what’s broken in their neighborhoods—trash that sits for days, abandoned cars that never move, and streets that just feel forgotten. But how can these issues be proven and elevated to get the attention of people who can fix them?

This project offers a **reproducible template** for using NYC 311 complaint data to:

- Identify local issues like **illegal dumping** or **dirty conditions**
- Narrow complaints down to **specific ZIP codes** or **neighborhood blocks**
- Use **mapping and response-time analysis** to understand how the city responds
- Share these insights with **community groups, journalists, or local leaders**

---

## Case Study: Webster Avenue (Gun Hill Road to E 233rd Street)

As a real-world example, I examined a section of **Webster Avenue in the Bronx**, between **Gun Hill Road and East 233rd Street**, just west of **Woodlawn Cemetery**. Since the COVID-19 pandemic, this area has seen a noticeable increase in **illegal dumping and other sanitation issues**.

Using NYC Open Data, I pulled 311 complaints from ZIP codes 10467 and 10470, filtered them down to a polygon surrounding the target corridor, and isolated complaint types like:

- **Illegal Dumping**
- **Unsanitary Condition**
- **Dirty Condition**
- **Derelict Vehicle**

---

## How to Do It Yourself

You can replicate this analysis with the following core components:

### 1. Pull 311 data from NYC Open Data

```python
from datetime import datetime, timedelta

def get_last_6_months_date():
    return (datetime.now() - timedelta(days=6*30)).strftime('%Y-%m-%dT%H:%M:%S')

six_months_ago = get_last_6_months_date()

# Example query for Bronx ZIP codes
query = f"&$where=created_date >= '{six_months_ago}' AND incident_zip IN('10467', '10470')"
````

> You can change the ZIP codes to target your area and adjust the date range if needed.

---

### 2. Filter data spatially using a polygon

This example focuses on a corridor near Woodlawn Cemetery. You can update the coordinates to match your area of interest.

```python
import pandas as pd
from shapely.geometry import Point, Polygon

# Define your custom polygon (lon, lat)
polygon = Polygon([
    (-73.872168, 40.878765),
    (-73.871046, 40.878558),
    (-73.863889, 40.895472),
    (-73.863040, 40.895186)
])

def is_within_polygon(lat, lon, poly):
    point = Point(lon, lat)
    return poly.contains(point)

# Filter your DataFrame
df['in_polygon'] = df.apply(
    lambda row: is_within_polygon(row['latitude'], row['longitude'], polygon),
    axis=1
)

df_filtered = df[df['in_polygon']].drop(columns='in_polygon')
```

---

## ![Insert spatial map of filtered complaints](path-to-image-1.png)

*A view of complaints isolated to the Webster Avenue corridor.*

---

### 3. Focus on Key Complaint Types

```python
complaints_of_interest = ['Illegal Dumping', 'UNSANITARY CONDITION', 'Dirty Condition', 'Derelict Vehicle']
df_filtered = df_filtered[df_filtered['complaint_type'].isin(complaints_of_interest)].copy()
```

> You can change the list of complaint types based on what issues matter in your neighborhood.

---

## Insights from the Data

* **Unsanitary Condition** complaints took **over a month on average** to be closed.
* **Illegal Dumping** complaints took **over 6 days on average**—a delay that likely allows dumping sites to grow and persist.
* Complaints in this zone are **concentrated near vacant lots and commercial frontages**, indicating patterns of neglect.

---

## ![Insert chart of response times or complaint counts](path-to-image-2.png)

*A look at how long it takes for city agencies to respond to different complaint types.*

---

## Final Thoughts

311 data isn’t just about fixing potholes. It’s a **tool for civic empowerment**.

If you're a **resident, community board member, organizer, or journalist**, you can use this template to:

* Prove that a problem exists with real evidence
* Spot slow response patterns by agency or location
* Bring these facts to the people who have the power to act

Let’s turn street-level problems into **city-level accountability**—one dataset at a time.
