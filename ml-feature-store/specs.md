# ML Feature Store Template: Iceberg + Snowpark Connect

## Template Overview

**In-product Title:** Build an ML Feature Store with Snowpark Connect for Apache Spark and Iceberg

**Focus:** 
This template brings together three pillars to solve the real-world challenge of ML feature store management:
1. **Iceberg's advanced table capabilities** (versioning, schema evolution, time travel) for production-grade data agility and auditability,
2. **PySpark API running directly on Snowflake** (via Snowpark Connect) for a frictionless, performant analytics experience using familiar DataFrame code, and
3. **ML feature store best practices**—showcasing how to build, evolve, and reproduce point-in-time ML features safely and scalably.

**Target Audience:** Data engineers, ML platform engineers, and analytics engineers building production-grade feature stores for machine learning

**Learning Objective:** Learn how Iceberg tables and Snowpark Connect enable reproducible, schema-flexible feature stores for machine learning.

**Target Time:** 5-10 minutes

**Dataset:** Tasty Bytes (customer and order data for RFM analysis)

---

## What We're Showcasing

### Core Technical Capabilities

1. **Snowpark Connect + PySpark API**
   - PySpark DataFrame operations for feature engineering
   - Query pushdown efficiency on Snowflake infrastructure
   - Window functions and aggregations
   - Familiar Spark syntax without cluster management

2. **Iceberg Managed Tables**
   - Create managed Iceberg table via Snowpark Connect
   - Partitioned writes (by `feature_date`)
   - Direct DataFrame writes with automatic versioning

3. **Schema Evolution + Version Control** ⭐ (Integrated demo)
   - **The Challenge:** Feature engineering is an iterative prcess where you discover new signals post-deployment. But production models depend on stable feature schemas, and traditional approaches force a choice: either block feature innovation or risk breaking production pipelines.
   
   - **The Solution:** Iceberg's schema evolution + snapshot-based time travel working together provide **backward-compatible versioning with zero-copy schema changes**.
   
   - **What we show:**
     - Create v1 with baseline RFM features (3 columns) → capture snapshot ID
     - Evolve schema by adding `customer_ltv_score` (4 columns) via `mergeSchema=true` → capture snapshot ID
     - Use time travel to query both versions simultaneously by snapshot ID
   
   - **Key demonstration:** 
     - Schema evolution creates v2 without rewriting v1 data (zero-copy, no downtime)
     - Both schemas coexist in the same table with full ACID guarantees
     - Time travel provides snapshot isolation: v1 readers see 3 columns, v2 readers see 4 columns
     - Complete backward compatibility: existing pipelines continue unchanged
   
   - **Technical value:** 
     - **Training-serving consistency**: Retrieve exact feature schemas used during model training, eliminating training-serving skew
     - **Feature store as versioned API**: Different model versions consume different feature versions from same table, like API versioning for data
     - **Zero-downtime feature rollout**: Deploy new features to production without migrating existing data or pausing pipelines
     - **Audit and compliance**: Complete lineage of what features existed at any point in time, critical for regulated industries
     - **Drift detection and debugging**: Compare feature distributions between snapshots to detect data drift or diagnose model degradation
     - **Experimentation without isolation overhead**: Run A/B tests with different feature sets (v1 vs v2) without duplicating tables or data
     - **Cost-efficient storage**: Iceberg's metadata-only schema changes and columnar format mean no data rewrite costs
     - **Point-in-time correctness**: Critical for temporal ML problems—features as they existed when event occurred, not as they exist today

### Business Context

Machine learning requires reproducible, versioned datasets that can evolve safely. This template shows how Iceberg's schema evolution and time travel work together to enable production-grade feature stores, ensuring your models train on consistent, auditable data while allowing safe feature experimentation.

**Key Value Propositions:**
- Schema evolution + versioning = Safe agility (add features without breaking production)
- Time travel = Reproducibility (recreate exact training datasets from any point in time)
- PySpark API = Familiarity (use DataFrame API you already know)
- Iceberg = Portability (query from any engine supporting open formats)

---

## Template Structure (5-10 Minute Flow)

### 1. Introduction & Setup (30 seconds)
**Markdown Cell:**
- Title and overview
- Use case: "ML models need stable, versioned features that can evolve safely"
- What you'll learn: how schema evolution + version control work together in feature stores

**Code Cell:**
- Environment setup (USE ROLE, WAREHOUSE, DATABASE, SCHEMA)
- Initialize Snowpark Connect Spark session
- Verify connection

### 2. Create Version 1: Initial Features (1 minute)
**Markdown Cell:**
- Briefly explain RFM analysis (Recency, Frequency, Monetary) and why these features matter for customer analytics
- Set context: "This is our baseline feature set for customer segmentation"

**Code Cell:**
- Load Tasty Bytes customer/order data
- Create Iceberg table
- Write data to Iceberg table

**Code Cell:**
- Use PySpark window functions to compute:
  - `recency_days`: Days since last order
  - `order_frequency`: Total number of orders
  - `monetary_value`: Total revenue per customer
