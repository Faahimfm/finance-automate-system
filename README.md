Simple Finance Dashboard

This project is a Streamlit-based finance dashboard that helps you upload your bank transaction CSV file, categorize your expenses, edit categories, and visualize your spending using charts.

Features
✔ Upload and read transaction CSV
Reads your CSV file using Pandas, cleans the data, and converts dates and amounts into the correct format.

✔ Automatic expense categorization
A categories.json file stores custom categories and keywords.
The app assigns categories based on transaction "Details".

✔ Manual category editing
You can:
Add new categories
Assign categories using a table editor
Save categorization keywords automatically

✔ Expense & Payment Summary

Total expenses by category
Interactive pie chart using Plotly
Payment totals in a separate tab

Code Explanation
###  Imports
import streamlit as st
import pandas as pd
import plotly.express as px
import json
import os


streamlit → Builds the UI
pandas → Reads and processes CSV files
plotly.express → Creates charts
json and os → Used to save/load category data

🖼 Page Setup
st.set_page_config(page_title="Simple Finance App", layout="wide")

Sets page title and layout to full width.

Category File Handling
Load or create categories.json
categories_file = "categories.json"
if "categories" not in st.session_state:
    st.session_state.categories = {"Uncategorized": []}


Creates default category if not found

Stores categories inside session_state so it persists while the app runs

if os.path.exists(categories_file):
    with open(categories_file, "r") as f:
        st.session_state.categories = json.load(f)


Loads saved categories from disk.

💾 Save Categories
def save_categories():
    with open(categories_file, "w") as f:
        json.dump(st.session_state.categories, f)


Saves your updated categories back to categories.json.

🧠 Automatic Categorization
def categorize_transactions(df):
    df["Category"] = "Uncategorized"


Adds a default category.

Then loops through categories and checks if transaction "Details" match any keyword.

lowered_keywords = [keyword.lower().strip() for keyword in keywords]


If match found → Assign category.

📥 Load & Clean Uploaded CSV
df = pd.read_csv(file)
df.columns = [col.strip() for col in df.columns]


Cleans column names.

df["Amount"] = df["Amount"].astype(str).str.replace(",", "").astype(float)
df["Date"] = pd.to_datetime(df["Date"])


Fixes numeric and date formats.

➕ Add Keyword to Category
def add_keyword_to_category(category, keyword):
    keyword = keyword.strip()


Adds the transaction “Details” text as a keyword so future CSV uploads auto-categorize.

🧩 Main App UI
uploaded_file = st.file_uploader("Upload your transaction CSV file")


Allows user to upload a CSV.

The app then splits data into:

debits_df = df[df["Debit/Credit"] == "Debit"]
credits_df = df[df["Debit/Credit"] == "Credit"]

📌 Tab 1 — Expenses (Debits)
Add new category
new_category = st.text_input("New Category Name")
add_button = st.button("Add Category")


Creates new categories manually.

Edit Expense Categories

A table editor allows users to modify the category column:

edited_df = st.data_editor(...)

Save changes & update keywords
if save_button:
    add_keyword_to_category(new_category, details)

Expense Summary Table
category_totals = debits_df.groupby("Category")["Amount"].sum()

Pie Chart
fig = px.pie(category_totals, values="Amount", names="Category")
st.plotly_chart(fig)

📌 Tab 2 — Payments (Credits)

Shows the total value of all credit transactions:

st.metric("Total Payments", f"{total_payments:,.2f} LKR")

▶ Running the App

In your terminal:

streamlit run app.py


Replace app.py with your filename.

🎉 Conclusion

This Streamlit app provides:

Smart transaction categorization

Editable categories

Visual insights into expenses

Simple, beginner-friendly financial tracking