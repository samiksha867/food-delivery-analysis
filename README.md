# food-delivery-analysis


import pandas as pd

orders = pd.read_csv("orders.csv")
users = pd.read_json("users.json")
print("orders column:",orders.columns)
print("users column:",users.columns)

import sqlite3

conn = sqlite3.connect(":memory:")

with open("restaurants.sql", "r") as f:
    sql_script = f.read()

conn.executescript(sql_script)

restaurants = pd.read_sql("SELECT * FROM restaurants", conn)
orders_users = orders.merge(
    users,
    on="user_id",
    how="left"
)
print("restaurant_id" in orders_users.columns)
#print("restaurant_id" in restaurant.columns)

#restaurants.rename(columns= { "RestaurantID":"restaurant_id"}, inplace=True )
final_df=orders_users.merge(restaurants,on="restaurant_id" ,how="left")
final_df.to_csv("final_food_delivery_dataset.csv", index=False)
print("final dataset created successfully")
conn = sqlite3.connect("restaurants.db")


import pandas as pd

final_df = pd.read_csv("final_food_delivery_dataset.csv")

gold_orders_count = final_df[final_df['membership'] == 'Gold'].shape[0]

print(gold_orders_count)


#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Hyderabad orders and calculate revenue
hyderabad_revenue = final_df[final_df['city'] == 'Hyderabad']['total_amount'].sum()

# Round to nearest integer
print(round(hyderabad_revenue))
#print(final_df.columns)

#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

distinct_users = final_df['user_id'].nunique()

print(distinct_users)

# Q

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Gold member orders
gold_orders = final_df[final_df['membership'] == 'Gold']

# Calculate average order value
avg_order_value_gold = gold_orders['total_amount'].mean()

# 2 decimals
print(round(avg_order_value_gold, 2))


final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter orders for restaurants with rating >= 4.5
high_rating_orders = final_df[final_df['rating'] >= 4.5]

# Count orders
print(high_rating_orders.shape[0])

#Q last
final_df = pd.read_csv("final_food_delivery_dataset.csv")
gold_df = final_df[final_df['membership'] == 'Gold']

city_revenue = gold_df.groupby('city')['total_amount'].sum()
top_city = city_revenue.idxmax()

orders_in_top_city = gold_df[gold_df['city'] == top_city].shape[0]

print(top_city, orders_in_top_city)

final_df=pd.read_csv("final_food_delivery_dataset.csv")
print(final_df.shape[0])

import os
print(os.getcwd())

import pandas as pd

orders = pd.read_csv("orders.csv")
print(orders.columns)
# Sum total order amount per user
user_total = orders.groupby('user_id')['total_amount'].sum().reset_index()

# Filter users with total > 1000
high_spenders = user_total[user_total['total_amount'] > 1000]

# Number of users
num_users = high_spenders['user_id'].nunique()
print("Number of users:", num_users)

#
# Load your merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")  # make sure this exists

# Define rating ranges
bins = [3.0, 3.5, 4.0, 4.5, 5.0]
labels = ['3.0–3.5', '3.6–4.0', '4.1–4.5', '4.6–5.0']

final_df['rating_range'] = pd.cut(final_df['rating'], bins=bins, labels=labels, right=True)

# Sum total revenue per rating range
revenue_by_rating = final_df.groupby('rating_range')['total_amount'].sum()

print("Total revenue by rating range:")
print(revenue_by_rating)

# Find the range with the highest revenue
max_range = revenue_by_rating.idxmax()
max_revenue = revenue_by_rating.max()

print(f"\nRating range with highest total revenue: {max_range} (₹{max_revenue})")


#import pandas as pd

# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter only Gold members
gold_members = final_df[final_df['membership'] == 'Gold']

# Group by city and calculate average order value
avg_order_city = gold_members.groupby('city')['total_amount'].mean()

print("Average order value per city (Gold members):")
print(avg_order_city)

# Find city with highest average
top_city = avg_order_city.idxmax()
top_avg = avg_order_city.max()

