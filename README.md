
## RETAIL CASE STUDY

As the retail industry becomes more and more competitive on a daily basis, meeting customer expectations through process optimization in service company operations is more crucial than ever. For a business to survive, it is critical to channel and manage data in a way that benefits both customers and revenues.

A retailer's customer data should ideally show how well it has reached and retained its clientele. Retailers used variables like conversion rate, average order value, buy frequency, and total amount spent in recent transactions to create reports that summarized consumer behavior. These metrics offered a broad understanding of the behavioural patterns of the clients.

The process of identifying and providing data-driven insights into previous and anticipated future consumer behavior is known as customer intelligence. Customer intelligence needs to develop derived metrics by combining transactional and behavioral data in order to be effective.

## GOAL

The main aim is to apply data analytics throughout the retail process for big players globally. This involves tracking popular products, forecasting sales and future demand through predictive simulation, and optimizing product placements and offers. The overarching goal is to enhance decision-making and strategy implementation at various stages of the retail business to stay competitive and ensure survival.

## DATA AVALIBILITY

o Customer: Customers information including demographics

o Transaction: Transactions of customers

o Product Heirarchy: Product information

## BUSINESS PROBLEM

A Retail store is required to analyze the day-to-day transactions and keep a track of its customers spread across various locations along with their purchases/returns across various categories.

## Import the library
    
        import pandas as pd (For data management)
    
        import numpy as np
    
        import matplotlib.pyplot as plt (For Plotting and Visualization)
    
        import seaborn as sns (For Plotting and Visualization)

## Import the data
    
        df = pd.read_csv("./Customer.csv") 
        df1 = pd.read_csv("./Transactions.csv")
        df2 = pd.read_csv("./prod_cat_info.csv")

1. Merge the datasets Customers, Product Hierarchy and Transactions as Customer_Final. Ensure to keep all customers who have done transactions with us and select the join type accordingly.

        customer_final = pd.merge(left=df,right=df1,left_on="customer_Id",right_on="cust_id",how="inner")

        customer_final = pd.merge(left=customer_final,right=df2,left_on="prod_subcat_code",right_on="prod_sub_cat_code",how="inner")
    
        customer_final

2. Prepare a summary report for the merged data set.

    a.) Get the column names and their corresponding data types
            
        customer_final.info()    
    
    b.) Top/Bottom 10 observations

        customer_final.head(10)
        
        customer_final.tail(10)

    c.) “Five-number summary” for continuous variables (min, Q1, median, Q3 and max)

        customer_final.describe().loc[["min","max","25%","50%","75%","mean"],]

    d.) Frequency tables for all the categorical variables

    Before finding the frequency of all categorical data first convert the DOB and tran_date to datetime

        customer_final["DOB"] = pd.to_datetime(customer_final["DOB"],format="mixed")

        customer_final["tran_date"] = pd.to_datetime(customer_final["tran_date"],format="mixed")

    Sort the data of object data type

        customer_final.select_dtypes(object).columns
    
    d.1) Gender

        customer_final["Gender"].value_counts()

    d.2) Store_Type

        customer_final["Store_type"].value_counts()

    d.3) Prod_cat

        customer_final["prod_cat"].value_counts()
    
    d.4) Prod_subcat

        customer_final["prod_subcat"].value_counts()

3. Generate histograms for all continuous variables and frequency bars for categorical variables.

    3.1.) Histograms for continuous variables
    
    Total_amt

        ttl_amt = customer_final[customer_final["total_amt"]>0]["total_amt"]
        
    Tax
        
        ttl_tax = customer_final[customer_final["Tax"]>0]["Tax"]
        
    Rate

        ttl_rate = customer_final[customer_final["Rate"]>0]["Rate"]
        
    DOB
        
        ttl_dob = customer_final["DOB"]

        fig,ax =plt.subplots(nrows=2,ncols=2,figsize=(10,10))
        sns.histplot(ax=ax[0,0],data=ttl_amt,kde=True,bins=30)
        sns.histplot(ax=ax[0,1],data=ttl_tax,kde=True,bins=30)
        sns.histplot(ax=ax[1,0],data=ttl_rate,kde=True,bins=30)
        sns.histplot(ax=ax[1,1],data=ttl_dob,kde=True,bins=30)
        plt.show()