- Add `feature_date` column (today's date)
- Write to partitioned Iceberg table `CUSTOMER_FEATURES`
- Show row count and sample data

**Code Cell:**
- Capture snapshot ID as `v1_snapshot_id`
- Display v1 schema (3 features + metadata columns)
- Save timestamp for later comparison

### 3. Create Version 2: Evolve Schema with New Feature (2 minutes)
**Markdown Cell:**
- Objective: Add a new feature, `customer_ltv_score`, to support lifetime value prediction use cases
- Challenge: Existing ML models rely on version 1 of the feature table in production
- Approach: Use Iceberg schema evolution to introduce the new column without impacting the current version; both versions remain accessible for training and inference


**Code Cell:**
- Read existing features from Iceberg table
- Compute new feature: `customer_ltv_score` (weighted formula based on RFM)
- Use `.withColumn()` to add to existing features
- Write with `mergeSchema=true` option
- Verify write success

**Code Cell:**
- Capture snapshot ID as `v2_snapshot_id`
- Display v2 schema (4 features + metadata columns)
- Show that table now has new column

### 4. Compare Versions Using Time Travel (2-3 minutes)
**Markdown Cell:**
- The power of combined capabilities: "Both versions are preserved and queryable"
- Use case: "Compare model inputs from v1 (3 features) vs v2 (4 features)"
- ML benefit: Safe experimentation without breaking production

**Code Cell:**
- Query v1 snapshot using time travel → show 3 feature columns
- Query v2 snapshot (current) → show 4 feature columns
- Display both DataFrames side-by-side with column counts
- Highlight schema differences

**Code Cell:**
- Compute and compare summary statistics for both versions:
  - v1: Mean/median for recency, frequency, monetary
  - v2: Same stats + LTV score distribution
- Show that base features are identical, only new feature differs
- Demonstrate data integrity across versions

**Code Cell:**
- Show complete snapshot history using metadata query
- Display: snapshot IDs, timestamps, operation types, schemas
- Highlight the evolution: v1 (3 cols) → v2 (4 cols)

**Markdown Cell:**
- Key insights:
  - "Schema evolution created v2 without rewriting v1 data"
  - "Time travel lets you query any historical version"
  - "ML teams can safely test new features while production uses v1"
  - "Rollback if needed: revert to v1 snapshot"
- Real-world scenarios:
  - A/B testing: v1 features for control, v2 for treatment
  - Model debugging: Compare predictions with/without new features
  - Auditing: "What features were available when we trained this model?"

### 5. Key Takeaways & Cleanup (30 seconds)
**Markdown Cell:**
- Reinforce learning objectives:
  1. Iceberg + Snowpark Connect enables flexible, versioned feature stores
  2. Schema evolution lets you add features without breaking downstream consumers
  3. Time travel ensures reproducible ML training on point-in-time data
  4. PySpark API provides familiar syntax on managed Snowflake infrastructure

**Code Cell:**
- Clean up: Drop Iceberg table
- Clear Spark cache
- Success message

**Markdown Cell:**
- Additional resources (docs links)
- Next steps: production feature store patterns

---

## Code Highlights to Include

### Schema Evolution (The "wow" moment)
```python
# Add new feature without breaking existing queries
df_enriched = df_features.withColumn(
    "customer_ltv_score", 
    col("total_revenue") * col("order_frequency") * 1.5
)

# Write with schema evolution enabled
df_enriched.write \
    .mode("append") \
    .option("mergeSchema", "true") \
    .saveAsTable("CUSTOMER_FEATURES")
```

### Time Travel (Reproducibility)
```python
# Query exact features used during model training
training_features = spark.read \
    .option("versionAsOf", v1_snapshot_id) \
    .table("CUSTOMER_FEATURES")

print(f"Training on historical snapshot: {v1_snapshot_id}")
```

### Metadata Inspection
```sql
-- Show snapshot history
SELECT * FROM TABLE(
  INFORMATION_SCHEMA.ICEBERG_TABLE_HISTORY('CUSTOMER_FEATURES')
) ORDER BY made_current_at DESC;
```

---

## Technical Requirements

### Snowpark Connect Demonstrations
- ✅ Initialize Spark session with Snowflake config
- ✅ PySpark transformations (window functions, aggregations)
- ✅ `.saveAsTable()` with Iceberg format
- ✅ `.explain()` showing query pushdown (optional)

### Iceberg-Specific Features
- ✅ `CREATE TABLE ... USING ICEBERG` (via DataFrame write)
- ✅ Partition specification during write (`partitionBy("feature_date")`)
- ✅ Schema evolution via write with `mergeSchema=true`
- ✅ Time travel: `versionAsOf` option
- ✅ Snapshot metadata queries (`ICEBERG_TABLE_HISTORY`)

### ML/Feature Store Context
- ✅ Feature engineering example (RFM analysis)
- ✅ Point-in-time correctness (query historical snapshot)
- ✅ Feature versioning (compare distributions)
- ✅ Schema flexibility (add features incrementally)

---
