Local URL: http://localhost:8503
Network URL: http://172.23.120.121:8503

Description:
The provided code is a Streamlit application designed to analyze and visualize sales data from CSV files sourced from COMC (Check Out My Cards), a platform for buying and selling sports cards. The application allows users to upload multiple CSV files, consolidates the data, and offers various interactive options for filtering and analyzing the sales data based on specified criteria.

Key Features of the Project:

Data Upload and Consolidation: Users can upload multiple CSV files containing sales data, which the application reads and combines into a single DataFrame for analysis.

Date Range Filtering: The application enables users to filter the sales data based on specific date ranges for both the date sold and the acquisition date. Options include custom ranges as well as predefined ranges like the last 7, 30, 90, or 180 days.

Sales Price Filtering: Users can select a range of sales prices to focus on specific segments of the market, helping to hone in on particular cards or price points of interest.

Category Selection: The application allows users to filter data based on various sports categories (e.g., baseball, basketball, football), enabling a tailored analysis based on user interest.

Data Visualization: The code generates visual representations of the filtered data, including bar graphs showing sport breakdowns and decade distributions, as well as tables that summarize key statistics and top-selling players.

Profit and Markup Analysis: The application calculates important financial metrics such as profit and markup based on the sales and purchase prices. Users can view these metrics summarized by sport and in quantile distributions.

Interactive Interface: Streamlit provides an interactive user interface, allowing users to easily explore the dataset and visualize findings without needing extensive programming knowledge.

