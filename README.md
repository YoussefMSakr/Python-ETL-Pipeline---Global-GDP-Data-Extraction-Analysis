# Python ETL Pipeline - Global GDP Data Extraction & Analysis

## 🎯 Overview

An end-to-end ETL pipeline that extracts GDP data for 180+ countries from public web sources using Python and BeautifulSoup. The pipeline transforms raw HTML data into clean, structured datasets suitable for economic analysis and comparative research.

## ✨ Key Features

- 🌍 **Comprehensive Coverage**: GDP data for 180+ countries
- 🔍 **Web Scraping**: Automated HTML parsing with BeautifulSoup
- 🔄 **Data Transformation**: USD Millions → Billions conversion
- 💾 **Dual Output**: CSV files + SQLite database
- 📊 **Analytics-Ready**: Standardized format for analysis
- 📝 **Process Logging**: Complete execution tracking

## 🏗️ Pipeline Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
│   Web Source │ --> │   Extract    │ --> │  Transform   │ --> │     Load     │
│   (HTML)     │     │ (BeautifulSoup)│   │   (Pandas)   │     │ (CSV/SQLite) │
└──────────────┘     └──────────────┘     └──────────────┘     └──────────────┘
                            │                     │                     │
                     Parse HTML Tables    Convert USD M->B      Store Data
                     Extract Countries    Standardize Format   Enable Queries
```

## 🛠️ Tech Stack

- **Language:** Python 3.9+
- **Web Scraping:** BeautifulSoup4, Requests
- **Data Processing:** Pandas, NumPy
- **Database:** SQLite3
- **Logging:** Python logging module

## 📂 Project Structure

```
gdp-data-etl/
│
├── src/
│   ├── extract.py              # Web scraping logic
│   ├── transform.py            # Data transformation
│   ├── load.py                 # Data loading (CSV + SQLite)
│   └── main.py                 # Main ETL orchestrator
│
├── data/
│   ├── raw/                    # Raw extracted data
│   ├── processed/              # Transformed data
│   └── output/
│       ├── gdp_data.csv        # Final CSV output
│       └── gdp_database.db     # SQLite database
│
├── notebooks/
│   └── analysis.ipynb          # Data exploration notebook
│
├── tests/
│   └── test_etl.py             # Unit tests
│
├── logs/
│   └── etl_execution.log       # Execution logs
│
├── requirements.txt
├── README.md
└── .gitignore
```

## 🚀 Getting Started

### Prerequisites

```bash
Python 3.9 or higher
pip package manager
Internet connection (for web scraping)
```

### Installation

1. **Clone the repository**
```bash
git clone https://github.com/yourusername/gdp-data-etl.git
cd gdp-data-etl
```

2. **Create virtual environment (recommended)**
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

### Quick Start

```bash
# Run the complete ETL pipeline
python src/main.py

# Or run individual stages
python src/extract.py    # Extract only
python src/transform.py  # Transform only
python src/load.py       # Load only
```

## 📊 Data Schema

### Output Format

| Column Name | Data Type | Description | Example |
|-------------|-----------|-------------|---------|
| country_name | string | Country name | United States |
| country_code | string | ISO country code | USA |
| gdp_usd_billions | float | GDP in billions | 23,315.08 |
| year | integer | Data year | 2024 |
| region | string | Geographic region | North America |
| rank | integer | Global GDP ranking | 1 |

## 🔄 ETL Process Details

### 1. Extract Phase 🔍

```python
# Web scraping with BeautifulSoup
def extract_gdp_data(url):
    """
    Extract GDP data from HTML tables
    - Parse HTML structure
    - Identify data tables
    - Extract country names and values
    """
    response = requests.get(url)
    soup = BeautifulSoup(response.content, 'html.parser')
    # ... extraction logic
```

**Features:**
- Error handling for network issues
- HTML table parsing
- Data validation during extraction

### 2. Transform Phase 🔄

```python
# Data transformation with Pandas
def transform_gdp_data(raw_data):
    """
    Transform raw data into analytics-ready format
    - Convert USD Millions → Billions
    - Standardize country names
    - Handle missing values
    - Round to 2 decimal places
    """
    df['gdp_usd_billions'] = df['gdp_usd_millions'] / 1000
    df['gdp_usd_billions'] = df['gdp_usd_billions'].round(2)
    # ... transformation logic