print(f"\nCity with highest average order value among Gold members: {top_city} (₹{top_avg:.2f})")

# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Count distinct restaurants per cuisine
restaurants_per_cuisine = final_df.groupby('cuisine')['restaurant_id'].nunique()
print("Number of distinct restaurants per cuisine:")
print(restaurants_per_cuisine)

# Total revenue per cuisine
revenue_per_cuisine = final_df.groupby('cuisine')['total_amount'].sum()
print("\nTotal revenue per cuisine:")
print(revenue_per_cuisine)

# Combine info in a single DataFrame
summary = pd.DataFrame({
    'num_restaurants': restaurants_per_cuisine,
    'total_revenue': revenue_per_cuisine
}).sort_values('num_restaurants')

print("\nSummary of cuisine:")
print(summary)

low_restaurants = summary['num_restaurants'].min()
possible_cuisines = summary[summary['num_restaurants'] == low_restaurants]
top_cuisine = possible_cuisines['total_revenue'].idxmax()

print(f"\nCuisine with fewest restaurants but still significant revenue: {top_cuisine}")



# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Total number of orders
total_orders = len(final_df)

# Number of orders by Gold members
gold_orders = len(final_df[final_df['membership'] == 'Gold'])

# Percentage
percentage_gold = round((gold_orders / total_orders) * 100)

print(f"Percentage of total orders by Gold members: {percentage_gold}%")


# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

restaurant_stats = final_df.groupby('restaurant_name').agg(
    total_orders=('total_price', 'count'),
    avg_order_value=('total_price', 'mean')
).reset_index()

low_order_restaurants = restaurant_stats[restaurant_stats['total_orders'] < 20]

#  Find the restaurant with highest average order value
top_restaurant = low_order_restaurants.loc[low_order_restaurants['avg_order_value'].idxmax()]

print("Restaurant with highest average order value but less than 20 orders:")
print(f"{top_restaurant['restaurant_name']}(Average order value: ₹{top_restaurant['avg_order_value']:.2f}, Total orders: {top_restaurant['total_orders']})")


# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

revenue_combo = final_df.groupby(['membership', 'cuisine'])['total_amount'].sum().reset_index()
top_combo = revenue_combo.loc[revenue_combo['total_amount'].idxmax()]
print("Combination with highest revenue:")
print(f"{top_combo['membership']} + {top_combo['cuisine']} (Revenue: ₹{top_combo['total_amount']:.2f})")
restaurants_per_cuisine = final_df.groupby('cuisine')['restaurant_id'].nunique()
revenue_per_cuisine = final_df.groupby('cuisine')['total_amount'].sum()

# Load dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Convert order_date to datetime if not already
final_df['order_date'] = pd.to_datetime(final_df['order_date'])

# Extract month
final_df['month'] = final_df['order_date'].dt.month

# Map month to quarter
def month_to_quarter(month):
    if month in [1, 2, 3]:
        return 'Q1 (Jan–Mar)'
    elif month in [4, 5, 6]:
        return 'Q2 (Apr–Jun)'
    elif month in [7, 8, 9]:
        return 'Q3 (Jul–Sep)'
    else:
        return 'Q4 (Oct–Dec)'

final_df['quarter'] = final_df['month'].apply(month_to_quarter)

# Sum revenue per quarter
revenue_per_quarter = final_df.groupby('quarter')['total_amount'].sum()
print("Total revenue per quarter:")
print(revenue_per_quarter)

# Find quarter with highest revenue
top_quarter = revenue_per_quarter.idxmax()
max_revenue = revenue_per_quarter.max()
print(f"\nQuarter with highest total revenue: {top_quarter} (₹{max_revenue:.2f})")




import pandas as pd

final_df = pd.read_csv("final_food_delivery_dataset.csv")

gold_orders_count = final_df[final_df['membership'] == 'Gold'].shape[0]

print(gold_orders_count)


#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Hyderabad orders and calculate revenue
hyderabad_revenue = final_df[final_df['city'] == 'Hyderabad']['total_amount'].sum()

