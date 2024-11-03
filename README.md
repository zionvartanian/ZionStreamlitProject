# ZionStreamlitProject
The provided code is a Streamlit application designed to analyze and visualize sales data from personal CSV files sourced from COMC (a platform for buying and selling sports cards) under the user's Sales Summary. The application allows users to upload multiple CSV files, consolidates the data, and offers various interactive options for filtering and analyzing the sales data based on specified criteria.

Key Features Include:

Data Upload and Consolidation: Users can upload multiple CSV files containing sales data, which the application reads and combines into a single DataFrame for analysis

Date Range Filtering: The application enables users to filter the sales data based on specific date ranges for both the date sold and the acquisition date. Options include custom ranges as well as predefined ranges like the last 7, 30, 90, or 180 days.

Sales Price Filtering: Users can select a range of sales prices to focus on specific segments of the market, which helps to hone in on particular cards or price points of interest.

Category Selection: The application allows users to filter data based on various sports categories (e.g., baseball, basketball, football), enabling a tailored analysis based on user interest.

Data Visualization: The code generates visual representations of the filtered data, including bar graphs showing sport breakdowns and decade distributions, as well as tables that summarize key statistics and top-selling players.

Profit and Markup Analysis: The application calculates important financial metrics such as profit and markup based on the sales and purchase prices. Users can view these metrics summarized by sport and in quantile distributions.

Interactive Interface: Streamlit provides an interactive user interface, allowing users to easily explore the dataset and visualize findings without needing extensive programming knowledge.

Impact:

This project is valuable for sports card collectors, investors, and sellers on COMC looking to understand market trends, track sales performance, and make informed decisions based on sales data. By transforming raw CSV data into visually engaging and informative analyses, the application helps users grasp complex information quickly and effectively.