Impact:
This project is valuable for sports card collectors, investors, and sellers looking to understand market trends, track sales performance, and make informed decisions based on sales data. By transforming raw CSV data into visually engaging and informative analyses, the application helps users grasp complex information quickly and effectively, ultimately enhancing their trading strategies on the COMC platform.


    import streamlit as st
    import pandas as pd
    import matplotlib.pyplot as plt
    from datetime import datetime, timedelta
    import re

    st.set_page_config(layout="wide")

    def extract_year(set_name):
      match = re.search(r'\b\d{4}\b', set_name) 
      ## grabs 4 consecutive integers from set name ##
      ## \b is word boundary, 4 limit ##
      if match:
        return int(match.group())
      else:
        return None
      ## grabs years from set_name for graphs later ##



    def extract_name(description):
    # Check if the description is a string #
        if isinstance(description, str):
            return re.sub(r'\s*\(.*?\)\s*', '', description)
        else:
            return ''

    ## re removes any text in () and whitespace before and after to just get the names ##
    ## keeps text after '-', removes text and whitespace before it ##


    def main():
      st.title("ComC Seller Stats - Analysis") 
      ## makes title for page ##

      st.write("Hello! Welcome to the 'ComC Seller Stats - Analysis' page.")
      st.write("In order to begin, import atleast one CSV from the Seller Stats page on your ComC Account!")
      st.write("This page has been created by UCLA student Zion Vartanian.") 
      ## makes smaller text under title ##

      files = st.file_uploader("Upload multiple spreadsheets CSV Only", type=["csv"], accept_multiple_files = True)
      ## makes file uploader with the text in quotes ##

      if files: 
      # means if files=True which means if a file(s) is uploaded ##
        df_list = []
        for file in files:
            df_file = pd.read_csv(file)
            df_list.append(df_file) 
            ## how to append multiple CSV's easily ##
    
        df = pd.concat(df_list, ignore_index=True)
        ## Ignores index and makes new ones based on addition of file(s) ##

        st.title("Sold Date Range Selector")
        date_range_option = st.selectbox("Select Date Range", ["Custom", "Last 7 Days", "Last 30 Days", "Last 90 Days", "Last 180 Days"], key = 'sold_date')

        if date_range_option == 'Custom':
            min_date = pd.to_datetime(df['Date Sold']).min().date()
        ## This line converts the 'Date Sold' column in df (the merged DataFrame of all uploaded CSV files) 
        # to a datetime format using pd.to_datetime. Then finds earliest date by .min(). Then .date() just extracts the date
        # and forgets the timestamp. ## 
            max_date = pd.to_datetime(df['Date Sold']).max().date()
            start_date = st.date_input("Start Date", min_value=min_date, max_value=max_date, value=min_date)
            end_date = st.date_input("End Date", min_value=min_date, max_value=max_date, value=max_date)
        elif date_range_option == 'Last 7 Days':
            end_date = datetime.now().date() 
            ## Gets current date and time, then .date() only gets date ##
            start_date = end_date - timedelta(days=6) 
            ## Subtracts current day by 6 days to demonstrate the range of the last 7 Days ##
        elif date_range_option == 'Last 30 Days':
            end_date = datetime.now().date()
            start_date = end_date - timedelta(days=29)
        elif date_range_option == 'Last 90 Days':
            end_date = datetime.now().date()
            start_date = end_date - timedelta(days=89)
        elif date_range_option == 'Last 180 Days':
            end_date = datetime.now().date()
            start_date = end_date - timedelta(days=179)
    ## timedelta() is a duration that you can add or subtract in code ##

        filtered_df = df[(pd.to_datetime(df["Date Sold"]).dt.date >= start_date)&
                     (pd.to_datetime(df["Date Sold"]).dt.date <= end_date)]
    ## filters df to have rows only where the "Date Sold" falls between start and end
    ## .dt.date extracts only the date part
    ## rows meeting both condiitons stay in filtered_df, while the rest are excluded ##

        st.title("Acquired Date Range Selector")
        date_range_option = st.selectbox("Select Date Range", ["Custom", "Last 7 Days", "Last 30 Days", "Last 90 Days", "Last 180 Days"], key = 'acquired_date')

        if date_range_option == 'Custom':
            min_date_acq = pd.to_datetime(df['Acquisition Date']).min().date() 
            max_date_acq = pd.to_datetime(df['Acquisition Date']).max().date()
            start_date_acq = st.date_input("Start Date", min_value=min_date_acq, max_value=max_date_acq, value=min_date_acq)
            end_date_acq = st.date_input("End Date", min_value=min_date_acq, max_value=max_date_acq, value=max_date_acq)
        elif date_range_option == 'Last 7 Days':
            end_date_acq = datetime.now().date() 
            start_date_acq = end_date - timedelta(days=6) 
        elif date_range_option == 'Last 30 Days':
            end_date_acq = datetime.now().date()
            start_date_acq = end_date - timedelta(days=29)
        elif date_range_option == 'Last 90 Days':
            end_date_acq = datetime.now().date()
            start_date_acq = end_date - timedelta(days=89)
        elif date_range_option == 'Last 180 Days':
            end_date_acq = datetime.now().date()
            start_date_acq = end_date - timedelta(days=179)

        filtered_df = df[(pd.to_datetime(df["Acquisition Date"]).dt.date >= start_date_acq)&
                     (pd.to_datetime(df["Acquisition Date"]).dt.date <= end_date_acq)]

        st.title("Sales Price Selector")
        sales_price_option = st.selectbox("Select Sales Price Range", ["Custom", "Under 39 cents", "Under 75 cents", "Under $2.51", "Over $2.50"], key = 'Sales_price_final')

        if sales_price_option == 'Custom':
            min_price = 0.01
            max_price = 100000
            start_price = st.number_input("Start Price", min_value=float(min_price), max_value=float(max_price), value=float(min_price))
            end_price = st.number_input("End Price", min_value=float(min_price), max_value=float(max_price), value=float(max_price))
        elif sales_price_option == 'Under 39 cents':
            start_price = 0.01
            end_price = 0.39
        elif sales_price_option == 'Under 75 cents':
            start_price = 0.01
            end_price = 0.75
        elif sales_price_option == 'Under $2.51':
            start_price = 0.01
            end_price = 2.50
        elif sales_price_option == 'Over $2.50':
            start_price = 2.51
            end_price = 100000
    
        filtered_df = filtered_df[(filtered_df["Sales Price"] >= start_price)&(filtered_df["Sales Price"] <= end_price)]
    
    ## filtered_df used on outside so that when we filter sales price it only does so  ##
    ## for values that are accepted by the previous acquisition and date range filters ##

        st.title("added vs flipped selector")
        added_select = st.checkbox("Added", value=True)
        flipped_select = st.checkbox("Flipped", value=True)

        if added_select and not flipped_select:
            filtered_df = filtered_df[filtered_df['Purchase Price'].isnull()]
        ## if you add card to site there is no purchase price ##
        elif flipped_select and not added_select:
            filtered_df = filtered_df[filtered_df['Purchase Price'].notnull()]
        elif flipped_select and added_select:
            filtered_df = filtered_df
        else:
            st.write("Please Select One of The Checkpoints")

    
        st.title("Category Selector")
        col1, col2, col3 = st.columns(3)
        ## for 3 columns of options ##

        with col1:
            Baseball_Select = st.checkbox("Baseball", value=True)
            Basketball_Select = st.checkbox("Basketball", value=True)
            Football_Select = st.checkbox("Football", value=True)
            Hockey_Select = st.checkbox("Hockey", value=True)
            Soccer_Select = st.checkbox("Soccer", value=True)
    
        with col2:
            Golf_Select = st.checkbox("Golf", value=True)
            Racing_Select = st.checkbox("Racing", value=True)
            MultiSport_Select = st.checkbox("MultiSport", value=True)
            MMA_Select = st.checkbox("MMA", value=True)
            Boxing_Select = st.checkbox("Boxing", value=True)
            Olympics_Select = st.checkbox("Olympics", value=True)
    
        with col3:
            Wrestling_Select = st.checkbox("Wrestling", value=True)
            Non_Sports_Select = st.checkbox("Non-Sports", value=True)
            Star_Wars_Select = st.checkbox("Star Wars", value=True)
            Marvel_Select = st.checkbox("Marvel", value=True)
            Pokemon_Select = st.checkbox("Pokemon", value=True)


        selected_categories = []
    
        if Baseball_Select:
            selected_categories.append("Baseball")
        if Basketball_Select:
            selected_categories.append("Basketball")
        if Football_Select:
            selected_categories.append("Football")
        if Hockey_Select:
            selected_categories.append("Hockey")
        if Soccer_Select:
            selected_categories.append("Soccer")
        if Golf_Select:
            selected_categories.append("Golf")
        if Racing_Select:
            selected_categories.append("Racing")
        if MultiSport_Select:
            selected_categories.append("MultiSport")
        if MMA_Select:
            selected_categories.append("MMA")
        if Boxing_Select:
            selected_categories.append("Boxing")
        if Olympics_Select:
            selected_categories.append("Olympics")
        if Wrestling_Select:
            selected_categories.append("Wrestling")
        if Non_Sports_Select:
            selected_categories.append("Non-Sports")
        if Star_Wars_Select:
            selected_categories.append("Star Wars")
        if Marvel_Select:
            selected_categories.append("Marvel")
        if Pokemon_Select:
            selected_categories.append("Pokemon")

        if selected_categories:
            filtered_df = filtered_df[filtered_df["Sport"].isin(selected_categories)]
        else:
            st.write("Please Select at least One Category")


        filtered_df['Year'] = filtered_df['Set Name'].apply(extract_year)
    ## for each value in 'Set Name', the extract_year function ##
    ## from the beginning of the code is applied ##

        col4, col5, col6 = st.columns(3)

        with col4:
            st.title("Sport Breakdown")
            sport_counts = filtered_df["Sport"].value_counts()
            fig, ax = plt.subplots()
            ax.bar(sport_counts.index, sport_counts.values)
            ax.set_xlabel('Sport')
            ax.set_ylabel('Count')
            ax.tick_params(axis='x', rotation=90)
            st.pyplot(fig)
    
        with col5:
            if 'Year' in filtered_df:
                min_year = int(filtered_df['Year'].min())
                max_year = int(filtered_df['Year'].max())
                decade_start = min_year - min_year % 10
            ##  if 1982, 1982 - (1982 % 10) = 1982 - 2 = 1980 ##

                decade_end = max_year + (10 - max_year % 10)
            ## if 2021, 2021 + (10 - 2021 % 10) = 2021 + (10 - 1) = 2021 + 9 = 2030 ##

                filtered_df['Decade'] = pd.cut(filtered_df['Year'], bins=range(decade_start, decade_end+1, 10), right=False)
            ## pd.cut bins the 'Year' values into intervals based on decades made 
            ## on bins variable -> right=False makes interval left-inclusive, for example
            ## [1980, 1990] will include 1980 but not 1990
            ## The +1 is necessary because the range function in Python does not
            ## include the endpoint

                st.title("Decade Distribution")
                decade_counts = filtered_df['Decade'].value_counts().sort_index()
                if not decade_counts.empty:
                    fig, ax = plt.subplots()
                    ax.bar(decade_counts.index.astype(str),decade_counts.values)
                ## bar chart where string is x and values is y ##

                    ax.set_xlabel('Decade')
                    ax.set_ylabel('Count')
                    ax.tick_params(axis='x', rotation=90)
                    st.pyplot(fig)
                else:
                    st.write("No years found in dataset")
            else:
                st.write("No Set Name column found in dataset")

        with col6:
            st.title("Quick Stats")
            cards_flipped = filtered_df['Purchase Price'].notnull().sum()
            avg_purchase_price = filtered_df['Purchase Price'].mean()
            eBay_count = (filtered_df['Purchased By'] == 'eBay').sum()
            cart_count = (filtered_df['Purchased By'] == '*Cart*').sum()
            comc_count = (~filtered_df['Purchased By'].isin(['eBay', '*Cart*'])).sum()

            st.write("Flipped Cards Sold", cards_flipped)
            st.write("Average Purchase Price of Flipped Cards", round(avg_purchase_price, 2))
            st.write("Count of Cards Sold on eBay", eBay_count)
            st.write("Count of Cards Sold by Cart", cart_count)
            st.write("Count of Cards Sold to COMC Users", comc_count)

        df['Description'] = df['Description'].astype(str)
        name_counts = df['Description'].value_counts()

        top_10_names = name_counts.head(10).reset_index()
        top_10_names.columns = ['Name', 'Count']

        top_10_names.index = top_10_names.index.rename("Rank") + 1

        st.title("Top 10 Selling Players")
        st.table(top_10_names)


        st.header("Stats Breakdown")
        st.write("The assumption on purchase price is 0.5 per card listed to the site for cards not flipped, 2 for any over 99")
    
        new_data_df = filtered_df

        new_data_df['COMC Credit'] = pd.to_numeric(new_data_df['COMC Credit'], errors='coerce')
        new_data_df['Purchase Price'] = pd.to_numeric(new_data_df['Purchase Price'], errors='coerce')

        new_data_df['Purchase Price'].fillna(new_data_df['Sales Price'].apply(lambda x: 2 if x>99 else 0.5), inplace=True)
        ## makes NA in 'Purchase Price' either 2 or 0.5 depending on x ##

        new_data_df['Profit'] = new_data_df['COMC Credit'] - new_data_df['Purchase Price']

        new_data_df['Markup'] = 100 * (new_data_df['Sales Price'] - new_data_df['Purchase Price'] / new_data_df['Purchase Price'])
        
        grouped_df = new_data_df.groupby('Sport').agg({'Profit': 'sum',
                                        'Markup': 'median',
                                        'Sales Price': ['sum', 'count']})
            
        grouped_df.columns = ['Profit_sum', 'Markup_median', 'Total_sales_amount', 'Sales_count']
        grouped_df = grouped_df[['Profit_sum', 'Total_sales_amount', 'Sales_count', 'Markup_median']]

        grouped_df = grouped_df.round(2)
        grouped_df_sorted = grouped_df.sort_values(by='Profit_sum', ascending=False)

        st.dataframe(grouped_df_sorted, width=1000, height=700)

        st.header("Advanced Profit by Quantile")
            # Group by 'Sport' and calculate deciles
        profit_deciles = new_data_df.groupby('Sport')['Profit'].quantile([0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]).unstack()
            # Reset index for better presentation
        profit_deciles.reset_index(inplace=True)
            # Rename columns for clarity
        profit_deciles.columns = ['Sport', '10%', '20%', '30%', '40%', '50%', '60%', '70%', '80%', '90%']
        profit_deciles = profit_deciles.round(2)
            # Display the deciles
        st.dataframe(profit_deciles, width=1000, height=700)


        st.header("Markup by Quantile")
            # Group by 'Sport' and calculate deciles
        markup_deciles = new_data_df.groupby('Sport')['Markup'].quantile([0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9]).unstack()
            # Reset index for better presentation
        markup_deciles.reset_index(inplace=True)
            # Rename columns for clarity
        markup_deciles.columns = ['Sport', '10%', '20%', '30%', '40%', '50%', '60%', '70%', '80%', '90%']
        markup_deciles = markup_deciles.round(2)
            # Display the deciles
        st.dataframe(markup_deciles, width=1000, height=700)

    if __name__ == "__main__":
      main()
