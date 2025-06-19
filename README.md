# ShelfSense-A-Smart-Retail-Intelligence-Dashboard
import pandas as pd

df = pd.read_csv('shelfsense_raw_data.csv')

df.head()

df.isnull().sum()

df['Date'] = pd.to_datetime(df['Date'])

# Total shrinkage value (money lost)
df["Shrinkage_Value"] = df["Shrinkage"] * df["Unit_Price"]

# Total possible revenue (if there was no shrinkage)
df["Ideal_Revenue"] = (df["Units_Sold"] + df["Shrinkage"]) * df["Unit_Price"]

# Loss % per transaction
df["Loss_Percent"] = (df["Shrinkage_Value"] / df["Ideal_Revenue"]) * 100

# Round for cleaner visuals
df["Loss_Percent"] = df["Loss_Percent"].round(2)

# TOTAL revenue generated
total_revenue = df["Revenue"].sum()

# TOTAL shrinkage value
total_loss = df["Shrinkage_Value"].sum()

# Overall loss percentage
overall_loss_percent = (total_loss / (total_revenue + total_loss)) * 100

# Average daily revenue
avg_daily_revenue = df.groupby("Date")["Revenue"].sum().mean()

print("💸 Total Revenue: $", round(total_revenue, 2))
print("🕳️ Total Shrinkage Loss: $", round(total_loss, 2))
print("📉 Overall Loss %:", round(overall_loss_percent, 2), "%")
print("📆 Avg Daily Revenue: $", round(avg_daily_revenue, 2))

# Total Revenue by Store
revenue_by_store = df.groupby("Store_ID")["Revenue"].sum().sort_values(ascending=False)

# Total Loss by Store
loss_by_store = df.groupby("Store_ID")["Shrinkage_Value"].sum().sort_values(ascending=False)

# Loss % by Store
loss_pct_by_store = (loss_by_store / (revenue_by_store + loss_by_store)) * 100

# Display results
print("🏬 Total Revenue by Store:")
print(revenue_by_store)

print("\n🔥 Shrinkage Loss by Store:")
print(loss_by_store)

print("\n📉 Loss % by Store:")
print(loss_pct_by_store.round(2))

# Revenue by Product Category
revenue_by_cat = df.groupby("Category")["Revenue"].sum().sort_values(ascending=False)

# Loss % by Category
loss_by_cat = df.groupby("Category")["Shrinkage_Value"].sum() / df.groupby("Category")["Ideal_Revenue"].sum() * 100

print("Revenue by Category:")
print(revenue_by_cat)

print("\n Loss % by Category:")
print(loss_by_cat.round(2))

# Daily total revenue across all stores
daily_revenue = df.groupby("Date")["Revenue"].sum()

# Display the first few rows
print(daily_revenue.head())
