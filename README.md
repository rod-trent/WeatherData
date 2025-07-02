# Instructions for Using the WeatherData Dataset

This guide provides step-by-step instructions for setting up and using the `WeatherData` dataset in Azure Data Explorer to follow along with the ["KQL Art Gallery: Visualizing Data with Creative Queries" blog post](https://rodtrent.substack.com/p/kql-art-gallery-visualizing-data). The dataset contains synthetic weather observations for cities (Seattle, Miami, Denver) and is designed for creating visualizations with Kusto Query Language (KQL).

## Prerequisites
- Access to an **Azure Data Explorer** cluster. If you don’t have one, you can use the [free cluster](https://dataexplorer.azure.com/freecluster) or the `help` cluster for testing.
- Basic familiarity with KQL and the Azure Data Explorer web UI.
- The `WeatherData_Setup.kql` file containing the table creation and data ingestion commands (provided separately).

## Step 1: Set Up the Database
1. **Log in to Azure Data Explorer**:
   - Navigate to [https://dataexplorer.azure.com/](https://dataexplorer.azure.com/) and sign in.
   - Select your cluster or use the `help` cluster.

2. **Create a Database** (if needed):
   - In the query editor, run the following command to create a database named `WeatherDB`:
     ```kql
     .create database WeatherDB
     ```
   - If using the `help` cluster, ensure you have permissions or skip this step and use an existing database.

3. **Select the Database**:
   - In the left pane, select `WeatherDB` (or your chosen database) to set it as the active database for queries.

## Step 2: Create and Populate the WeatherData Table
1. **Obtain the Dataset**:
   - Use the `WeatherData_Setup.kql` file, which contains the KQL commands to create the `WeatherData` table and ingest sample data.

2. **Run the Table Creation Command**:
   - Copy the following command from the `WeatherData_Setup.kql` file:
     ```kql
     .create table WeatherData (
         Timestamp: datetime,
         City: string,
         Temperature: real,
         Humidity: real
     )
     ```
   - Paste it into the Azure Data Explorer query editor and execute it by clicking **Run** or pressing **F5**.

3. **Ingest the Sample Data**:
   - Copy the ingestion command from the `WeatherData_Setup.kql` file, starting with:
     ```kql
     .ingest inline into table WeatherData <|
     ```
     followed by the data rows (e.g., `2025-06-02T00:00:00,Seattle,55.2,80`).
   - Paste the entire ingestion command into the query editor and execute it.
   - Note: The provided sample includes one day of data for brevity. To fully replicate the [blog post’s](https://rodtrent.substack.com/p/kql-art-gallery-visualizing-data) 30-day dataset, extend the data by adding rows for additional days (up to `2025-07-01`) with realistic temperature and humidity variations, as suggested in the file’s comments.

4. **Verify the Data**:
   - Run a simple query to confirm the data was ingested correctly:
     ```kql
     WeatherData
     | take 10
     ```
   - This should display the first 10 rows, showing columns for `Timestamp`, `City`, `Temperature`, and `Humidity`.

## Step 3: Use the Dataset with KQL Queries
The `WeatherData` dataset is now ready for the visualizations described in the [blog post](https://rodtrent.substack.com/p/kql-art-gallery-visualizing-data). Follow these steps to use it with the provided KQL queries:

1. **Bar Chart of Average Temperatures**:
   - Use the query:
     ```kql
     WeatherData
     | where Timestamp > ago(30d)
     | summarize AvgTemp = avg(Temperature) by City
     | render barchart
     ```
   - Run the query and view the bar chart in the results pane. Customize the title (e.g., “City Climate Showcase”) in the visualization options.

2. **Time Series of Temperature Trends**:
   - Use the query:
     ```kql
     WeatherData
     | where Timestamp > ago(7d) and City == "Seattle"
     | summarize AvgTemp = avg(Temperature) by bin(Timestamp, 1h)
     | render timechart
     ```
   - Execute and adjust the chart’s appearance (e.g., add annotations for key trends).

3. **Heatmap of Temperature Patterns**:
   - Use the query:
     ```kql
     WeatherData
     | where Timestamp > ago(7d) and City == "Miami"
     | summarize AvgTemp = avg(Temperature) by bin(Timestamp, 1h)
     | project HourOfDay = hourofday(Timestamp), DayOfWeek = dayofweek(Timestamp), AvgTemp
     | summarize Heat = avg(AvgTemp) by HourOfDay, DayOfWeek
     | render heatmap
     ```
   - Run and customize the color gradient to highlight temperature patterns.

## Step 4: Explore and Experiment
- **Extend the Dataset**: Add more cities or data points by appending new rows to the ingestion command. For example, include Chicago or vary temperatures to simulate seasonal changes.
- **Try New Visualizations**: Experiment with other `render` options like `columnchart`, `areachart`, or `scatterchart`.
- **Share Your Work**: Save your visualizations and share them with the community using `#KQLArtGallery` on social media.

## Troubleshooting
- **No Data Returned**: Ensure the ingestion command ran successfully and the `Timestamp` values are within the query’s time range (e.g., `ago(30d)`).
- **Visualization Issues**: Check that the `render` operator is correctly specified and that the query produces the expected columns.
- **Permissions Errors**: If using a shared cluster, verify you have write permissions for the database or use a personal cluster.

## Notes
- The sample data starts on `2025-06-02` and should be extended to `2025-07-01` for full compatibility with the [blog post’s](https://rodtrent.substack.com/p/kql-art-gallery-visualizing-data) queries.
- If you lack access to Azure Data Explorer, you can test similar queries on the `help` cluster’s `StormEvents` table, adapting the schema accordingly.
- For large datasets, consider batching the ingestion command to avoid timeouts.

With these instructions, you’re ready to set up the `WeatherData` dataset and create stunning data visualizations with KQL. Happy querying and storytelling!