```

**Transformations:**
- ✅ Unit conversion (Millions → Billions)
- ✅ Standardized rounding (2 decimal places)
- ✅ Column renaming for clarity
- ✅ Data type casting
- ✅ NULL handling

### 3. Load Phase 💾

```python
# Dual output: CSV + SQLite
def load_data(df):
    """
    Load data to multiple destinations
    - Save as CSV for portability
    - Store in SQLite for queries
    """
    # CSV export
    df.to_csv('data/output/gdp_data.csv', index=False)
    
    # SQLite database
    conn = sqlite3.connect('data/output/gdp_database.db')
    df.to_sql('gdp_data', conn, if_exists='replace', index=False)
```

## 📈 Usage Examples

### Query Data from SQLite

```python
import sqlite3
import pandas as pd

# Connect to database
conn = sqlite3.connect('data/output/gdp_database.db')

# Query top 10 economies
query = """
SELECT country_name, gdp_usd_billions, rank
FROM gdp_data
ORDER BY gdp_usd_billions DESC
LIMIT 10
"""

df = pd.read_sql_query(query, conn)
print(df)
```

### Filter by GDP Threshold

```python
# Countries with GDP > $1 Trillion
high_gdp = df[df['gdp_usd_billions'] > 1000]
print(f"Countries with GDP > $1T: {len(high_gdp)}")
```

### Regional Analysis

```python
# Average GDP by region
regional_avg = df.groupby('region')['gdp_usd_billions'].mean()
print(regional_avg.sort_values(ascending=False))
```

## 📊 Sample Output

```
Top 10 Economies by GDP (2024)

Rank  Country          GDP (Billions USD)
────────────────────────────────────────
1     United States    23,315.08
2     China            17,734.06
3     Japan             4,940.88
4     Germany           4,259.93
5     India             3,732.22
6     United Kingdom    3,158.94
7     France            2,938.27
8     Italy             2,099.88
9     Canada            2,016.85
10    Brazil            1,924.49
```

## 🧪 Testing

```bash
# Run all tests
pytest tests/

# Run specific test file
pytest tests/test_etl.py

# Run with coverage
pytest --cov=src tests/
```

## 📝 Logging

All ETL operations are logged to `logs/etl_execution.log`:

```
2024-01-15 10:30:15 - INFO - Starting ETL pipeline
2024-01-15 10:30:16 - INFO - Extracting data from source
2024-01-15 10:30:18 - INFO - Successfully extracted 190 countries
2024-01-15 10:30:19 - INFO - Transforming data...
2024-01-15 10:30:20 - INFO - Transformation complete
2024-01-15 10:30:21 - INFO - Loading to CSV and SQLite
2024-01-15 10:30:22 - INFO - ETL pipeline completed successfully
```

## 🎓 Use Cases

This dataset enables various economic analyses:

1. **GDP Rankings**: Identify top economies globally
2. **Threshold Analysis**: Countries above/below GDP thresholds
3. **Regional Comparison**: Compare economic performance by region
4. **Trend Analysis**: Track economic growth over time
5. **Research**: Support economic research and policy analysis

## 🔍 Data Quality Metrics

- ✅ **Completeness**: 100% of targeted countries
- ✅ **Accuracy**: Cross-validated with multiple sources
- ✅ **Consistency**: Standardized formatting
- ✅ **Timeliness**: Updated from latest available data





### Common Issues

**Issue**: Web scraping fails
```bash
# Solution: Check internet connection and source URL
# Verify website structure hasn't changed
```

**Issue**: Data conversion errors
```bash
# Solution: Check for non-numeric values in source data
# Review transformation logic in transform.py
```

## 📚 Lessons Learned

- ✅ BeautifulSoup is excellent for parsing HTML tables
- ✅ Data validation during extraction prevents downstream issues
- ✅ Dual output (CSV + DB) provides flexibility
- ✅ Comprehensive logging is essential for debugging
- ✅ Unit conversion requires careful handling of precision



## 👤 Author

**Youssef Mohamed Sakr**
- Email: yousssseefssakr@gmail.com
- GitHub: https://github.com/YoussefMSakr
- LinkedIn: https://www.linkedin.com/in/youssef-mohamed-36bba4282

## 🙏 Acknowledgments

- Data source: Wikipedia
- Python community for excellent libraries
- IBM Data Engineering course

---

⭐ Star this repo if you found it useful!

📊 **Ready to analyze global economic data!**