# Round to nearest integer
print(round(hyderabad_revenue))
#print(final_df.columns)

#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

distinct_users = final_df['user_id'].nunique()

print(distinct_users)

# Q

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Gold member orders
gold_orders = final_df[final_df['membership'] == 'Gold']

# Calculate average order value
avg_order_value_gold = gold_orders['total_amount'].mean()

# 2 decimals
print(round(avg_order_value_gold, 2))


final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter orders for restaurants with rating >= 4.5
high_rating_orders = final_df[final_df['rating'] >= 4.5]

# Count orders
print(high_rating_orders.shape[0])

#Q last
final_df = pd.read_csv("final_food_delivery_dataset.csv")
gold_df = final_df[final_df['membership'] == 'Gold']

city_revenue = gold_df.groupby('city')['total_amount'].sum()
top_city = city_revenue.idxmax()

orders_in_top_city = gold_df[gold_df['city'] == top_city].shape[0]

print(top_city, orders_in_top_city)

final_df=pd.read_csv("final_food_delivery_dataset.csv")
print(final_df.shape[0])

import os
print(os.getcwd())

import pandas as pd

orders = pd.read_csv("orders.csv")
print(orders.columns)
# Sum total order amount per user
user_total = orders.groupby('user_id')['total_amount'].sum().reset_index()

# Filter users with total > 1000
high_spenders = user_total[user_total['total_amount'] > 1000]

# Number of users
num_users = high_spenders['user_id'].nunique()
print("Number of users:", num_users)

#
# Load your merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")  # make sure this exists

# Define rating ranges
bins = [3.0, 3.5, 4.0, 4.5, 5.0]
labels = ['3.0–3.5', '3.6–4.0', '4.1–4.5', '4.6–5.0']

final_df['rating_range'] = pd.cut(final_df['rating'], bins=bins, labels=labels, right=True)

# Sum total revenue per rating range
revenue_by_rating = final_df.groupby('rating_range')['total_amount'].sum()

print("Total revenue by rating range:")
print(revenue_by_rating)

# Find the range with the highest revenue
max_range = revenue_by_rating.idxmax()
max_revenue = revenue_by_rating.max()

print(f"\nRating range with highest total revenue: {max_range} (₹{max_revenue})")


#import pandas as pd

# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter only Gold members
gold_members = final_df[final_df['membership'] == 'Gold']

# Group by city and calculate average order value
avg_order_city = gold_members.groupby('city')['total_amount'].mean()

print("Average order value per city (Gold members):")
print(avg_order_city)

# Find city with highest average
top_city = avg_order_city.idxmax()
top_avg = avg_order_city.max()

print(f"\nCity with highest average order value among Gold members: {top_city} (₹{top_avg:.2f})")

# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Count distinct restaurants per cuisine
restaurants_per_cuisine = final_df.groupby('cuisine')['restaurant_id'].nunique()
print("Number of distinct restaurants per cuisine:")
print(restaurants_per_cuisine)

# Total revenue per cuisine
revenue_per_cuisine = final_df.groupby('cuisine')['total_amount'].sum()
print("\nTotal revenue per cuisine:")
print(revenue_per_cuisine)

# Combine info in a single DataFrame
summary = pd.DataFrame({
    'num_restaurants': restaurants_per_cuisine,
    'total_revenue': revenue_per_cuisine
}).sort_values('num_restaurants')

print("\nSummary of cuisine:")
print(summary)

low_restaurants = summary['num_restaurants'].min()
possible_cuisines = summary[summary['num_restaurants'] == low_restaurants]
top_cuisine = possible_cuisines['total_revenue'].idxmax()

print(f"\nCuisine with fewest restaurants but still significant revenue: {top_cuisine}")



# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Total number of orders
total_orders = len(final_df)

# Number of orders by Gold members
gold_orders = len(final_df[final_df['membership'] == 'Gold'])

# Percentage
percentage_gold = round((gold_orders / total_orders) * 100)