![Histogram for continuous variable](https://github.com/gaurav94g/Retail-Case-Study/assets/117978888/05c4f298-595c-4feb-a597-e17dc276d364)

    3.2.) Histogram for categorical variable

        fig,ax = plt.subplots(ncols=1,nrows=2,figsize=(10,5))

    Gender

            a1 = customer_final["Gender"].value_counts().index
            a2 = customer_final["Gender"].value_counts()
            ax[0].bar(x=a1,height=a2)

    Store_type

            b1 = customer_final["Store_type"].value_counts().index
            b2 = customer_final["Store_type"].value_counts()
            ax[1].bar(x=b1,height=b2)

            plt.tight_layout()
            plt.show()

4. Calculate the following information using the merged dataset :
    
    a.) Time period of the available transaction data

        start_date = customer_final['tran_date'].dt.date.min()
        end_date = customer_final['tran_date'].dt.date.max()

        print(f"   Start Date: {start_date}")
        print(f"   End Date: {end_date}")
        print(f"   Time Period: {end_date - start_date}")
    
    b.) Count of transactions where the total amount of transaction was negative
        
        count_negative_transactions = customer_final[customer_final["total_amt"]<0]["total_amt"].count()

        print(f"Total Count: {count_negative_transactions}")

5. Analyze which product categories are more popular among females vs male customers.

        # Create the pivot table
        gender_product = pd.pivot_table(customer_final, values='transaction_id', index='prod_cat', columns='Gender', aggfunc='count', fill_value=0)

        # Get the unique values in the prod_cat column
        categories = customer_final["prod_cat"].unique()

        # Plot the bars
        plt.figure(figsize=(15,5))
        gender_product.plot(kind="bar")

        # Add labels and title
        plt.xlabel('Product Category')
        plt.ylabel('Number of Transactions')
        plt.title('Number of Transactions by Product Category and Gender')
        plt.legend()

        # Show the plot
        plt.show()

6. Which City code has the maximum customers and what was the percentage of customers from that city?

        city_customer = customer_final.groupby("city_code").agg({"customer_Id":"count"}).sort_values(ascending=False,by="customer_Id")
        city_customer

7. Which store type sells the maximum products by value and by quantity?

        store_type = customer_final.groupby(["Store_type"]).agg({"Qty":sum,"total_amt":sum})
store_type

8. What was the total amount earned from the "Electronics" and "Clothing" categories from Flagship Stores?

        flagship_amt = customer_final.groupby(["Store_type","prod_cat"]).agg({"total_amt":sum}).reset_index()
        
        flagship_amt.loc[(flagship_amt["Store_type"]=="Flagship store") & ((flagship_amt["prod_cat"]=="Electronics") | (flagship_amt["prod_cat"]=="Clothing")),:]

9. What was the total amount earned from "Male" customers under the "Electronics" category?

        ele_m = customer_final.loc[(customer_final["prod_cat"]=="Electronics") & (customer_final["Gender"]=="M"),:]

        print(f"The amount earned from male through electronic purchase: {ele_m["total_amt"].sum()}")

10. How many customers have more than 10 unique transactions, after removing all transactions which have any negative amounts?

        # Step 1: Remove transactions with negative amounts
            pst_tran = customer_final[customer_final["total_amt"]>0]

        # Step 2: Find customers with more than 10 unique transactions
            unq_c = pst_tran.groupby("customer_Id")["transaction_id"].nunique()

        # Print the result
            print("Number of customers with more than 10 unique transactions:", len(unq_c))

11. For all customers aged between 25 - 35:

    a.) What was the total amount spent for “Electronics” and “Books” product categories?
    
        import datetime as dte

        customer_final["AGE"] = (dte.datetime.now().year - customer_final["DOB"].dt.year)

        customer_final

        filter_data = customer_final[(customer_final["AGE"] >= 25) & (customer_final["AGE"] <= 35)]

        tota_amt = filter_data.loc[(filter_data["prod_cat"]=="Electronics") | (filter_data["prod_cat"]=="Book"),:]

        print(f"The total amount spent for “Electronics” and “Books” product categories: {tota_amt["total_amt"].sum()}")

    b.) What was the total amount spent by these customers between 1st Jan, 2014 to 1st Mar, 2014?

        filter_data = customer_final[(customer_final["tran_date"]>="2014-01-01") & (customer_final["tran_date"] <= "2014-03-01")]
        
        print(f"The total amount spent by customer between 1st Jan 2014 to 1st March 2014: {filter_data["total_amt"].sum()}")
