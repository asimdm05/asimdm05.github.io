# Business Analyst

### Education
The University of Arizona, Eller College of Management
    Major: Finance and Management Information Systems | May 2027
    GPA: 4.0
    Honors/Awards: Global Wildcat Scholarship Award, Highest Academic Distinction

### Work Experience
Student Finance Specialist @ University of Arizona, Finance Strategy & Solutions | April 2024 – Present
- Optimized reports and projections with university financial reporting databases enhancing data accuracy and timeliness for 15%.
- Streamlined audit, AP/AR processes, administration and reconciliation of accounts improving financial
controls and administrative efficiency for 23%.
- Established financial and accounting data analysis, identifying and investigating a $400,000 financial discrepancy
- Conducted research topics and procedures pertaining to compliance with GASB & ABOR guidelines.

Assistant to the CEO @ Boss Security Screens  | March 2024 – Present
- Consulted 5 business divisions in 3 states, implemented financial reporting and informational systems,
standardized procedures for expanded locations and reduced operations timeline by 30%.
- Conducted partner conferences to address pain points, increased satisfaction by 65%, and installed systems improving customer satisfaction by 35%.
- Researched and provided cost-effective solutions, achieving a 40% performance improvement and a 70% increase in marketing lead income.

National executive board member, Head of PR Department @ Youth Movement of Koreans in Kazakhstan Public Association | October 2022 – August 2023
- Coordinated international concerts, conventions, and conferences with an attendance of 3,000 people.
- Cultivated relationships with prominent industry leaders by hosting key entrepreneurial forums.
- Recruited, trained, and led a team of 100 employees for major national level events.

Development Strategy Intern @ Made in Korea (MIKO) | June 2022 – August 2023
- Developed promotional campaign involving collaboration with the nation's most prominent influencers.
- Managed and implemented major marketing events resulting in an increased conversion rate by 25%.
- Established local production and supply chain system by increasing 10% sales.

### Python Projects
Wayfair Sales & Product Trends Analysis

