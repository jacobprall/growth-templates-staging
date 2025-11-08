# Technical Creative Brief: ML Feature Store Template

## Project Overview

**Template Name:** Build an ML Feature Store with Snowpark Connect for Apache Spark and Iceberg

**Strategic Goal:** Create a compelling, hands-on learning experience that demonstrates how Iceberg's versioning capabilities solve real ML infrastructure challenges—while driving adoption of Snowflake's Iceberg and Snowpark Connect features.

**Target Completion Time:** 5-10 minutes

**Target Audience:** 
- Data engineers building ML infrastructure
- ML platform engineers managing feature stores
- Analytics engineers supporting ML workflows

---

## The Story We're Telling

### The Problem
ML teams face an impossible choice when managing feature stores:
- **Lock schemas** → Block innovation (can't add new features without breaking production)
- **Allow schema changes** → Risk production (existing models break when schemas evolve)

This tension slows down ML iteration and creates operational risk. Traditional databases force you to choose stability OR agility—you can't have both.

### The Solution
Iceberg's schema evolution + time travel working together provide **backward-compatible versioning**:
- Add new features to production without downtime or data rewrites
- Old models query v1 snapshots (3 features) 
- New models query v2 snapshots (4 features)
- Both coexist in the same table with full ACID guarantees

### The Demo Journey
We show this concretely through an RFM analysis evolution:
1. **Version 1:** Create baseline features (recency, frequency, monetary)
2. **Version 2:** Add LTV score via schema evolution (`mergeSchema=true`)
3. **Time Travel:** Query both versions simultaneously, proving backward compatibility
4. **Real-World Value:** Explain A/B testing, debugging, compliance use cases

---

## Key Technical Capabilities to Showcase

### 1. Snowpark Connect for Apache Spark
**Why it matters:** Familiar PySpark API on managed Snowflake infrastructure (no cluster management)

**What we show:**
- PySpark DataFrame operations for feature engineering
- Window functions and aggregations pushed down to Snowflake
- Direct table writes with `.saveAsTable()`

### 2. Iceberg Schema Evolution
**Why it matters:** Add features without breaking production or rewriting data

**What we show:**
- Write with `mergeSchema=true` to add columns
- Zero-copy operation (metadata-only changes)
- Backward compatibility (v1 readers unaffected)

### 3. Iceberg Time Travel
**Why it matters:** Query exact feature snapshots for reproducible ML training

**What we show:**
- Capture snapshot IDs after each write
- Query historical versions with `.option("snapshot_id", v1_snapshot_id)`
- Compare feature distributions across versions

### 4. ML Feature Store Best Practices
**Why it matters:** Bridge from "cool demo" to "production use cases"

**What we show:**
- Point-in-time correctness for training data
- A/B testing with feature versions
- Model debugging via snapshot comparison
- Compliance and audit trails

---

## Critical Challenges & Blockers

### 🚨 PRIMARY BLOCKER: Zero-Setup Iceberg Table Access

**The Problem:**
We do not currently have a way for users to drop in and work with Iceberg tables with zero setup. This is the main blocker preventing us from shipping this template.

**What's Missing:**
- No pre-provisioned Iceberg tables in `SNOWFLAKE_LEARNING_DB`
- No existing Iceberg external volumes or catalogs in the learning environment
- Users would need to manually configure external volumes, catalogs, and Iceberg tables
- This violates the "5-minute, zero-setup" template principle

**What We Need:**
- Pre-created Iceberg-compatible infrastructure in the learning environment
- OR a way to programmatically create Iceberg tables without external volume setup
- OR managed Iceberg tables that work like standard Snowflake tables (ideal)

**Impact on Template:**
- Without this, users hit setup friction immediately
- Breaks the "drop in and run" promise of templates
- Adds 15+ minutes of configuration before any learning begins

### Secondary Challenges

**1. Snowpark Connect Session Initialization**
- Need to ensure Spark session auto-connects to Snowflake in notebook environment
- Potential auth complexity if not pre-configured

**2. Iceberg Metadata Queries**
- `TABLE_STORAGE_METRICS` and `ICEBERG_TABLE_HISTORY` functions need to work
- Snapshot ID capture and time travel syntax must be production-ready

**3. Schema Evolution Syntax**
- Confirm `mergeSchema=true` option works with Snowflake's Iceberg implementation
- Validate that partitioned writes work as expected

---

## Success Criteria

### User Experience Goals
- ✅ **Zero setup required** - User runs cells sequentially without configuration
- ✅ **Fast time-to-insight** - Complete in under 10 minutes
- ✅ **Clear value demonstration** - User thinks "I need this for my ML pipelines"
- ✅ **Production-ready patterns** - Code is copy-paste-ready for real workflows

### Technical Demonstration Goals
- ✅ Create and query Iceberg tables using PySpark
- ✅ Demonstrate schema evolution with `mergeSchema=true`
- ✅ Show time travel querying with snapshot IDs
- ✅ Prove data integrity across schema versions
- ✅ Highlight zero-copy, zero-downtime feature adds

### Learning Outcomes
After completing the template, users should be able to:
1. Build versioned ML feature stores using Iceberg
2. Safely evolve feature schemas without breaking production
3. Query historical feature snapshots for reproducible training
4. Apply these patterns to their own ML infrastructure

---

## Dataset & Environment

**Data Source:** Tasty Bytes `order_header` table
- Customer transaction history for RFM analysis
- Pre-loaded from S3 into `SNOWFLAKE_LEARNING_DB`
- Columns: `customer_id`, `order_ts`, `order_total`, etc.

**Environment Requirements:**
- Standard learning environment: `SNOWFLAKE_LEARNING_ROLE`, `SNOWFLAKE_LEARNING_WH`, `SNOWFLAKE_LEARNING_DB`
- Snowpark Connect for Apache Spark (PySpark API enabled)
- **🚨 Iceberg table support (BLOCKER - see above)**

---

## Next Steps

### Before Development Can Proceed
1. **Resolve Iceberg zero-setup blocker** - Confirm infrastructure approach
2. Validate Snowpark Connect auto-initialization in notebooks
3. Test schema evolution and time travel syntax on Snowflake's Iceberg implementation

### Post-Resolution
1. Implement template following approved patterns
2. Test end-to-end in learning environment
3. Validate 5-10 minute completion time
4. Review with ML SMEs for technical accuracy
5. Ship to production

---

## Open Questions

1. **Iceberg Infrastructure:** What's the path to zero-setup Iceberg table creation in the learning environment?
2. **Snapshot Metadata:** Which metadata functions are available for Iceberg tables in Snowflake?
3. **Time Travel Syntax:** Is `snapshot_id` or `timestamp` the preferred time travel method?
4. **Partitioning:** Do Iceberg partitioned tables work seamlessly with Snowpark Connect writes?
5. **External Volumes:** If required, can these be pre-configured per user or shared globally?
