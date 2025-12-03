# Databricks Data Engineering Fundamentals

**A Hands-on Guide to Modern Data Engineering with Databricks**

This repository provides a complete, beginner-friendly introduction to **Data Engineering on Databricks**, implementing industry-standard patterns that work on both **Databricks Community Edition** (free) and paid workspaces.

## 🚀 Overview

Learn the Medallion Architecture through hands-on practice:

* 🏗️ **Medallion Architecture**: Implement Bronze → Silver → Gold data layers
* 💎 **Delta Lake**: ACID transactions, time travel, schema evolution
* 📊 **Data Quality**: Validation, deduplication, and data cleansing
* 🔄 **ETL Pipelines**: PySpark patterns for loading and transforming data
* 📈 **Business Analytics**: Aggregations, metrics, and reporting tables
* ⚡ **Best Practices**: Production-ready patterns for real-world use


## 🛠️ Getting Started

### Prerequisites

- **Databricks Account**: Sign up for free at [community.cloud.databricks.com](https://community.cloud.databricks.com) or use your company workspace
- **No prior experience needed**: Basic SQL knowledge is helpful but not required
- **Runtime**: DBR 13.3 LTS or higher

---

### ⚡ Quick Start - Choose Your Method

#### **Option A: Git Repos (Recommended)** ⭐

**Zero manual uploads needed!**

1. **Import via Git**:
   - In Databricks: `Workspace → Repos → Add Repo`
   - Paste your repo URL
   - Click "Create"

2. **Run the notebook**:
   - Navigate to `src/main_pipeline.ipynb`
   - Start your cluster
   - Customize widgets (catalog, bronze_db, silver_db, gold_db) if needed
   - Run cells sequentially
   - The notebook auto-detects Git Repos, copies CSVs to Volume, and loads into Delta! 🎉

**No manual CSV upload required!**

---

#### **Option B: Manual Upload**

1. **Download** this repository

2. **Upload CSV files**:
   - In Databricks: `Data → Create Table → Upload File`
   - Upload all 4 CSV files from `data/` folder
   - Default path: `/FileStore/tables/`

3. **Upload notebook**:
   - `Workspace → Import → src/main_pipeline.ipynb`

4. **Run it**:
   - Start cluster and run all cells
   - Watch Bronze → Silver → Gold layers build!

---

## 📂 Medallion Architecture

The **Medallion Architecture** organizes data into three progressive layers:

* **🥉 Bronze Layer (Raw Data)**: CSV files → Delta tables with PySpark
* **🥈 Silver Layer (Cleaned Data)**: Validated, standardized, with derived columns
* **🥇 Gold Layer (Business Data)**: Pre-calculated metrics and analytics

### Benefits:
- **Data Lineage**: Track data from source to insights
- **Scalability**: Handle growing data volumes efficiently
- **Data Quality**: Progressive refinement at each layer
- **Flexibility**: Easy to add new sources or analytics

---

## 🏗️ What You'll Build

### E-Commerce Analytics Platform

**Data Pipeline:**
- 👥 Customers → Bronze → Silver → Customer Analytics
- 📦 Products → Bronze → Silver → Product Performance  
- 🛒 Orders → Bronze → Silver → Revenue Trends
- 📋 Order Items → Bronze → Silver → Category Analytics

**Gold Layer Tables:**
- `{catalog}.{gold_db}.customer_analytics` - Lifetime value, segmentation
- `{catalog}.{gold_db}.product_performance` - Sales metrics, rankings
- `{catalog}.{gold_db}.monthly_revenue` - Time-series analysis
- `{catalog}.{gold_db}.category_performance` - Category-level KPIs

---

## 🎯 Key Concepts Covered

### Delta Lake Features
- ✅ ACID transactions for data reliability
- ✅ Time travel (query historical versions)
- ✅ Schema evolution (add/modify columns safely)
- ✅ Upserts and merges (handle updates efficiently)
- ✅ Automatic optimization and compaction

### Data Engineering Patterns
- ✅ PySpark CSV loading into Delta tables
- ✅ Data validation and quality checks
- ✅ SQL transformations for cleansing
- ✅ Incremental data processing
- ✅ Business metrics calculation

### Performance Optimization
- ✅ Partitioning strategies
- ✅ Z-ordering for faster queries
- ✅ Caching for iterative development
- ✅ Broadcast joins for small tables

---

## 💡 Example Queries

Once you've run the pipeline, try these queries:

```sql
%sql
-- Top 10 customers by total spend
SELECT * FROM {catalog}.{gold_db}.customer_analytics
ORDER BY lifetime_value DESC
LIMIT 10;

%sql
-- Monthly revenue trend
SELECT 
  TO_DATE(month_start_date) as month,
  ROUND(net_revenue, 2) as revenue
FROM {catalog}.{gold_db}.monthly_revenue
ORDER BY month DESC;

%sql
-- Product performance
SELECT * FROM {catalog}.{gold_db}.product_performance
WHERE total_quantity_sold > 5
ORDER BY total_revenue DESC;
```

---

## 🎓 Learning Path

### For Complete Beginners

1. Start with `src/main_pipeline.ipynb`
2. Focus on understanding the Bronze → Silver → Gold progression
3. Run each cell and read the explanations
4. Try modifying simple SQL queries (all SQL cells use `%sql` magic command)

### For SQL Developers

1. Review the SQL-based transformations in the main pipeline (look for `%sql` cells)
2. Compare traditional database patterns to Delta Lake
3. Explore the advanced joins and aggregations in the Gold layer
4. Study `src/best_practices.ipynb` for production patterns

### For Python/PySpark Developers

1. Review the PySpark CSV loading pattern and Volume integration in Bronze layer
2. Study DataFrame transformations in Silver layer
3. Experiment with custom aggregations in Gold layer
4. Explore advanced patterns in `src/best_practices.ipynb`

---

## 🔐 Unity Catalog (3-Level Namespacing) + Volumes

This repository uses **Unity Catalog** with 3-level namespacing and **Volumes** for data storage:

```
catalog.schema.table
   ↓      ↓      ↓
  demo.bronze.customers
```

### Configuration Widgets:
- **catalog**: Unity Catalog name (default: "demo")
- **bronze_db**: Bronze schema name (default: "bronze")
- **silver_db**: Silver schema name (default: "silver")
- **gold_db**: Gold schema name (default: "gold")

### Volume Structure:
```
/Volumes/{catalog}/{bronze_db}/ecommerce/
├── files/
│   ├── customers/customers.csv
│   ├── products/products.csv
│   ├── orders/orders.csv
│   └── order_items/order_items.csv
└── downloads/
```

### Examples:
- `{catalog}.{bronze_db}.customers` - Raw customer data
- `{catalog}.{silver_db}.orders` - Cleaned order data
- `{catalog}.{gold_db}.customer_analytics` - Business metrics

**Works on Community Edition and all paid workspaces!** ✅

---

## 🌟 Advanced Features

### Time Travel
```sql
%sql
-- View table history
DESCRIBE HISTORY {catalog}.{silver_db}.customers LIMIT 5;

%sql
-- Query previous version
SELECT * FROM {catalog}.{silver_db}.customers VERSION AS OF 1;
```

### Schema Evolution
```python
# Add new columns safely
df = df.withColumn("new_column", lit(None))
df.write.mode("append").option("mergeSchema", "true").saveAsTable(f"{catalog}.{silver_db}.orders")
```

### Upserts (Merge Operations)
```sql
%sql
MERGE INTO {catalog}.{silver_db}.customers target
USING {catalog}.{bronze_db}.customers_new source
ON target.customer_id = source.customer_id
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *;
```

---

## 📖 Additional Resources

- [Databricks Documentation](https://docs.databricks.com/)
- [Delta Lake Documentation](https://docs.delta.io/)
- [Medallion Architecture Guide](https://www.databricks.com/glossary/medallion-architecture)
- [PySpark Documentation](https://spark.apache.org/docs/latest/api/python/)

---

## 🎓 Next Steps

After completing this workshop:

1. **Explore Real Data**: Connect to your own data sources (CSV, JSON, Parquet, databases)
2. **Learn Streaming**: Implement real-time pipelines with Structured Streaming
3. **Add ML**: Build machine learning models on your clean data
4. **Optimize**: Study advanced performance tuning and cost optimization
5. **Production**: Learn about CI/CD, testing, and monitoring for data pipelines
6. **Check Best Practices**: Open `best_practices.ipynb` for production patterns

---

## 🤝 Contributing

This is an educational project. Feel free to:
- Submit issues for bugs or unclear explanations
- Suggest improvements or additional examples
- Share your own variations and use cases

---

## 📝 License

MIT License - feel free to use this for learning, teaching, or as a starting point for your own projects.

---

**Happy Data Engineering! 🚀**

_Last updated: December 2025_  
_Compatible with: Databricks Community Edition & Paid Workspaces, DBR 13.3 LTS or higher_