print(f"Percentage of total orders by Gold members: {percentage_gold}%")


# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

restaurant_stats = final_df.groupby('restaurant_name').agg(
    total_orders=('total_price', 'count'),
    avg_order_value=('total_price', 'mean')
).reset_index()

low_order_restaurants = restaurant_stats[restaurant_stats['total_orders'] < 20]

#  Find the restaurant with highest average order value
top_restaurant = low_order_restaurants.loc[low_order_restaurants['avg_order_value'].idxmax()]

print("Restaurant with highest average order value but less than 20 orders:")
print(f"{top_restaurant['restaurant_name']}(Average order value: ₹{top_restaurant['avg_order_value']:.2f}, Total orders: {top_restaurant['total_orders']})")


# Load merged dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

revenue_combo = final_df.groupby(['membership', 'cuisine'])['total_amount'].sum().reset_index()
top_combo = revenue_combo.loc[revenue_combo['total_amount'].idxmax()]
print("Combination with highest revenue:")
print(f"{top_combo['membership']} + {top_combo['cuisine']} (Revenue: ₹{top_combo['total_amount']:.2f})")
restaurants_per_cuisine = final_df.groupby('cuisine')['restaurant_id'].nunique()
revenue_per_cuisine = final_df.groupby('cuisine')['total_amount'].sum()

# Load dataset
final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Convert order_date to datetime if not already
final_df['order_date'] = pd.to_datetime(final_df['order_date'])

# Extract month
final_df['month'] = final_df['order_date'].dt.month

# Map month to quarter
def month_to_quarter(month):
    if month in [1, 2, 3]:
        return 'Q1 (Jan–Mar)'
    elif month in [4, 5, 6]:
        return 'Q2 (Apr–Jun)'
    elif month in [7, 8, 9]:
        return 'Q3 (Jul–Sep)'
    else:
        return 'Q4 (Oct–Dec)'

final_df['quarter'] = final_df['month'].apply(month_to_quarter)

# Sum revenue per quarter
revenue_per_quarter = final_df.groupby('quarter')['total_amount'].sum()
print("Total revenue per quarter:")
print(revenue_per_quarter)

# Find quarter with highest revenue
top_quarter = revenue_per_quarter.idxmax()
max_revenue = revenue_per_quarter.max()
print(f"\nQuarter with highest total revenue: {top_quarter} (₹{max_revenue:.2f})")




import pandas as pd

final_df = pd.read_csv("final_food_delivery_dataset.csv")

gold_orders_count = final_df[final_df['membership'] == 'Gold'].shape[0]

print(gold_orders_count)


#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Hyderabad orders and calculate revenue
hyderabad_revenue = final_df[final_df['city'] == 'Hyderabad']['total_amount'].sum()

# Round to nearest integer
print(round(hyderabad_revenue))
#print(final_df.columns)

#

final_df = pd.read_csv("final_food_delivery_dataset.csv")

distinct_users = final_df['user_id'].nunique()

print(distinct_users)

# Q

final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter Gold member orders
gold_orders = final_df[final_df['membership'] == 'Gold']

# Calculate average order value
avg_order_value_gold = gold_orders['total_amount'].mean()

# 2 decimals
print(round(avg_order_value_gold, 2))


final_df = pd.read_csv("final_food_delivery_dataset.csv")

# Filter orders for restaurants with rating >= 4.5
high_rating_orders = final_df[final_df['rating'] >= 4.5]

# Count orders
print(high_rating_orders.shape[0])

#Q last
final_df = pd.read_csv("final_food_delivery_dataset.csv")
gold_df = final_df[final_df['membership'] == 'Gold']

city_revenue = gold_df.groupby('city')['total_amount'].sum()
top_city = city_revenue.idxmax()

orders_in_top_city = gold_df[gold_df['city'] == top_city].shape[0]

print(top_city, orders_in_top_city)

final_df=pd.read_csv("final_food_delivery_dataset.csv")
print(final_df.shape[0])

