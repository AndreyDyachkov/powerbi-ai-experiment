# Power BI Churn Dashboard Project

## Project Overview
This project is a Power BI dashboard designed to analyze and visualize customer churn data using AI assistance. The objective is to provide insights into the factors contributing to customer churn and to help businesses take proactive measures to retain their customers.

## Directory Structure
```
/powerbi-ai-experiment
├── /data
│   ├── churn_data.csv
│   └── data_model.pbix
├── /reports
│   └── churn_dashboard.pbix
├── /scripts
│   └── data_processing.py
└── README.md
```

## Features
- Interactive visualizations of churn data
- AI-driven insights and predictions
- User-friendly interface for data exploration
- Drill-down functionality to explore individual customer behaviors

## Data Model
The data model consists of multiple tables capturing customer information, transaction history, and churn indicators. Relationships between tables are defined to facilitate comprehensive analysis. Major tables include:
- Customer Information
- Transaction History
- Churn Indicators

## Pages
The dashboard includes several key pages:
1. **Overview**: A high-level summary of customer churn metrics.
2. **Analysis**: Detailed insights into factors contributing to churn.
3. **Predictions**: AI-generated forecasts of future churn rates.
4. **Recommendations**: Suggested actions based on the analysis.

## Setup Instructions
1. Clone the repository:
   ```
   git clone https://github.com/AndreyDyachkov/powerbi-ai-experiment.git
   ```
2. Navigate to the project directory:
   ```
   cd powerbi-ai-experiment
   ```
3. Open the `data_model.pbix` file in Power BI Desktop.
4. Refresh the data model to load the latest data.
5. Publish the report to Power BI Service to share with stakeholders.

## References
- [Power BI Documentation](https://docs.microsoft.com/en-us/power-bi)
- [AI in Power BI](https://docs.microsoft.com/en-us/power-bi/ai)
- [Customer Churn Analysis Techniques](https://example.com/churn-analysis)

For further information, please refer to the supporting documentation linked above.