```py
# --- Imports ---
import os
import io
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from google.colab import files
from plotnine import (
    ggplot, aes, geom_col, geom_line, geom_smooth,
    facet_wrap, scale_y_continuous, scale_x_datetime,
    labs, theme, theme_minimal, expand_limits, element_text
)
from mizani.breaks import date_breaks
from mizani.formatters import date_format, currency_format

# --- Upload & Load Excel Files ---
uploaded = files.upload()

product_df = pd.read_excel("/content/wayfair_products.xlsx")
orderlines_df = pd.read_excel("/content/wayfair_orderlines.xlsx")
shops_df = pd.read_excel("/content/wayfair_shops.xlsx")

# --- Merge DataFrames ---
orderline_joined_df = (
    orderlines_df
    .merge(product_df, how="left", left_on="product.id", right_on="product.id")
    .merge(shops_df, how="left", left_on="store.id", right_on="shop.id")
)

orderline_joined_df.info()
print(orderline_joined_df)

# --- Data Wrangling ---
df = orderline_joined_df.copy()

# Split product.info into Type and Style
temp_df = df['product.info'].str.split('-', expand=True)
df['Type'] = temp_df[0]
df['Style'] = temp_df[1]

# Drop unnecessary column
df.drop(columns='shop.id', inplace=True)

# Clean column names
df.columns = df.columns.str.replace(".", "_")

# Add total_sales column
df['total_sales'] = df['price'] * df['quantity']

# Get top 10 sales orders
top_10_orders = df.sort_values('total_sales', ascending=False).head(10)
print(top_10_orders)

# --- Examining Data ---
product_df.head(5)
orderlines_df.head(5)

# --- Creating Top 5 Material ---
m = product_df['material']
material_frequency_series = m.value_counts()
material_frequency_series.nlargest(5)

# --- Creating horizontal descending bar chart ---
top5_material = product_df['material'].value_counts().nlargest(5).sort_values(ascending=True)
fig1 = top5_material.plot(kind="barh", title="Top 5 Materials")
plt.show()

orderlines_wrangled_df = df.copy()

# --- Creating Top 5 Type ---
t = orderlines_wrangled_df['Type']
sn_frequency_series = t.value_counts()
sn_frequency_series.nlargest(5)

top5_type = orderlines_wrangled_df['Type'].value_counts().nlargest(5).sort_values(ascending=True)
fig2 = top5_type.plot(kind="barh", title="Top 5 Product Types")
plt.show()

# --- Creating Top 5 Style ---
s = orderlines_wrangled_df['Style']
sn_frequency_series = s.value_counts()
sn_frequency_series.nlargest(5)

top5_style = orderlines_wrangled_df['Style'].value_counts().nlargest(5).sort_values(ascending=True)
fig3 = top5_style.plot(kind="barh", title="Top 5 Product Styles")
plt.show()

# --- Save Cleaned Data ---
os.makedirs("orderlines_wrangled_df", exist_ok=True)
orderlines_wrangled_df.to_pickle("/content/orderlines_wrangled_df.pkl")

# --- Reload Pickled Data ---
df = pd.read_pickle("/content/orderlines_wrangled_df.pkl")
df = pd.DataFrame(df)
df.info()

# --- Weekly Sales Analysis ---
order_date_series = df['order_date']
order_date_series.dt.year

sales_week = (
    df[['order_date', 'total_sales']]
    .set_index('order_date')
    .resample('W')
    .agg(np.sum)
    .reset_index()
)

# Step 2 - visualize
# simple plot
sales_week.set_index('order_date').plot(
    kind='line', subplots=True, layout=(1, 1), figsize=(10, 5)
)
plt.show()

# Based on the weekly sales plot,
# we can observe that volatility decreased and the mean sales level increased. 
# The highest and lowest total sales occurred in November.

# --- Quarterly Price Analysis ---
price_quarter_df = (
    df[['order_date', 'price']]
    .set_index('order_date')
    .resample('Q')
    .agg(np.mean)
    .reset_index()
)

# Visualize
# simple plot
price_quarter_df.set_index('order_date').plot(
    kind='line', subplots=True, layout=(1, 1), figsize=(10, 5)
)
plt.show()

# Based on the quarterly sales plot, 
# we can see that average total sales increased by the last quarter. 
# The year 2023 started with a drop in sales.

# --- ggplot Visualization for Weekly Revenue ---
!pip install scikit-misc

usd = currency_format(prefix="$", precision=2, big_mark=',')

ggplot(data=sales_week, mapping=aes(x='order_date', y='total_sales')) + \
geom_line() + \
geom_smooth(method='loess', color='blue', span=0.3) + \
scale_y_continuous(labels=usd) + \
labs(title='Revenue by Week', x='Date', y='Revenue') + \
theme_minimal() + \
expand_limits(y=0)

# Based on the weekly revenue graph,
# revenue started to rise from May 1st onward.
# There was also high volatility in November. 
# Black Friday sales influenced rise of the furniture.

# --- Sales by Style per Quarter ---
sales_by_quarter_Style = (
    df[['Style', 'order_date', 'total_sales']]
    .set_index('order_date')
    .groupby("Style")
    .resample('Q')
    .agg(np.sum)
    .drop(columns='Style')
    .reset_index()
)

# simple plot
df_pivot = sales_by_quarter_Style.pivot(
    index='order_date',
    columns='Style',
    values='total_sales'
).fillna(0)

df_pivot.plot(kind='line', subplots=True, layout=(5, 1), figsize=(15, 15))
plt.tight_layout()
plt.show()

# Based on the graphs of sales by furniture style,
# the Black Friday trend is evident due to the simultaneous rise and fall in sales across styles.
# Scandinavian furniture had the highest total sales.
# Traditional and Mid style had the lowest compared to others.

# --- Final Grouped Plot for Strategic Planning ---
df_pivot.plot(kind='line', figsize=(15, 7))
plt.title("Total Sales by Style per Quarter")
plt.ylabel("Total Sales")
plt.xlabel("Order Date")
plt.grid(True)
plt.legend(title='Style', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show()

# In my opinion, this plot is easier to read and more practical for strategic business planning
# due to the grouping by sales. Based on this, the business could consider focusing more 
# on Scandinavian-style furniture to increase revenue. Additionally, the graph helps 
# identify which styles could be scaled down due to lower sales performance.

```

###Tableu Projects

