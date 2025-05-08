# Data Engineering Coding Test

This repository contains my solution to an entry-level data engineering coding test completed during COVID-19 in August 2020. The challenge demonstrates proficiency in Python and SQL - two essential languages for data engineering tasks. This was created as part of a technical assessment for a company recruiting process.

## Project Structure

```
├── Answers/                      # Directory containing solution files
│   ├── Python_Test.ipynb         # Jupyter notebook with Python solution
│   ├── SQL_Answers.txt           # SQL solution queries
│   └── schema_code.sql           # SQL schema setup script
└── Entry level data engineer coding assignment.docx  # Assignment document
```

## Technical Challenge Overview

The assessment consisted of two main parts:

### 1. SQL Challenge

The SQL portion focused on querying campaign, recipient, and delivery log data. Key tasks included:

- Calculating open rates for email campaigns
- Computing average open/click rates per month
- Finding specific recipients and their interaction patterns
- Analyzing delivery timing and weekday performance metrics
- Calculating time differences between campaign starts and email deliveries

### 2. Python Data Analysis

The Python challenge involved analyzing NBA data using pandas to:

- Clean and transform a dataset from FiveThirtyEight
- Create new calculated columns including timestamps and boolean flags
- Split and filter data based on specific conditions
- Perform aggregations to identify trends by year
- Create a pivoted statistics table showing team performance by year

## Technologies Used

- **Python**: Data processing and transformation with pandas
- **SQL**: Data querying and analysis
- **Jupyter Notebook**: Interactive code development and visualization

## Solutions

### SQL Solutions

Key SQL implementations include:

1. Open rate calculation by campaign:
   ```sql
   SELECT campaign_id, count(open_flag) as open_rate 
   FROM delivery_logs 
   WHERE open_flag = 1 
   GROUP BY campaign_id;
   ```

2. Monthly averages for open and click rates:
   ```sql
   SELECT AVG(opens), AVG(clicks), EXTRACT(MONTH FROM campaign_start_date) as Month 
   FROM campaign 
   GROUP BY month 
   ORDER BY month;
   ```

3. Date difference calculation:
   ```sql
   SELECT ROUND(AVG(DATEDIFF(delivery_logs.date_received, campaign.campaign_start_date))) as avg_datediff 
   FROM campaign 
   INNER JOIN delivery_logs ON campaign.campaign_id = delivery_logs.campaign_id;
   ```

### Python Implementation

The Python solution includes:

1. Data loading and inspection:
   ```python
   import pandas as pd
   data = pd.read_csv('https://raw.githubusercontent.com/fivethirtyeight/data/master/nba-elo/nbaallelo.csv')
   df = data.copy()
   ```

2. Creating timestamp columns:
   ```python
   df['date_game'] = df['date_game'].apply(lambda x: pd.Timestamp(x).strftime('%d-%m-%Y'))
   df['date_game_ts'] = pd.to_datetime(df['date_game'], format='%d-%m-%Y')
   ```

3. Adding a 'great game' flag based on scoring:
   ```python
   df['great_game_flag'] = np.nan
   for i in range(len(df)):
       if int(df.loc[i, "pts"]) > 100 or int(df.loc[i, "opp_pts"]) > 100:
           df.at[i, "great_game_flag"] = 1
       else:
           df.at[i, "great_game_flag"] = 0
   ```

4. League-based data splitting:
   ```python
   aba = df[df["lg_id"]=="ABA"].sort_values(by=["date_game_ts"], ascending=True)
   nba = df[df["lg_id"]=="NBA"].sort_values(by=["date_game_ts"], ascending=True)
   ```

5. ABA statistics aggregation:
   ```python
   stats_aba = aba[aba['great_game_flag'] == 1.0].groupby('year_id')['great_game_flag'].count().reset_index(name="great_game_count")
   ```

6. NBA team performance pivot:
   ```python
   nba[nba['game_result'] == 'W'].pivot_table(index="fran_id", columns="year_id", aggfunc='count').fillna(0)
   ```

## Running the Code

### SQL Queries
1. Visit [SQL Fiddle](http://sqlfiddle.com/)
2. Select MySQL 5.6
3. Copy and paste the schema code from `Answers/schema_code.sql`
4. Click "Build Schema"
5. Run the queries from `Answers/SQL_Answers.txt`

### Python Analysis
1. Install required packages:
   ```
   pip install pandas numpy jupyter
   ```
2. Run the Jupyter notebook:
   ```
   jupyter notebook Answers/Python_Test.ipynb
   ```

## Conclusion

This coding test demonstrates key data engineering capabilities, including:
- Data cleaning and transformation
- SQL query optimization
- Efficient data manipulation with pandas
- Statistical analysis and aggregation
- Working with timestamps and date calculations

These skills represent the foundation of effective data engineering, enabling the extraction of meaningful insights from complex datasets.
