
# Project ETL Mini Project

The ETL (Extract, Transform, Load) mini project is designed to provide hands-on experience in building an ETL pipeline using Python, Pandas, and either Python dictionary methods or regular expressions. The project is aimed at individuals looking to practice and enhance their data processing skills. Participants will extract and transform data from provided CSV files, create new data files, and then use these files to create an Entity-Relationship Diagram (ERD) and table schema. Finally, the transformed data will be loaded into a PostgreSQL database for further analysis. Collaboration and effective communication with a partner are emphasized throughout the project to ensure successful completion. The project is structured to be completed within a week, making it ideal for students or professionals seeking to quickly apply ETL concepts in a practical setting.




## Table of Contents
1. [Installation](#installation)
2. [Roadmap](#roadmap)
- [Project Setup](#Project_Setup)
- [Data Extraction and Transformation](#Data_Extraction_and_Transformation)
    - [Create Category and Subcategory DataFrames](#Create_Category_and_Subcategory_DataFrames)
    - [Create Campaign DataFrame](Create_Campaign_DataFrame)
    - [Create Contacts DataFrame](Create_Contacts_DataFrame)
- [Database Schema and Table Creation](Database_Schema_and_Table_Creation)
    - [Design ERD and Table Schemas](Design_ERD_and_Table_Schemas)
    - [Create PostgreSQL Database and Tables](Create_PostgreSQL_Database_and_Tables)
- [Data Import and Verification](Data_Import_and_Verification)
    - [Import Data and Verify](Import_Data_and_Verify)
- [Final Review and Submission](Final_Review_and_Submission)
3. [Acknowledgements](#acknowledgements)


## Author

- [Kayla Vaccaro @biddlebird](https://www.github.com/biddlebird)


## Installation
```
import pandas as pd
import numpy as np
import re
from datetime import datetime as dt
pd.set_option('max_colwidth', 400)
```
    
# Roadmap

## Roadmap for the ETL Mini Project

### Project Setup
   - **Repository Creation**
     - Create a new GitHub repository named `Crowdfunding_ETL`.
     - Clone the repository to your local machine.
     - Rename the provided Jupyter notebook file to include your names (e.g., `ETL_Mini_Project_NRomanoff_JSmith.ipynb`).
     - Add the notebook file and the `Resources` folder (containing `crowdfunding.xlsx` and `contacts.xlsx`) to the repository.
     - Push the initial setup to GitHub
---

### Data Extraction and Transformation
   - **Create Category and Subcategory DataFrames**
     - Extract and transform the data from `crowdfunding.xlsx` to create a `category` DataFrame.
     - Ensure the `category` DataFrame has `category_id` and `category` columns.
     - Export the `category` DataFrame as `category.csv` and save it to the repository.
     - Extract and transform the data to create a `subcategory` DataFrame.
     - Ensure the `subcategory` DataFrame has `subcategory_id` and `subcategory` columns.
     - Export the `subcategory` DataFrame as `subcategory.csv` and save it to the repository.

```
category = crowdfunding_info_df['category'].unique().tolist()
sub_cat = crowdfunding_info_df['sub-category'].unique().tolist()

print(f"categories: {category}")
print(f"sub-categories: {sub_cat}")

category_ids = np.arange(1, 10)
subcategory_ids = np.arange(1, 25)
```

Use a list comprehension to add "cat" to each category_id. 
```
cat_ids = [f"cat{cat_id}" for cat_id in category_ids]
```
Use a list comprehension to add "subcat" to each subcategory_id.    
```
scat_ids = [f"subcat{scat_id}" for scat_id in subcategory_ids]
```

Create a category DataFrame with the category_id array as the category_id and categories list as the category name.
```
category_df = pd.DataFrame({
    'category_id': cat_ids,
    'category': category
})
```

Create a category DataFrame with the subcategory_id array as the subcategory_id and subcategories list as the subcategory name. 
```
subcategory_df = pd.DataFrame({
    'subcategory_id': scat_ids,
    'subcategory': sub_cat
})
```

Printing the DataFrames
```
print("Category DataFrame:")
print(category_df)

print("\nSubcategory DataFrame:")
print(subcategory_df)
```
Export categories_df and subcategories_df as CSV files.
```
category_df.to_csv("Resources/category.csv", index=False)

subcategory_df.to_csv("Resources/subcategory.csv", index=False)
```
---
   - **Create Campaign DataFrame**
     - Extract and transform the data from `crowdfunding.xlsx` to create a `campaign` DataFrame.
     - Ensure the `campaign` DataFrame includes necessary columns such as `cf_id`, `contact_id`, `company_name`, `description`, `goal`, `pledged`, `outcome`, `backers_count`, `country`, `currency`, `launch_date`, `end_date`, `category_id`, and `subcategory_id`.
     - Export the `campaign` DataFrame as `campaign.csv` and save it to the repository.
Create a copy of the crowdfunding_info_df DataFrame name campaign_df. 
```
campaign_df = crowdfunding_info_df.copy()
campaign_df.head()
```
Rename the blurb, launched_at, and deadline columns.
```
campaign_df.rename(columns={
    'blurb': 'description',
    'launched_at':'launched_date',
    'deadline': 'end_date'}, 
    inplace = True)
```
Convert the goal and pledged columns to a `float` data type.
```
campaign_df['goal'] = campaign_df['goal'].astype(float)
campaign_df['pledged'] = campaign_df['pledged'].astype(float)
```

Format the launched_date and end_date columns to datetime format
```
campaign_df['launched_date'] = pd.to_datetime(campaign_df['launched_date'])
campaign_df['end_date'] = pd.to_datetime(campaign_df['end_date'])
campaign_df = campaign_df.drop("category & sub-category", axis = 1)
```
Merge the campaign_df with the category_df on the "category" column and 
the subcategory_df on the "subcategory" column.

```
subcategory_df = subcategory_df.rename(columns={'subcategory': 'sub-category'})

campaign_df_mer = campaign_df.merge(category_df, on='category', how='left').merge(subcategory_df, on='sub-category', how='left')
```
Drop unwanted columns
```
columns_to_drop = ['sub-category', 'category', 'staff_pick', 'spotlight']
clean_mer_campdf = campaign_df_mer.drop(columns=columns_to_drop)
```
Export the DataFrame as a CSV file. 
```
clean_mer_campdf.to_csv("Resources/campaign.csv", index=False)
```
---
   - **Create Contacts DataFrame**
     - Choose either Python dictionary methods or regular expressions to extract and transform data from `contacts.xlsx`.
     - Create a `contacts` DataFrame with `contact_id`, `name`, and `email` columns.
     - Split the `name` column into `first_name` and `last_name`.
     - Clean the DataFrame and export it as `contacts.csv`, saving it to the repository.

Read the data into a Pandas DataFrame. Use the `header=3` parameter when reading in the data.
```
contact_info_df= pd.read_excel('Resources/contacts.xlsx', header=3)
contact_info_df.head()
contact_info_df_copy = contact_info_df.copy()
```
Extract the four-digit contact ID number.
```
contact_info_df_copy['contact_id'] = contact_info_df_copy['contact_info'].apply(
    lambda x: re.search(r'\d{4}', x).group() if re.search(r'\d{4}', x) else None
)
```
Convert the "contact_id" column to an int64 data type.
```
contact_info_df_copy['contact_id'] = contact_info_df_copy['contact_id'].astype(int)
```
Extract the name of the contact and add it to a new column.
```
name = r'"name": "(?P<name>.*?)"'
contact_info_df_copy['name'] = contact_info_df_copy['contact_info'].str.extract(name, expand=True)
```
Extract the email from the contacts and add the values to a new column.
```
email = r'"email": "(?P<name>.*?)"'
contact_info_df_copy['email'] = contact_info_df_copy['contact_info'].str.extract(email, expand=True)
```
Create a copy of the contact_info_df with the 
'contact_id', 'name', 'email' columns.
```
clean_contacts = contact_info_df_copy[['contact_id', 'name', 'email']]
```
Create a "first"name" and "last_name" column with the first and last names from the "name" column. 
```
clean_contacts[['first_name', 'last_name']] = clean_contacts['name'].str.split(' ', expand=True)
```

Drop the contact_name column
```
clean_contacts = clean_contacts.drop(columns=['name'])
```
Reorder the columns
```
clean_contacts = clean_contacts.iloc[:, [0,2,3,1]]
```
Export the DataFrame as a CSV file. 
```
clean_contacts.to_csv("Resources/contacts.csv", encoding='utf8', index=False)
```

### Database Schema and Table Creation
   - **Design ERD and Table Schemas**
     - Inspect the four CSV files and sketch an Entity-Relationship Diagram (ERD) using QuickDBD.
     - Use the ERD to create a table schema for each CSV file, specifying data types, primary keys, foreign keys, and constraints.
     - Save the database schema as `crowdfunding_db_schema.sql` and add it to the repository.

   - **Create PostgreSQL Database and Tables**
     - Create a new PostgreSQL database named `crowdfunding_db`.
     - Use the `crowdfunding_db_schema.sql` file to create tables in the database, ensuring the correct order to handle foreign keys.
     - Verify the creation of each table by running SELECT statements.

### Data Import and Verification
   - **Import Data and Verify**
     - Import each CSV file (`category.csv`, `subcategory.csv`, `campaign.csv`, `contacts.csv`) into its corresponding SQL table.
     - Verify that each table has the correct data by running SELECT statements for each table.

```
CREATE TABLE "Campaign" (
    "cf_id" int   NOT NULL,
    "contact_id" int   NOT NULL,
    "company_name" object   NOT NULL,
    "description" object   NOT NULL,
    "goal" float   NOT NULL,
    "pledged" float   NOT NULL,
    "outcome" object   NOT NULL,
    "backers_count" int   NOT NULL,
    "country" object   NOT NULL,
    "currency" object   NOT NULL,
    "launched_date" int   NOT NULL,
    "end_date" int   NOT NULL,
    "category" object   NOT NULL,
    "sub-category" object   NOT NULL,
    CONSTRAINT "pk_campaign" PRIMARY KEY ("cf_id")
);

CREATE TABLE "category" (
    "category_id" object NOT NULL,
    "category" object NOT NULL,
    CONSTRAINT "pk_category" PRIMARY KEY ("category_id")
)

CREATE TABLE "subcategory" (
    "subcategory_id" object NOT NULL,
    "subcategory" object NOT NULL,
    CONSTRAINT "pk_subcategory" PRIMARY KEY ("subcategory_id")
)

CREATE TABLE "Contacts" (
    "contact_info" object   NOT NULL,
    "contact_id" int   NOT NULL,
    "name" object   NOT NULL,
    "email" object   NOT NULL,
    CONSTRAINT "pk_Contacts" PRIMARY KEY (
        "contact_id"
     )
);

ALTER TABLE "Campaign" ADD CONSTRAINT "fk_Campaign_contact_id" FOREIGN KEY("contact_id")
REFERENCES "Contacts" ("contact_id");

ALTER TABLE "Campaign" ADD CONSTRAINT "fk_Campaign_category_id" FOREIGN KEY("category_id")
REFERENCES "Contacts" ("category_id");

ALTER TABLE "Campaign" ADD CONSTRAINT "fk_Campaign_subcategory_id" FOREIGN KEY("subcategory_id")
REFERENCES "Contacts" ("subcategory_id");
```

### Final Review and Submission
   - **Documentation and Final Checks**
     - Review the entire project for completeness and accuracy.
     - Ensure all transformations and load steps have been correctly implemented and documented.
     - Push the final version of the project to GitHub.
     - Coordinate with your partner to ensure both have the final version on your local machines.
## Acknowledgements

 - [Awesome Readme Templates](https://awesomeopensource.com/project/elangosundar/awesome-README-templates)
 - [Awesome README](https://github.com/matiassingers/awesome-readme)
 - [How to write a Good readme](https://bulldogjob.com/news/449-how-to-write-a-good-readme-for-your-github-project)
- [Thay Chansy](https://github.com/thaychansy)
- [Nate Sheibley](https://github.com/Nate-Sheibley)
