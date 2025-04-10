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


### Wayfair Sales & Product Trends Analysis

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

# --- Data Wrangling ---
df = orderline_joined_df.copy()
df['Type'], df['Style'] = df['product.info'].str.split('-', expand=True)[[0, 1]]
df = df.drop(columns='shop.id')
df.columns = df.columns.str.replace(".", "_")
df['total_sales'] = df['price'] * df['quantity']

# --- Top 10 Orders ---
top_10_orders = df.sort_values('total_sales', ascending=False).head(10)
print(top_10_orders)

# --- Data Preview ---
print(product_df.head(5))
print(orderlines_df.head(5))

# --- Top 5 Materials ---
top5_material = product_df['material'].value_counts().nlargest(5).sort_values(ascending=True)
top5_material.plot(kind="barh", title="Top 5 Materials")
plt.show()

# --- Top 5 Product Types ---
top5_type = df['Type'].value_counts().nlargest(5).sort_values(ascending=True)
top5_type.plot(kind="barh", title="Top 5 Product Types")
plt.show()

# --- Top 5 Product Styles ---
top5_style = df['Style'].value_counts().nlargest(5).sort_values(ascending=True)
top5_style.plot(kind="barh", title="Top 5 Product Styles")
plt.show()

# --- Save Processed Data ---
os.makedirs("orderlines_wrangled_df", exist_ok=True)
df.to_pickle("/content/orderlines_wrangled_df.pkl")

# --- Reload for Safety ---
df = pd.read_pickle("/content/orderlines_wrangled_df.pkl")
df = pd.DataFrame(df)

# --- Weekly Sales Aggregation ---
sales_week = (
    df[['order_date', 'total_sales']]
    .set_index('order_date')
    .resample('W')
    .sum()
    .reset_index()
)

# --- Plot Weekly Sales ---
sales_week.set_index('order_date').plot(
    kind='line', subplots=True, figsize=(10, 5), title="Weekly Sales Trend"
)
plt.show()

# --- Quarterly Average Price ---
price_quarter_df = (
    df[['order_date', 'price']]
    .set_index('order_date')
    .resample('Q')
    .mean()
    .reset_index()
)

price_quarter_df.set_index('order_date').plot(
    kind='line', figsize=(10, 5), title="Quarterly Average Price"
)
plt.show()

# --- ggplot Visualization for Weekly Revenue ---
!pip install scikit-misc

usd = currency_format(prefix="$", precision=2, big_mark=",")

(
    ggplot(data=sales_week, mapping=aes(x='order_date', y='total_sales')) +
    geom_line() +
    geom_smooth(method='loess', color='blue', span=0.3) +
    scale_y_continuous(labels=usd) +
    labs(title='Revenue by Week', x='Date', y='Revenue') +
    theme_minimal() +
    expand_limits(y=0)
)

# --- Sales by Style Per Quarter ---
sales_by_quarter_style = (
    df[['Style', 'order_date', 'total_sales']]
    .set_index('order_date')
    .groupby('Style')
    .resample('Q')
    .sum()
    .drop(columns='Style')
    .reset_index()
)

# --- Pivot and Plot Each Style Separately ---
df_pivot = (
    sales_by_quarter_style
    .pivot(index='order_date', columns='Style', values='total_sales')
    .fillna(0)
)

df_pivot.plot(kind='line', subplots=True, layout=(5, 1), figsize=(15, 15), legend=False)
plt.tight_layout()
plt.show()

# --- Final Grouped Plot for Strategic Insight ---
df_pivot.plot(kind='line', figsize=(15, 7))
plt.title("Total Sales by Style per Quarter")
plt.xlabel("Order Date")
plt.ylabel("Total Sales")
plt.grid(True)
plt.legend(title='Style', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.tight_layout()
plt.show()

```

###Tableu Projects

