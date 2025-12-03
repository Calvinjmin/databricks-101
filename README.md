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
   - Navigate to `Main_Pipeline.ipynb`
   - Start your cluster
   - Run cells sequentially
   - The notebook auto-detects Git Repos and loads CSVs directly! 🎉

**No manual CSV upload required!**

---

#### **Option B: Manual Upload**

1. **Download** this repository

2. **Upload CSV files**:
   - In Databricks: `Data → Create Table → Upload File`
   - Upload all 4 CSV files from `data/` folder
   - Default path: `/FileStore/tables/`

3. **Upload notebook**:
   - `Workspace → Import → Main_Pipeline.ipynb`

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
- `gold.customer_analytics` - Lifetime value, segmentation
- `gold.product_performance` - Sales metrics, rankings
- `gold.monthly_revenue` - Time-series analysis
- `gold.category_performance` - Category-level KPIs

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
-- Top 10 customers by total spend
SELECT * FROM gold.customer_analytics
ORDER BY lifetime_value DESC
LIMIT 10;

-- Monthly revenue trend
SELECT 
  TO_DATE(month_start_date) as month,
  ROUND(net_revenue, 2) as revenue
FROM gold.monthly_revenue
ORDER BY month DESC;

-- Product performance
SELECT * FROM gold.product_performance
WHERE total_quantity_sold > 5
ORDER BY total_revenue DESC;
```

---

## 🎓 Learning Path

### For Complete Beginners

1. Start with `Main_Pipeline.ipynb`
2. Focus on understanding the Bronze → Silver → Gold progression
3. Run each cell and read the explanations
4. Try modifying simple SQL queries

### For SQL Developers

1. Review the SQL-based transformations in the main pipeline
2. Compare traditional database patterns to Delta Lake
3. Explore the advanced joins and aggregations in the Gold layer
4. Study `Best_Practices.ipynb` for production patterns

### For Python/PySpark Developers

1. Review the PySpark CSV loading pattern in the Bronze layer
2. Study DataFrame transformations in Silver layer
3. Experiment with custom aggregations in Gold layer
4. Explore advanced patterns in `Best_Practices.ipynb`

---

## 🔐 Unity Catalog Support

### Community Edition (Free):
- Uses **Hive metastore** by default ✅
- All code works out-of-the-box
- Tables like: `bronze.customers`, `silver.orders`, `gold.customer_analytics`

### Paid Workspaces with Unity Catalog:
The notebooks include comments showing how to:
- Create schemas: `CREATE SCHEMA IF NOT EXISTS catalog.bronze`
- Use three-level namespacing: `catalog.bronze.customers`
- Store CSVs in Volumes: `/Volumes/catalog/schema/volume`

**The patterns you learn here work on both!**

---

## 🌟 Advanced Features

### Time Travel
```sql
-- View table history
DESCRIBE HISTORY silver.customers LIMIT 5;

-- Query previous version
SELECT * FROM silver.customers VERSION AS OF 1;
```

### Schema Evolution
```python
# Add new columns safely
df = df.withColumn("new_column", lit(None))
df.write.mode("append").option("mergeSchema", "true").saveAsTable("silver.orders")
```

### Upserts (Merge Operations)
```sql
MERGE INTO silver.customers target
USING bronze.customers_new source
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
6. **Check Best Practices**: Open `Best_Practices.ipynb` for production patterns

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
