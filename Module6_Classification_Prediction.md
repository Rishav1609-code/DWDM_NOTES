
# 📘 B.Tech 6th Semester — DWDM Exam Preparation

**Student:** Rishav Raj | **Semester:** 6th Sem | **Subject:** Data Warehousing & Data Mining  
**Module 6:** Classification & Prediction of Data Warehousing | **Marks per Answer:** 5–10 Marks

---

## 📌 How to Use This Document

- Each answer is structured for **5–10 marks** university exam questions.
- Every answer covers **1-2 pages** of content with proper depth.
- Format includes: **Definition → Explanation → Key Points → Diagram/Example → Conclusion**

---
---

## ❓ Question 1: Explain the use of Dynamic Itemset Counting (DIC) algorithm?

### ✅ Answer:

#### 📖 Definition / Introduction

**Dynamic Itemset Counting (DIC)** is an optimization of the Apriori algorithm for frequent itemset mining, proposed by Brin et al. The key idea is that DIC **reduces the number of database scans** by dynamically starting the counting of new candidate itemsets **before** completing the current scan. In standard Apriori, a full database scan must finish for k-itemsets before generating (k+1)-candidates. DIC interleaves candidate generation and counting within the same scan, making it significantly more efficient.

---

#### 📝 Detailed Explanation

**Key Point 1: Problem with Standard Apriori**

> In Apriori:
> - **Pass 1:** Scan entire DB to count all 1-itemsets → find frequent 1-itemsets
> - **Pass 2:** Generate 2-candidates from frequent 1-itemsets → scan entire DB again → find frequent 2-itemsets
> - **Pass k:** Repeat until no more frequent itemsets
>
> **Problem:** If the longest frequent itemset has k items, Apriori needs **k full database scans**. For large databases with long patterns, this is very expensive.

**Key Point 2: How DIC Works**

> DIC divides the database into **M equal-sized partitions** (blocks) called **marking points**. Instead of waiting for a full scan to complete, DIC adds new candidate itemsets **at every marking point** as soon as all their subsets are confirmed frequent.
>
> **Process:**
> 1. **Start** by counting 1-itemsets from the first partition (block)
> 2. At the **first marking point** (end of first partition): any 1-itemset that already exceeds the proportional support threshold → its 2-itemset supersets are immediately added as candidates
> 3. Continue scanning the next partition, now counting **both remaining 1-itemsets and the newly added 2-candidates simultaneously**
> 4. At the **second marking point**: newly confirmed frequent 2-itemsets trigger generation of 3-candidates
> 5. Continue until all partitions are scanned — potentially in **fewer total passes**
>
> **Key Insight:** DIC doesn't wait for a full scan to finish before starting the next level of candidates. By "pipelining" candidate generation and counting, it can discover itemsets of different sizes within the **same database scan**.

**Key Point 3: Itemset States in DIC**

> DIC classifies each itemset into one of **four states** using a box metaphor:
>
> | State | Symbol | Meaning |
> |-------|--------|---------|
> | **Dashed Empty Box** | ☐ (dashed) | Not yet started counting (suspected infrequent) |
> | **Solid Empty Box** | ☐ (solid) | Being counted, currently below threshold (confirmed infrequent) |
> | **Dashed Filled Box** | ■ (dashed) | Being counted, currently above threshold (suspected frequent) |
> | **Solid Filled Box** | ■ (solid) | Completed full scan, confirmed frequent |
>
> Transitions:
> - An itemset starts as **dashed empty** (not counted yet)
> - When counting begins → **solid empty** (counting, below threshold)
> - If count exceeds threshold → **dashed filled** (suspected frequent)
> - After a full DB scan → **solid filled** (confirmed frequent)

**Key Point 4: Advantages of DIC over Apriori**

> | Feature | Apriori | DIC |
> |---------|---------|-----|
> | DB Scans | k scans for k-length patterns | Fewer scans (often 2-3 regardless of pattern length) |
> | Candidate Start | After full scan completes | At each marking point within a scan |
> | Efficiency | Waits unnecessarily | Pipelines counting of different-length itemsets |
> | Speed | Slower for long patterns | Significantly faster |
> | Memory | Candidates of one level at a time | Multiple levels simultaneously (slightly more memory) |

**Key Point 5: When to Use DIC**

> - When the database is very large and multiple scans are expensive
> - When frequent itemsets tend to be long (many levels)
> - When the database can be naturally partitioned into blocks
> - When I/O cost (disk reads) is the primary bottleneck

---

#### 📊 Diagram / Table (if applicable)

```
  APRIORI vs DIC — SCAN COMPARISON:

  APRIORI (4 levels = 4 full scans):
  Scan 1: |====== Count 1-itemsets ======|
  Scan 2: |====== Count 2-itemsets ======|
  Scan 3: |====== Count 3-itemsets ======|
  Scan 4: |====== Count 4-itemsets ======|
  Total: 4 complete database scans

  DIC (4 levels ≈ 2 scans):
  Scan 1: |--P1--|--P2--|--P3--|--P4--|
           count  count  count  count
           1-sets 1+2    1+2+3  1+2+3+4
                  sets   sets   sets
  Scan 2: |--P1--|--P2--|--P3--|--P4--|
           verify verify verify all
           all    all    all    confirmed
  Total: ~2 database scans (not 4!)

  DIC STATE TRANSITIONS:

  ☐ dashed ──→ ☐ solid ──→ ■ dashed ──→ ■ solid
  (not yet     (counting,   (above        (confirmed
   counted)    below min)   threshold)    frequent)

  MARKING POINTS:
  Database: [Block 1][Block 2][Block 3][Block 4]
                ↑         ↑         ↑         ↑
             Mark 1    Mark 2    Mark 3    Mark 4
             Start     Add 2-    Add 3-    Add 4-
             1-items   candidates candidates candidates
```

---

#### 💡 Example

> **Example:** Database has 10,000 transactions divided into 4 blocks of 2,500 each. Min support = 5%.
>
> - **Block 1 (T1–T2500):** Count {A}, {B}, {C}, {D}, {E}. After Block 1: {A}=600, {B}=500, {C}=700 → proportionally above threshold → immediately start counting {A,B}, {A,C}, {B,C}
> - **Block 2 (T2501–T5000):** Continue counting remaining 1-itemsets AND 2-itemsets simultaneously. After Block 2: {A,C}=300 → above proportional threshold → start counting {A,B,C}
> - **Block 3:** Count 1, 2, AND 3-itemsets together in the same scan
> - **Block 4:** Complete all counts in the same scan
>
> **Result:** What would take 3 full scans in Apriori is accomplished in approximately **1.5 scans** with DIC.

---

#### 🔚 Conclusion

> Dynamic Itemset Counting (DIC) significantly improves upon Apriori by interleaving candidate generation with support counting at designated marking points within a single database scan. Instead of waiting for a complete scan before advancing to the next level, DIC starts counting higher-level candidates as soon as their subsets are confirmed frequent — reducing the total number of database scans from k (for k-length patterns) to typically 2-3. This makes DIC particularly effective for large databases with long frequent patterns where I/O cost dominates.

---
---

## ❓ Question 2: Describe the principle of partitioning technique for frequent itemset generation & justify how it improves efficiency compared to Apriori? State the Apriori algorithm?

### ✅ Answer:

#### 📖 Definition / Introduction

The **Partitioning technique** (proposed by Savasere, Omiecinski, and Navathe) is an efficient approach for frequent itemset mining that divides the database into **non-overlapping partitions**, mines each partition independently for local frequent itemsets, and then combines results. It requires only **two database scans** regardless of itemset length. The **Apriori algorithm** (Agrawal & Srikant, 1994) is the foundational algorithm for frequent itemset generation using a level-wise, candidate-generation-and-test approach.

---

#### 📝 Detailed Explanation

**Key Point 1: Apriori Algorithm — Statement**

> **Apriori Principle (Anti-monotone property):** If an itemset is **infrequent**, then all its **supersets** must also be infrequent. Equivalently, every subset of a frequent itemset must also be frequent.
>
> **Algorithm:**
> ```
> INPUT: Database D, minimum support threshold (min_sup)
> OUTPUT: All frequent itemsets L
>
> 1. L₁ = {frequent 1-itemsets}          // Scan DB, count each item
> 2. for (k = 2; L_{k-1} ≠ ∅; k++) do
> 3.    C_k = apriori_gen(L_{k-1})       // Generate candidates
> 4.    for each transaction t ∈ D do     // Scan DB
> 5.       C_t = subset(C_k, t)          // Find candidates in t
> 6.       for each candidate c ∈ C_t do
> 7.          c.count++                   // Increment count
> 8.    end for
> 9.    L_k = {c ∈ C_k | c.count ≥ min_sup}  // Prune
> 10. end for
> 11. return L = ∪_k L_k
> ```
>
> **apriori_gen (Candidate Generation):**
> - **Join Step:** Join L_{k-1} with itself — combine two (k-1)-itemsets that share (k-2) items
> - **Prune Step:** Remove any candidate whose (k-1)-subset is not in L_{k-1} (using Apriori principle)

**Key Point 2: Partitioning Technique — Principle**

> The partitioning technique works in **two phases** requiring exactly **two database scans**:
>
> **Phase 1: Local Frequent Itemset Generation (Scan 1)**
> > 1. Divide database D into **n non-overlapping partitions**: P₁, P₂, ..., P_n
> > 2. Each partition is small enough to **fit in main memory**
> > 3. For each partition P_i, independently find **all local frequent itemsets** (itemsets frequent within that partition) using Apriori or any algorithm
> > 4. **Local support threshold** = (min_sup × |P_i|) for partition P_i
> > 5. Union all local frequent itemsets from all partitions → **candidate set** for Phase 2
>
> **Phase 2: Global Frequent Itemset Verification (Scan 2)**
> > 1. Take the union of all local frequent itemsets as **global candidates**
> > 2. Scan the **entire database once** to compute the **global support** of each candidate
> > 3. Keep only those with global support ≥ min_sup → **final frequent itemsets**
>
> **Key Theorem (Correctness):**
> > If an itemset X is globally frequent (frequent in the entire DB), then X must be locally frequent in **at least one partition**. Therefore, the union of all local frequent itemsets is a **superset** of all global frequent itemsets — no true frequent itemset is missed.
>
> **Proof:** If X has global support ≥ min_sup, suppose X is NOT locally frequent in ANY partition. Then in each partition P_i, support(X) < min_sup. Summing across all partitions: total support(X) < n × min_sup partitions... but this contradicts X being globally frequent. ∴ X must be locally frequent in at least one partition.

**Key Point 3: Why Partitioning Improves Efficiency over Apriori**

> | Aspect | Apriori | Partitioning |
> |--------|---------|-------------|
> | **DB Scans** | k scans for k-length patterns | Exactly 2 scans (always) |
> | **Memory** | May not fit in memory for large DBs | Each partition fits in memory |
> | **I/O Cost** | High (multiple full scans) | Low (only 2 full scans) |
> | **Parallelization** | Difficult (each level depends on previous) | Easy — each partition mined independently |
> | **Candidate Generation** | Expensive join + prune at each level | Local mining is fast (small partitions); global is just one verification scan |
> | **Scalability** | Degrades with DB size | Scales well — partition sizes remain manageable |
>
> **Key Justification:**
> 1. **Reduced I/O:** Only 2 scans vs. potentially many — major savings for disk-based databases
> 2. **Memory Efficiency:** Each partition fits entirely in RAM → in-memory mining is fast
> 3. **Parallelism:** Partitions can be mined **simultaneously** on different processors/machines
> 4. **Independence:** No inter-partition dependency in Phase 1

---

#### 📊 Diagram / Table (if applicable)

```
  APRIORI ALGORITHM FLOW:

  Database D
      ↓
  Scan 1 → L₁ (frequent 1-itemsets)
      ↓
  Generate C₂ (candidates from L₁)
      ↓
  Scan 2 → L₂ (frequent 2-itemsets)
      ↓
  Generate C₃ → Scan 3 → L₃ → ... → L_k → STOP (L_k = ∅)

  APRIORI PRUNING PRINCIPLE:
  If {A,B} is INFREQUENT:
  → {A,B,C}, {A,B,D}, {A,B,C,D} ALL INFREQUENT (pruned!)

  PARTITIONING TECHNIQUE:

  Database D (too large for memory):
  ┌────────┬────────┬────────┬────────┐
  │  P₁    │  P₂    │  P₃    │  P₄    │
  └───┬────┘───┬────┘───┬────┘───┬────┘
      ↓        ↓        ↓        ↓
  PHASE 1 (Scan 1): Mine each partition independently
      ↓        ↓        ↓        ↓
  Local F₁  Local F₂  Local F₃  Local F₄
      └────────┼────────┼────────┘
               ↓
        UNION of all local frequent itemsets
        = Global Candidates
               ↓
  PHASE 2 (Scan 2): Verify global support
               ↓
        FINAL FREQUENT ITEMSETS

  COMPARISON:
  ┌──────────────┬───────────┬──────────────┐
  │              │ Apriori   │ Partitioning │
  ├──────────────┼───────────┼──────────────┤
  │ DB Scans     │ k (many)  │ 2 (always)   │
  │ Memory       │ Full DB   │ 1 partition  │
  │ Parallel?    │ Difficult │ Easy         │
  │ I/O Cost     │ High      │ Low          │
  └──────────────┴───────────┴──────────────┘
```

---

#### 💡 Example

> **Example:** Database D has 1,000,000 transactions, min_sup = 5%.
>
> **Apriori approach:** If longest frequent itemset is 6-items → 6 full scans of 1M records = 6 million record reads.
>
> **Partitioning approach:**
> - Divide into 10 partitions of 100K each (each fits in 4 GB RAM)
> - **Phase 1:** Mine each partition independently. Local min_sup = 5% × 100K = 5,000 transactions. Find local frequent itemsets in each partition (entirely in-memory → fast). Union = 2,000 candidate itemsets.
> - **Phase 2:** One scan of full 1M records to verify 2,000 candidates → keep those with global count ≥ 50,000.
> - **Total:** 2 scans of 1M = 2 million record reads (vs. 6 million for Apriori!)
> - **Bonus:** Phase 1 can run on 10 machines in parallel → 10× speedup.

---

#### 🔚 Conclusion

> The Apriori algorithm uses level-wise candidate generation with the anti-monotone property to prune the search space, but requires k database scans for k-length patterns. The partitioning technique improves efficiency by dividing the database into memory-fitting partitions, mining each independently (Phase 1), and verifying globally (Phase 2) — requiring only 2 scans regardless of pattern length. It offers superior I/O efficiency, memory usage, and natural parallelism. The correctness is guaranteed because any globally frequent itemset must be locally frequent in at least one partition.

---
---

## ❓ Question 3: What is clustering? Discuss two main methods of clustering? Why is it difficult to handle categorical data for clustering? Distinguish between partition clustering & hierarchical clustering? What are the features of a good cluster? Why is clustering required for DW & DM?

### ✅ Answer:

#### 📖 Definition / Introduction

**Clustering** is an **unsupervised learning** technique that groups a set of data objects into **clusters** such that objects within the same cluster are **highly similar** to each other and objects in different clusters are **highly dissimilar**. Unlike classification (supervised), clustering does not use predefined class labels — the algorithm discovers natural groupings in the data on its own.

---

#### 📝 Detailed Explanation

**Key Point 1: Two Main Methods of Clustering**

> **Method 1: Partitioning Methods**
> > - Divide n data objects into **K non-overlapping clusters** (K specified by user)
> > - Each object belongs to **exactly one cluster**
> > - Optimizes an objective function — typically minimizing **within-cluster variance** (sum of squared distances from each point to its cluster centroid)
> > - **Iterative refinement:** Start with initial partition → reassign objects → recalculate centroids → repeat until convergence
> > - **Key algorithms:** K-Means, K-Medoids (PAM), CLARA, CLARANS
> > - **Pros:** Simple, efficient, scalable
> > - **Cons:** Must specify K; sensitive to initialization; assumes spherical clusters
>
> **Method 2: Hierarchical Methods**
> > - Create a **tree-like hierarchy** of clusters (dendrogram) — no need to specify K in advance
> > - **Two approaches:**
> >   - **Agglomerative (Bottom-Up):** Start with each object as its own cluster → iteratively merge the **two closest clusters** → continue until one cluster remains or desired K is reached
> >   - **Divisive (Top-Down):** Start with all objects in one cluster → iteratively split the **most dissimilar cluster** → continue until each object is its own cluster or desired K is reached
> > - **Linkage criteria** (for measuring inter-cluster distance):
> >   - **Single Linkage:** Minimum distance between any two points in different clusters
> >   - **Complete Linkage:** Maximum distance between any two points in different clusters
> >   - **Average Linkage:** Average distance between all pairs of points
> >   - **Ward's Method:** Minimize increase in total within-cluster variance
> > - **Key algorithms:** AGNES (agglomerative), DIANA (divisive), BIRCH, CURE
> > - **Pros:** No need to specify K; produces dendrogram for visual analysis; can discover arbitrary shapes
> > - **Cons:** O(n²) or O(n³) complexity; irreversible merge/split decisions; not scalable for large datasets

**Key Point 2: Why Categorical Data is Difficult for Clustering**

> Most clustering algorithms (especially K-Means) rely on **distance metrics** (Euclidean, Manhattan) that require **numerical data**. Categorical data poses unique challenges:
>
> | Challenge | Explanation |
> |-----------|-------------|
> | **No natural distance** | How far is "Red" from "Blue"? There's no inherent numerical distance between categories |
> | **No meaningful mean** | K-Means uses centroid (mean) — what is the "mean" of {Red, Blue, Green}? Undefined for categories |
> | **No ordering** | Categories like {Male, Female} or {Honda, Toyota, BMW} have no natural order — cannot compute greater/lesser |
> | **Mixed data** | Real datasets often mix numerical and categorical attributes — different distance measures needed |
> | **High cardinality** | Attributes with many categories (e.g., ZIP codes with 10,000+ values) create very sparse, high-dimensional spaces |
>
> **Solutions:**
> - **K-Modes algorithm:** Replaces mean with **mode** and uses **simple matching distance** (count of mismatched attributes)
> - **K-Prototypes:** Combines K-Means (for numerical) and K-Modes (for categorical) — for mixed data
> - **Gower's Distance:** Unified distance metric that handles numerical, categorical, and binary attributes
> - **One-Hot Encoding:** Convert categories to binary vectors → then use standard distance (but increases dimensionality)

**Key Point 3: Partition vs Hierarchical Clustering — Comparison**

> | Feature | Partition Clustering | Hierarchical Clustering |
> |---------|---------------------|------------------------|
> | **Approach** | Divide data into K flat groups | Build tree of nested clusters |
> | **K required?** | Yes, must specify K upfront | No, K can be chosen after by cutting dendrogram |
> | **Output** | K flat clusters | Dendrogram (tree structure) |
> | **Complexity** | O(n × K × t) — efficient | O(n²) to O(n³) — expensive |
> | **Scalability** | Good for large datasets | Poor for large datasets |
> | **Reversibility** | Reassignment possible each iteration | Merge/split is irreversible |
> | **Cluster Shape** | Assumes spherical (K-Means) | Can discover arbitrary shapes |
> | **Sensitivity** | Sensitive to initialization, outliers | Sensitive to linkage method, noise |
> | **Examples** | K-Means, K-Medoids, CLARA | AGNES, DIANA, BIRCH, CURE |
> | **Best for** | Large, well-separated data | Small-medium data, exploratory analysis |

**Key Point 4: Features of a Good Cluster**

> | Feature | Description |
> |---------|-------------|
> | **High Intra-cluster Similarity** | Objects within the same cluster should be as **similar** as possible (low within-cluster variance) |
> | **High Inter-cluster Dissimilarity** | Objects in different clusters should be as **dissimilar** as possible (large distance between cluster centers) |
> | **Compactness** | Cluster members are close to the centroid/medoid — small diameter |
> | **Separation** | Clusters are well-separated from each other — clear boundaries |
> | **Connectedness** | Nearby data points belong to the same cluster |
> | **Robustness** | Not overly influenced by noise or outliers |
> | **Scalability** | Algorithm produces consistent results regardless of data size |
> | **Interpretability** | Clusters should be meaningful and interpretable by domain experts |
> | **Balance** | Clusters shouldn't be extremely unequal in size (unless data naturally requires it) |

**Key Point 5: Why Clustering is Required for DW & DM**

> | Application | How Clustering Helps |
> |-------------|---------------------|
> | **Data Summarization** | Cluster large DW tables into groups → summarize each cluster → faster OLAP queries |
> | **Customer Segmentation** | Group customers by behavior → targeted marketing, personalized services |
> | **Anomaly Detection** | Points not belonging to any cluster → potential anomalies (fraud, defects) |
> | **Data Preprocessing** | Clustering as a preprocessing step for classification or association mining |
> | **Dimensionality Reduction** | Replace data with cluster representatives → reduce DW storage |
> | **Pattern Discovery** | Discover natural groupings in data → reveal hidden business insights |
> | **Data Cleaning** | Identify outliers and noise during DW loading (ETL) |
> | **Trend Analysis** | Track how clusters evolve over time → strategic insights |

---

#### 📊 Diagram / Table (if applicable)

```
  PARTITIONING vs HIERARCHICAL:

  PARTITIONING (K-Means, K=3):
  ┌─ ─ ─ ─┐ ┌─ ─ ─ ─┐ ┌─ ─ ─ ─┐
  │ ● ● ●  │ │ ▲ ▲ ▲  │ │ ■ ■ ■  │
  │  ● ●   │ │ ▲  ▲   │ │  ■ ■   │
  │   ★    │ │   ★    │ │   ★    │
  └─ ─ ─ ─┘ └─ ─ ─ ─┘ └─ ─ ─ ─┘
  Cluster 1  Cluster 2  Cluster 3
  (★ = centroid, flat partitions)

  HIERARCHICAL (Agglomerative):
  Step 1: {A} {B} {C} {D} {E}    ← 5 clusters
  Step 2: {A,B} {C} {D} {E}      ← merge closest
  Step 3: {A,B} {C} {D,E}        ← merge closest
  Step 4: {A,B,C} {D,E}          ← merge closest
  Step 5: {A,B,C,D,E}            ← 1 cluster

  DENDROGRAM:
  Height
   5 ─┤          ┌────────┐
   4 ─┤     ┌────┤        │
   3 ─┤  ┌──┤    │        │
   2 ─┤  │  │    │   ┌────┤
   1 ─┤──┤  │    │   │    │
   0 ─┤  A  B    C   D    E
       Cut at height 3 → 2 clusters: {A,B,C} and {D,E}

  CATEGORICAL DATA PROBLEM:
  Point 1: (Red, Male, Honda)
  Point 2: (Blue, Female, Toyota)
  Euclidean Distance = ??? (undefined for categories!)
  
  Solution — Simple Matching Distance:
  Mismatches: Red≠Blue(1), Male≠Female(1), Honda≠Toyota(1)
  Distance = 3/3 = 1.0 (all attributes differ)

  GOOD CLUSTER PROPERTIES:
  ┌─────────────────────────────────────────┐
  │ GOOD CLUSTERS        BAD CLUSTERS       │
  │                                         │
  │ ●●●    ▲▲▲           ●▲●▲              │
  │ ●●●    ▲▲▲            ●▲●▲             │
  │ (compact,             (mixed,           │
  │  separated)            overlapping)     │
  └─────────────────────────────────────────┘
```

---

#### 💡 Example

> **Customer Segmentation (Retail DW):** An e-commerce data warehouse contains 5 million customer records with attributes: purchase frequency, average order value, recency of last purchase. K-Means clustering (K=4) reveals:
> - **Cluster 1 — "High-Value Loyal":** Frequent purchases, high AOV, recent activity → premium loyalty rewards
> - **Cluster 2 — "Bargain Hunters":** Frequent but low AOV, buy only during sales → flash sale notifications
> - **Cluster 3 — "At-Risk":** Used to be active but haven't purchased in 6 months → send win-back campaigns
> - **Cluster 4 — "New Customers":** Recent first purchase, low frequency → welcome series with onboarding discounts
>
> Each cluster gets a **different marketing strategy** — this is only possible through clustering.

---

#### 🔚 Conclusion

> Clustering is a fundamental unsupervised technique that groups data by similarity without predefined labels. The two main methods — partitioning (K-Means, K-Medoids) and hierarchical (agglomerative, divisive) — differ in approach, scalability, and output. Categorical data is challenging because standard distance metrics and means are undefined for categories — solved by K-Modes and Gower's distance. Good clusters exhibit high intra-cluster similarity, inter-cluster dissimilarity, compactness, and robustness. Clustering is essential in DW/DM for customer segmentation, anomaly detection, data summarization, and pattern discovery.

---
---

## ❓ Question 4: What is metadata in data warehousing? What is metadata catalog? Explain different types of metadata?

### ✅ Answer:

#### 📖 Definition / Introduction

**Metadata** in data warehousing is **"data about data"** — it describes the structure, content, meaning, origin, transformations, and usage of the data stored in the warehouse. Metadata is essential for understanding what data exists, where it came from, how it was transformed, and how it can be used. A **metadata catalog** (or metadata repository) is a centralized storage system that organizes, manages, and provides access to all metadata in the data warehousing environment.

---

#### 📝 Detailed Explanation

**Key Point 1: What is Metadata?**

> Metadata provides **context and meaning** to the raw data in the warehouse. Without metadata, the data warehouse is just a collection of numbers and strings with no explanation.
>
> **Examples of metadata:**
> - Table name: `Sales_Fact`; Column: `revenue`; Data type: `DECIMAL(12,2)`; Description: "Total revenue in INR"
> - "This data was extracted from Oracle ERP on 2024-03-15 at 02:00 AM using Informatica ETL Job #42"
> - "The 'customer_id' in Sales_Fact maps to 'cust_key' in Customer_Dim"
> - "This column was derived by multiplying quantity × unit_price"
> - "Last updated: 2024-03-16; Update frequency: Daily at 2 AM"

**Key Point 2: Metadata Catalog (Repository)**

> A **metadata catalog** is a **centralized, searchable repository** that stores and manages all metadata for the data warehousing environment.
>
> **Functions:**
> - **Discovery:** Helps users find what data exists and where it is located
> - **Lineage:** Tracks data from source to warehouse (where did this number come from?)
> - **Impact Analysis:** If a source column changes, what downstream reports are affected?
> - **Documentation:** Self-documenting data dictionary for the entire warehouse
> - **Governance:** Enforces data quality rules, access policies, and naming standards
> - **Search:** Users can search for tables, columns, or business terms
>
> **Tools:** Apache Atlas, Alation, Collibra, AWS Glue Data Catalog, Google Data Catalog

**Key Point 3: Types of Metadata**

> **Type 1: Technical Metadata (Structural/System Metadata)**
> > Describes the **technical structure** of data — database schemas, table definitions, column properties.
> >
> > | Element | Example |
> > |---------|---------|
> > | Table definitions | Table: `Sales_Fact`, 15 columns, 50M rows |
> > | Column definitions | `revenue`: DECIMAL(12,2), NOT NULL |
> > | Data types & constraints | Primary key, foreign key, indexes |
> > | Database schema | Star schema with 1 fact + 5 dimensions |
> > | Storage details | Partition by date, stored on SSD |
> > | Index information | B-tree index on `product_key` |
>
> **Type 2: Business Metadata (Descriptive Metadata)**
> > Describes the **business meaning** of data in non-technical, business-friendly language.
> >
> > | Element | Example |
> > |---------|---------|
> > | Business definitions | "Revenue = total sales amount excluding returns and taxes" |
> > | Business rules | "A customer is 'active' if they purchased in the last 90 days" |
> > | Data ownership | "Sales data owned by VP of Sales, John Kumar" |
> > | Business glossary | "AOV = Average Order Value = Total Revenue / Number of Orders" |
> > | Report descriptions | "Monthly Sales Dashboard — shows KPIs for management review" |
>
> **Type 3: Operational Metadata (Process Metadata)**
> > Describes the **operational processes** — ETL jobs, data lineage, job schedules, performance stats.
> >
> > | Element | Example |
> > |---------|---------|
> > | ETL job details | "Job: Load_Sales_Daily runs at 2 AM, takes 45 mins" |
> > | Data lineage | "revenue in Sales_Fact ← SUM(line_total) from ERP.Order_Lines" |
> > | Load statistics | "Last load: 2024-03-16, 500,000 rows inserted, 0 errors" |
> > | Data quality metrics | "98.5% completeness, 99.9% accuracy for customer records" |
> > | Job scheduling | "Incremental load every 4 hours; full refresh every Sunday" |
> > | Error logs | "2024-03-15: 12 records rejected due to NULL customer_id" |
>
> **Type 4: Administrative Metadata**
> > Describes **access control, security, and governance** policies.
> >
> > | Element | Example |
> > |---------|---------|
> > | Access permissions | "HR data: Read access for HR team only" |
> > | User roles | "Analyst role: SELECT on all fact tables; Admin: full access" |
> > | Audit trails | "User 'rishav_raj' ran query on Sales_Fact at 10:30 AM" |
> > | Retention policies | "Keep detailed data for 2 years; aggregated for 7 years" |
> > | Compliance | "PII data encrypted at rest; GDPR-compliant masking applied" |

---

#### 📊 Diagram / Table (if applicable)

```
  METADATA IN DATA WAREHOUSING:

  ┌──────────────────────────────────────────────┐
  │              DATA WAREHOUSE                   │
  │                                              │
  │  Actual Data:                                │
  │  ┌─────────┬─────────┬──────────┐            │
  │  │ cust_id │ product │ revenue  │            │
  │  │ 1001    │ Laptop  │ 65000.00 │            │
  │  │ 1002    │ Phone   │ 25000.00 │            │
  │  └─────────┴─────────┴──────────┘            │
  │                                              │
  │  Metadata (about the above data):            │
  │  ┌──────────────────────────────────────┐    │
  │  │ • "revenue" is DECIMAL(12,2)         │    │
  │  │ • Source: Oracle ERP, Order_Lines     │    │
  │  │ • Loaded daily at 2 AM via ETL       │    │
  │  │ • Business def: "Sales excl. tax"    │    │
  │  │ • Owner: VP Sales                    │    │
  │  │ • Access: Sales team + Management    │    │
  │  └──────────────────────────────────────┘    │
  └──────────────────────────────────────────────┘

  TYPES OF METADATA:
  ┌─────────────────────────────────────────────────┐
  │              METADATA CATALOG                    │
  ├───────────────┬─────────────────────────────────┤
  │ Technical     │ Schemas, tables, columns, types │
  ├───────────────┼─────────────────────────────────┤
  │ Business      │ Definitions, rules, glossary    │
  ├───────────────┼─────────────────────────────────┤
  │ Operational   │ ETL jobs, lineage, load stats   │
  ├───────────────┼─────────────────────────────────┤
  │ Administrative│ Access control, audit, retention│
  └───────────────┴─────────────────────────────────┘

  DATA LINEAGE (via Metadata):
  Oracle ERP          SAP CRM           CSV Files
  Order_Lines         Customer           Products.csv
       ↓                  ↓                 ↓
       └──────── ETL (Informatica) ────────┘
                       ↓
              Data Warehouse
              Sales_Fact table
                       ↓
              Power BI Dashboard
              "Monthly Sales Report"

  Metadata tracks this ENTIRE lineage!
```

---

#### 💡 Example

> **Example:** A business analyst sees `revenue = 1,25,00,000` in the quarterly report and asks: "Where did this number come from?" Using the **metadata catalog**:
>
> - **Technical metadata** tells her: `revenue` column is in `Sales_Fact` table, type DECIMAL(12,2)
> - **Business metadata** explains: "Revenue = total sales amount excluding returns, discounts, and taxes"
> - **Operational metadata** shows lineage: "This value = SUM(Sales_Fact.revenue) where quarter='Q1-2024'. Sales_Fact.revenue was loaded from Oracle ERP table `ORDER_LINES.line_total` via ETL Job `Load_Sales_Daily` running every night at 2 AM. Last successful load: March 31, 2024"
> - **Administrative metadata** confirms: "Data validated by Finance team. Retention: 7 years. Access: Analyst + Management roles"
>
> Without metadata, the analyst would have no way to verify or understand this number.

---

#### 🔚 Conclusion

> Metadata is "data about data" — providing essential context for understanding, managing, and governing a data warehouse. The metadata catalog is a centralized repository for storing and searching all metadata. Four types of metadata serve different purposes: technical (structure), business (meaning), operational (processes and lineage), and administrative (access and governance). Effective metadata management enables data discovery, impact analysis, data lineage tracking, and regulatory compliance — making metadata the backbone of a well-governed data warehousing environment.

---
---

## ❓ Question 5: Describe the working of the PAM algorithm? Compare its performance with CLARA & CLARANS? Write a short note on CLARANS vs PAM & CLARA? How is CLARANS different from CLARA?

### ✅ Answer:

#### 📖 Definition / Introduction

**PAM (Partitioning Around Medoids)**, also known as **K-Medoids**, is a partitioning-based clustering algorithm that selects **actual data points (medoids)** as cluster centers — unlike K-Means which uses computed means (centroids). PAM is more robust to outliers than K-Means. **CLARA (Clustering Large Applications)** and **CLARANS (Clustering Large Applications based on Randomized Search)** are scalable extensions of PAM designed for larger datasets.

---

#### 📝 Detailed Explanation

**Key Point 1: PAM Algorithm — Working**

> **Input:** Dataset of n objects, number of clusters K
> **Output:** K clusters with medoids as representatives
>
> **Step 1: Initialization (BUILD Phase)**
> > Select K initial medoids from the dataset. Methods:
> > - Random selection
> > - First medoid = most centrally located point; subsequent medoids chosen to minimize total dissimilarity
>
> **Step 2: Assignment**
> > Assign every non-medoid object to the cluster of its **nearest medoid** (using distance metric — e.g., Manhattan or Euclidean)
>
> **Step 3: Swap Evaluation (SWAP Phase)**
> > For every pair of (medoid m, non-medoid o):
> > 1. **Tentatively swap** m with o — make o the new medoid, remove m
> > 2. Reassign all objects to the nearest medoid (after swap)
> > 3. Calculate the **total cost** (sum of distances of all objects to their nearest medoid)
> > 4. Compute **ΔTC** = (new total cost) - (old total cost)
> > 5. If ΔTC < 0 → the swap **improves** clustering → **perform the swap**
> > 6. Choose the swap with the **most negative ΔTC** (greatest improvement)
>
> **Step 4: Repeat**
> > Repeat Step 2-3 until **no swap reduces the total cost** → algorithm converges
>
> **Cost Computation:**
> > Total Cost (TC) = Σᵢ distance(objectᵢ, nearest_medoid)
> > Goal: Minimize TC
>
> **Complexity:** O(K × (n-K)² × t) per iteration — expensive for large n

**Key Point 2: CLARA (Clustering Large Applications)**

> CLARA is designed for **large datasets** where PAM is too slow.
>
> **Approach:**
> 1. Draw **multiple random samples** from the large dataset (sample size typically 40 + 2K)
> 2. Apply **PAM on each sample** to find K medoids
> 3. For each set of medoids, assign **all objects** in the full dataset to the nearest medoid and compute total cost
> 4. Return the set of medoids with the **lowest total cost**
>
> **Key Idea:** Instead of running PAM on the full dataset (too expensive), run it on small samples and pick the best result.
>
> **Limitation:** Quality depends on the sample — if the sample is not representative, the best medoids may be missed. Only examines a limited subset of potential medoids.

**Key Point 3: CLARANS (Clustering Large Applications based on Randomized Search)**

> CLARANS improves upon CLARA by exploring the **search space more intelligently** using a randomized neighbor-based approach.
>
> **Approach:**
> 1. Start with K randomly selected medoids (current node in the "graph" of possible medoid sets)
> 2. Define **neighbors**: a neighbor of the current node is obtained by replacing one medoid with a non-medoid
> 3. Randomly select a neighbor → compute the cost change
> 4. If the neighbor has **lower cost** → move to that neighbor (accept the swap)
> 5. If the neighbor has **higher cost** → try another random neighbor
> 6. After testing **maxneighbor** random neighbors without improvement → declare this a **local optimum**
> 7. Restart with new random medoids (repeat **numlocal** times)
> 8. Return the **best local optimum** across all restarts
>
> **Key Difference from CLARA:** CLARANS does NOT restrict search to a sample — it considers the **entire dataset** at each step, but samples the neighbor space randomly instead of exhaustively.

**Key Point 4: Comparison — PAM vs CLARA vs CLARANS**

> | Feature | PAM | CLARA | CLARANS |
> |---------|-----|-------|---------|
> | **Full name** | Partitioning Around Medoids | Clustering Large Applications | Clustering Large Apps based on Randomized Search |
> | **Approach** | Exhaustive swap of all (medoid, non-medoid) pairs | PAM on random samples | Randomized neighbor search on full data |
> | **Dataset size** | Small (100s-1000s) | Large (10,000s+) | Large (10,000s+) |
> | **Search space** | Examines ALL possible swaps | Limited to sampled objects | Randomized subset of neighbors |
> | **Quality** | Best (exact optimization within constraint) | Lower (depends on sample quality) | Better than CLARA |
> | **Complexity** | O(K(n-K)²) — very high | O(K(40+2K)² × samples) — moderate | O(n² × numlocal) — moderate |
> | **Scalability** | Poor | Good | Very Good |
> | **Outlier Robustness** | High (uses medoids) | High | High |
> | **Drawback** | Too slow for large n | Best medoids may not be in sample | May converge to local optima |
>
> **CLARANS vs CLARA — Key Differences:**
> | Aspect | CLARA | CLARANS |
> |--------|-------|---------|
> | **Sample** | Fixed samples of data | No fixed sample — full dataset |
> | **Search** | PAM on sample; biased to sample | Random neighbor search; unbiased |
> | **Coverage** | Only medoids in sample are considered | Any object can become a medoid |
> | **Quality** | Limited by sample representativeness | Typically better clustering quality |
> | **Efficiency** | Faster per iteration | Slightly slower but better results |
> | **Graph analogy** | Searches a few fixed subgraphs | Random walk on the full graph |

---

#### 📊 Diagram / Table (if applicable)

```
  PAM ALGORITHM FLOW:

  Step 1: Select K=2 initial medoids: M₁, M₂
  
  Dataset: A B C D E F G H
  Initial Medoids: M₁=C, M₂=F

  Step 2: Assign to nearest medoid
  Cluster 1 (C): {A, B, C, D}
  Cluster 2 (F): {E, F, G, H}
  Total Cost = Σ distances = 15.2

  Step 3: Try ALL swaps:
  Swap C↔A: TC=16.5 (worse)  ✗
  Swap C↔B: TC=14.8 (better!) ✓ ← best so far
  Swap C↔D: TC=15.0 (better)
  Swap F↔E: TC=15.5 (worse)  ✗
  ...
  Best swap: C↔B (TC=14.8, ΔTC=-0.4)

  Step 4: Perform swap. New medoids: M₁=B, M₂=F
  Repeat until no swap improves cost.

  PAM vs CLARA vs CLARANS:

  PAM:           CLARA:          CLARANS:
  Full dataset   Sample → PAM    Full dataset
  All swaps      All swaps on    Random neighbor
  tested         sample only     swaps tested
  ┌──────────┐   ┌──────────┐   ┌──────────┐
  │●●●●●●●●●●│   │●●○○○○○○○○│   │●●●●●●●●●●│
  │●●●●●●●●●●│   │○●○○○●○○○○│   │●●●●●●●●●●│
  │ Test ALL  │   │ Test on  │   │ Random   │
  │ swaps     │   │ sample ● │   │ neighbor │
  └──────────┘   └──────────┘   └──────────┘
  Best quality   Fast but may    Good quality
  but SLOW       miss best       + scalable
```

---

#### 💡 Example

> **PAM Example:** 6 data points in 2D: A(1,1), B(2,2), C(3,1), D(8,8), E(9,9), F(10,8). K=2.
> - **Initial medoids:** M₁=A(1,1), M₂=D(8,8)
> - **Assign:** Cluster 1={A,B,C} (near A), Cluster 2={D,E,F} (near D)
> - **TC** = d(A,A)+d(B,A)+d(C,A)+d(D,D)+d(E,D)+d(F,D) = 0+1.41+2+0+1.41+2 = **6.82**
> - **Try swap A↔B:** New medoid B(2,2). TC = d(A,B)+0+d(C,B)+d(D,D)+d(E,D)+d(F,D) = 1.41+0+1.41+0+1.41+2 = **6.23** → Better! Accept.
> - Continue until convergence. **Medoids are actual data points → robust to outliers.**
>
> **CLARA vs CLARANS:** For 1 million data points with K=5:
> - **PAM:** Must evaluate 5 × 999,995 ≈ 5M swap candidates per iteration — **too slow**
> - **CLARA:** Draws 5 samples of 50 objects each → runs PAM on each 50-object sample (fast!) → picks best
> - **CLARANS:** Starts with random 5 medoids on the full 1M dataset → randomly tests neighbors (maxneighbor times) → accepts improvements → restarts numlocal times → **better quality than CLARA because it considers ALL objects as potential medoids**

---

#### 🔚 Conclusion

> PAM (K-Medoids) selects actual data points as cluster centers through exhaustive swap evaluation, making it robust to outliers but computationally expensive O(K(n-K)²). CLARA scales PAM by applying it to random samples — fast but may miss optimal medoids not in the sample. CLARANS improves upon CLARA by performing randomized neighbor search on the full dataset, achieving better quality while maintaining scalability. The key progression is: PAM (best quality, worst scalability) → CLARA (good scalability, limited quality) → CLARANS (good scalability, better quality through full-dataset random walk).

---
---

## ❓ Question 6: What is dimensional modeling? What is a generalized association rule? Discuss K-means or K-medoid algorithm with example? Explain KNN algorithm with example?

### ✅ Answer:

#### 📖 Definition / Introduction

**Dimensional modeling** is a data warehouse design technique that organizes data into **fact tables** (measurements) and **dimension tables** (descriptive context) for intuitive querying and fast analytical performance. **Generalized association rules** discover associations at multiple concept hierarchy levels. **K-Means and K-Medoids** are partitioning-based clustering algorithms, while **KNN (K-Nearest Neighbors)** is a simple yet powerful instance-based classification algorithm.

---

#### 📝 Detailed Explanation

**Key Point 1: Dimensional Modeling**

> Dimensional modeling (popularized by **Ralph Kimball**) structures data warehouse schemas around **business processes** using two types of tables:
>
> **Fact Table:**
> - Central table containing **quantitative measures** (metrics) of a business process
> - Contains **foreign keys** linking to dimension tables
> - Stores numeric, additive facts: revenue, quantity_sold, profit, cost
> - Very large — millions/billions of rows
> - Example: `Sales_Fact(date_key, product_key, store_key, customer_key, revenue, quantity, profit)`
>
> **Dimension Table:**
> - Contains **descriptive attributes** (context) for analyzing facts
> - Relatively small (thousands to millions of rows)
> - Contains textual/descriptive fields
> - Example: `Product_Dim(product_key, name, brand, category, subcategory, price)`
>
> **Schema Types:**
> - **Star Schema:** Central fact table + denormalized dimension tables (fastest queries, simplest design)
> - **Snowflake Schema:** Star schema with normalized (sub-divided) dimensions (saves space, more JOINs)
> - **Galaxy/Constellation:** Multiple fact tables sharing dimension tables
>
> **Benefits:**
> - Intuitive and easy for business users to understand
> - Fast query performance (fewer JOINs in star schema)
> - Supports standard OLAP operations (roll-up, drill-down, slice, dice)
> - Extensible — easy to add new dimensions or facts

**Key Point 2: Generalized Association Rule**

> A **generalized association rule** is an association rule that considers items at **different levels of a concept hierarchy** (taxonomy), not just at the lowest (most specific) level.
>
> **Concept Hierarchy Example:**
> ```
>                 Food
>                /    \
>           Dairy     Bakery
>          /    \     /    \
>       Milk  Cheese Bread  Cake
>       / \
>   Whole  Skim
> ```
>
> **Standard association rule (leaf level):**
> > `{Whole Milk, White Bread} → {Cheddar Cheese}` (very specific — may have low support)
>
> **Generalized association rule (higher levels):**
> > `{Dairy} → {Bakery}` (more general — higher support, broader pattern)
> > `{Milk} → {Bread}` (intermediate level)
>
> **Why Generalized?**
> > - Leaf-level rules may have **too low support** (too specific)
> > - Higher-level rules reveal **broader, more actionable patterns**
> > - Example: "Dairy products and Bakery products are often bought together" is more useful for store layout than "Amul Whole Milk 500ml and Britannia White Bread 400g are bought together"
>
> **Mining Approaches:**
> > - **Level-crossing rules:** Involve items at different hierarchy levels (e.g., `{Milk} → {White Bread}`)
> > - **Mining at multiple levels:** Mine at each level of the hierarchy and report rules above min_support/confidence thresholds

**Key Point 3: K-Means Algorithm with Example**

> **Algorithm:**
> 1. Choose K (number of clusters)
> 2. Randomly initialize K centroids
> 3. Assign each point to the nearest centroid (Euclidean distance)
> 4. Recalculate centroids (mean of cluster members)
> 5. Repeat 3-4 until convergence
>
> **Example:** Points: A(1,1), B(2,1), C(1,2), D(5,4), E(6,5), F(5,5). K=2
>
> **Iteration 1:**
> - Initial centroids: C₁=A(1,1), C₂=D(5,4)
> - Assign:
>   - A→C₁ (d=0), B→C₁ (d=1), C→C₁ (d=1), D→C₂ (d=0), E→C₂ (d=√2=1.41), F→C₂ (d=1)
> - Clusters: {A,B,C} and {D,E,F}
> - New centroids: C₁=((1+2+1)/3, (1+1+2)/3) = **(1.33, 1.33)**, C₂=((5+6+5)/3, (4+5+5)/3) = **(5.33, 4.67)**
>
> **Iteration 2:** Reassign — same clusters → **Converged!**
> - Final: Cluster 1 = {A,B,C} centered at (1.33, 1.33), Cluster 2 = {D,E,F} centered at (5.33, 4.67)

**Key Point 4: K-Medoid (PAM) vs K-Means**

> | Feature | K-Means | K-Medoid (PAM) |
> |---------|---------|---------------|
> | Center | Mean (computed, may not be actual point) | Medoid (actual data point) |
> | Outlier Robustness | Sensitive (mean pulled by outliers) | Robust (medoid not affected) |
> | Objective | Minimize sum of squared Euclidean distances | Minimize sum of absolute distances |
> | Speed | Fast O(nKt) | Slow O(K(n-K)²) |
> | Data Types | Numerical only (needs mean) | Any data type (needs only distance) |

**Key Point 5: KNN (K-Nearest Neighbors) Algorithm**

> **KNN** is a **lazy, instance-based, supervised classification** algorithm. It does NOT build a model during training — instead, it stores all training data and classifies new instances at prediction time by finding the K closest training examples.
>
> **Algorithm:**
> 1. Store all training data (no training phase — "lazy learner")
> 2. For a new query point Q:
>    a. Calculate the distance from Q to **every training point** (Euclidean, Manhattan, etc.)
>    b. Select the **K nearest neighbors** (K closest points)
>    c. Assign Q the **majority class** among these K neighbors (voting)
> 3. For regression: assign the **average value** of K neighbors
>
> **Choosing K:**
> - **Small K (e.g., 1):** Sensitive to noise — single outlier can cause misclassification
> - **Large K:** Smoother boundaries but may include points from other classes
> - **Rule of thumb:** K = √n (where n = number of training samples); choose odd K to avoid ties
>
> **Example:** Classify point Q(3,3) using K=3.
>
> Training data:
> | Point | x | y | Class |
> |-------|---|---|-------|
> | P1 | 1 | 1 | A |
> | P2 | 2 | 2 | A |
> | P3 | 4 | 4 | B |
> | P4 | 5 | 5 | B |
> | P5 | 3 | 2 | A |
>
> Distances from Q(3,3):
> - d(Q,P1) = √[(3-1)²+(3-1)²] = √8 = 2.83
> - d(Q,P2) = √[(3-2)²+(3-2)²] = √2 = 1.41
> - d(Q,P3) = √[(3-4)²+(3-4)²] = √2 = 1.41
> - d(Q,P4) = √[(3-5)²+(3-5)²] = √8 = 2.83
> - d(Q,P5) = √[(3-3)²+(3-2)²] = 1
>
> K=3 nearest: **P5 (A, d=1), P2 (A, d=1.41), P3 (B, d=1.41)**
> Majority vote: **A=2, B=1 → Q is classified as Class A** ✓

---

#### 📊 Diagram / Table (if applicable)

```
  DIMENSIONAL MODEL (STAR SCHEMA):
            ┌──────────┐
            │   TIME   │
            │ Dimension│
            │date,month│
            │quarter,yr│
            └────┬─────┘
                 │
  ┌──────────┐  │  ┌──────────┐
  │ PRODUCT  │──┼──│ CUSTOMER │
  │name,brand│  │  │name,city │
  │category  │  │  │segment   │
  └──────────┘  │  └──────────┘
         ┌──────┴──────┐
         │  SALES FACT │
         │revenue, qty │
         │profit, cost │
         └──────┬──────┘
                │
            ┌───┴──────┐
            │  STORE   │
            │city,state│
            └──────────┘

  CONCEPT HIERARCHY (Generalized Association):
                Food
               /    \
          Dairy     Bakery
         /    \     /    \
      Milk  Cheese Bread  Cake

  Rule at leaf:    {Whole Milk} → {White Bread}  (support: 2%)
  Generalized:     {Dairy} → {Bakery}            (support: 25%)
  ↑ More general = higher support = more useful!

  KNN CLASSIFICATION (K=3):

     5 │            B(P4)
     4 │         B(P3)
     3 │       ?Q ← Classify this
     2 │    A(P2)  A(P5)
     1 │ A(P1)
     0 └──────────────→
       0  1  2  3  4  5

  K=3 nearest to Q: P5(A), P2(A), P3(B)
  Vote: A=2, B=1 → Q = Class A ✓
```

---

#### 💡 Example

> **Dimensional Modeling:** Flipkart's data warehouse uses a star schema:
> - **Fact:** `Order_Fact(order_date_key, product_key, seller_key, customer_key, order_amount, discount, delivery_days)`
> - **Dimensions:** Time, Product (name, brand, category), Customer (city, segment), Seller (name, rating)
> - **Query:** "Average delivery time by product category for Premium customers in Q1 2024" → single SQL query joining fact with Product + Customer + Time dimensions
>
> **Generalized Rule:** In a grocery store, mining at the item level finds `{Amul Toned Milk 500ml} → {Britannia Brown Bread}` with support 0.5% (too low to act on). Mining at the category level finds `{Dairy} → {Bakery}` with support 28% — now the store can place the Dairy and Bakery sections adjacent to each other.
>
> **KNN Real-world:** A hospital classifies a new patient's disease risk. Features: age=45, BP=140, cholesterol=250. KNN (K=5) finds the 5 most similar patients from historical records → 4 had heart disease, 1 didn't → predicts **heart disease (80% vote)**. No complex model needed — just similarity to past cases.

---

#### 🔚 Conclusion

> Dimensional modeling organizes data warehouses into intuitive fact + dimension tables for fast analytical queries using star/snowflake schemas. Generalized association rules mine patterns at multiple hierarchy levels, discovering broader and more actionable insights than leaf-level rules. K-Means/K-Medoid clustering partitions data into K groups using centroids/medoids respectively. KNN classifies new instances by majority vote of K nearest neighbors — a simple, powerful, lazy learning algorithm. Together, these techniques form the analytical foundation of modern data warehousing and mining.

---
---

## ❓ Question 7: Briefly describe the following approaches of clustering: i) Partitioning methods ii) Hierarchical methods iii) Density-based methods iv) Grid-based methods

### ✅ Answer:

#### 📖 Definition / Introduction

Clustering methods can be broadly categorized into four major approaches based on how they define and discover clusters. **Partitioning methods** divide data into K flat groups; **hierarchical methods** build tree-structured nested clusters; **density-based methods** define clusters as dense regions separated by sparse areas; and **grid-based methods** quantize space into cells and perform clustering on the grid structure.

---

#### 📝 Detailed Explanation

**i) Partitioning Methods**

> - **Principle:** Divide n objects into **K non-overlapping clusters** where K is specified by the user
> - **Objective:** Minimize an objective function — typically the **total within-cluster dissimilarity** (sum of distances from each point to its cluster center)
> - **Process:** Start with initial partition → iteratively reassign objects and update centers → converge
> - **Cluster Shape:** Typically assumes **spherical** clusters of similar size
>
> **Key Algorithms:**
> | Algorithm | Center Type | Strengths | Weaknesses |
> |-----------|------------|-----------|------------|
> | **K-Means** | Mean (centroid) | Fast O(nKt), simple | Sensitive to outliers; spherical only |
> | **K-Medoids (PAM)** | Actual point (medoid) | Robust to outliers | Slow O(K(n-K)²) |
> | **CLARA** | Medoid (sampled) | Scalable | Sample-dependent quality |
> | **CLARANS** | Medoid (randomized) | Better than CLARA | May find local optima |
>
> **Best for:** Well-separated, spherical, roughly equal-sized clusters in large datasets

**ii) Hierarchical Methods**

> - **Principle:** Create a **hierarchy of clusters** represented as a **dendrogram** (tree)
> - **Two Approaches:**
>   - **Agglomerative (Bottom-Up):** Start with n individual clusters → merge closest pair → repeat until 1 cluster
>   - **Divisive (Top-Down):** Start with 1 cluster → split most dissimilar cluster → repeat until n clusters
> - **No need to specify K** in advance — cut the dendrogram at desired height to get K clusters
>
> **Linkage Methods (Inter-cluster Distance):**
> | Linkage | Definition | Tendency |
> |---------|-----------|----------|
> | **Single** | Min distance between any two cross-cluster points | Chains (elongated clusters) |
> | **Complete** | Max distance between any two cross-cluster points | Compact, spherical clusters |
> | **Average** | Mean distance of all cross-cluster point pairs | Balanced |
> | **Ward's** | Minimize increase in total variance | Compact, equal-sized clusters |
>
> **Key Algorithms:**
> - **AGNES:** Agglomerative nesting
> - **DIANA:** Divisive analysis
> - **BIRCH:** Balanced Iterative Reducing and Clustering using Hierarchies (scalable)
> - **CURE:** Clustering Using Representatives (handles arbitrary shapes + outliers)
>
> **Best for:** Small-medium datasets; exploratory analysis; when K is unknown; discovering hierarchy

**iii) Density-Based Methods**

> - **Principle:** A cluster is a **dense region of points** separated from other clusters by **sparse regions** (low-density areas)
> - **Key Advantage:** Can discover clusters of **arbitrary shape** (not just spherical) and naturally handles noise/outliers (points in sparse regions are labeled as noise)
> - **No need to specify K** — number of clusters is determined automatically by density
>
> **Key Concepts (DBSCAN):**
> - **ε (epsilon):** Maximum radius of a neighborhood
> - **MinPts:** Minimum number of points required within ε to form a dense region
> - **Core Point:** Has ≥ MinPts within ε-radius → inside a cluster
> - **Border Point:** Within ε of a core point but has < MinPts in its own ε-neighborhood
> - **Noise Point:** Neither core nor border → outlier
>
> **Key Algorithms:**
> | Algorithm | Description |
> |-----------|-------------|
> | **DBSCAN** | Density-Based Spatial Clustering of Applications with Noise — most popular density method |
> | **OPTICS** | Ordering Points To Identify Clustering Structure — handles varying density |
> | **DENCLUE** | Density-based clustering using density distribution functions |
>
> **Best for:** Datasets with arbitrary shapes, noise, outliers; spatial data; when K is unknown

**iv) Grid-Based Methods**

> - **Principle:** Quantize the data space into a **finite number of cells** (grid structure), then perform clustering on the **grid cells** rather than individual data points
> - **Key Advantage:** **Very fast** — processing time depends on grid resolution, NOT the number of data points → O(grid_cells) not O(n)
>
> **Process:**
> 1. Define a grid structure over the data space (divide each dimension into intervals)
> 2. Count the number of data points in each cell
> 3. Identify **dense cells** (cells with count ≥ threshold)
> 4. Merge adjacent dense cells to form clusters
> 5. Points in sparse cells → noise
>
> **Key Algorithms:**
> | Algorithm | Description |
> |-----------|-------------|
> | **STING** | Statistical Information Grid — hierarchical grid with statistical summaries at each cell |
> | **WaveCluster** | Uses wavelet transformation on the grid to identify dense regions at multiple scales |
> | **CLIQUE** | Subspace clustering using grid-based density (for high-dimensional data) |
>
> **Best for:** Very large datasets where speed is critical; spatial data mining

---

#### 📊 Diagram / Table (if applicable)

```
  FOUR CLUSTERING APPROACHES — VISUAL COMPARISON:

  1. PARTITIONING (K-Means):       2. HIERARCHICAL:
  ┌─ ─ ─ ─┐ ┌─ ─ ─ ─┐              Dendrogram:
  │ ● ●   │ │ ▲ ▲   │                ┌──────┐
  │  ● ●  │ │  ▲ ▲  │              ┌─┤      ├─┐
  │   ★   │ │   ★   │             ┌┤ │      │ ├┐
  └─ ─ ─ ─┘ └─ ─ ─ ─┘             AB  C     D EF
  (K flat clusters,               (Tree of nested clusters,
   ★=centroid)                     cut at any level for K)

  3. DENSITY-BASED (DBSCAN):       4. GRID-BASED (STING):
   ●●●●                           ┌──┬──┬──┬──┐
  ● ● ● ●    ▲▲▲▲                 │  │  │░░│  │
   ●●●●●    ▲ ▲ ▲ ▲               │  │░░│░░│  │
              ▲▲▲▲                 │  │░░│░░│  │
        ×  ×                       │  │  │  │  │
  (Arbitrary shapes OK!            └──┴──┴──┴──┘
   × = noise/outliers)             ░ = dense cells → cluster
                                   (Grid cells, not points)

  DBSCAN CONCEPTS:
           ε                      ● = Core point (≥MinPts in ε)
        ┌──●──┐                   ○ = Border point (in ε of core)
        │●  ● │                   × = Noise point (isolated)
        │  ●  │  ← ε-neighborhood
        │ ●  ●│   has 6 points
        └─────┘   (≥MinPts=5) → CORE

  COMPARISON TABLE:
  ┌───────────────┬──────────┬──────────┬──────────┬──────────┐
  │ Feature       │Partition │Hierarch. │Density   │Grid      │
  ├───────────────┼──────────┼──────────┼──────────┼──────────┤
  │ K required?   │ Yes      │ No       │ No       │ No       │
  │ Shape         │ Spherical│ Any      │ Any      │ Any      │
  │ Noise handling│ Poor     │ Poor     │ Excellent│ Good     │
  │ Scalability   │ Good     │ Poor     │ Moderate │ Excellent│
  │ Complexity    │ O(nKt)   │ O(n²-n³)│ O(nlogn) │ O(cells) │
  │ Output        │ K groups │Dendrogram│ Clusters │ Clusters │
  │ Example algo  │ K-Means  │ AGNES    │ DBSCAN   │ STING    │
  └───────────────┴──────────┴──────────┴──────────┴──────────┘
```

---

#### 💡 Example

> **Partitioning:** Customer segmentation — K-Means with K=4 divides 1M customers into "High-Value," "Budget," "At-Risk," "New" segments based on purchase behavior.
>
> **Hierarchical:** A biologist clusters 200 species based on genetic similarity. The dendrogram reveals evolutionary relationships — cutting at height 10 gives 5 families, height 5 gives 12 genera.
>
> **Density-Based:** Urban planning — DBSCAN identifies population clusters of arbitrary shapes on a map. Dense residential areas form clusters; isolated farmhouses are labeled as noise. Unlike K-Means, DBSCAN correctly identifies the L-shaped cluster along a river and the ring-shaped cluster around a lake.
>
> **Grid-Based:** Earthquake epicenter analysis — STING overlays a grid on a geographic map, counts events per cell, and identifies seismic hotspots in seconds — even with millions of earthquake records.

---

#### 🔚 Conclusion

> The four major clustering approaches each have distinct strengths: **Partitioning** (K-Means, PAM) — fast and simple for spherical clusters; **Hierarchical** (AGNES, DIANA) — produces dendrograms, no K needed, but O(n²) complexity; **Density-based** (DBSCAN, OPTICS) — discovers arbitrary shapes and handles noise naturally; **Grid-based** (STING, WaveCluster) — extremely fast, depends on grid resolution not data size. Choosing the right method depends on dataset size, cluster shapes, noise levels, and whether K is known in advance.

---
---

## ❓ Question 8: What are the four axioms of distance metrics? Show that Manhattan distance satisfies all four? {Numerical on K-means} {Construct hierarchical tree with hierarchical clustering}

### ✅ Answer:

#### 📖 Definition / Introduction

A **distance metric** (also called a metric) is a function d(x, y) that defines the "distance" between two points in a space. For a function to qualify as a valid distance metric, it must satisfy **four axioms** (properties). The **Manhattan distance** (L1 norm / City Block distance) is a commonly used metric that computes distance as the sum of absolute differences along each dimension.

---

#### 📝 Detailed Explanation

**Key Point 1: Four Axioms of Distance Metrics**

> For a function d: X × X → ℝ to be a valid distance metric, it must satisfy:
>
> **Axiom 1: Non-Negativity**
> > `d(x, y) ≥ 0` for all x, y
> > Distance is always **non-negative**. The distance between any two points is zero or positive.
>
> **Axiom 2: Identity of Indiscernibles (Reflexivity)**
> > `d(x, y) = 0 if and only if x = y`
> > Distance is **zero only when** two points are the same point. If two points are different, distance must be positive.
>
> **Axiom 3: Symmetry**
> > `d(x, y) = d(y, x)` for all x, y
> > Distance from x to y equals distance from y to x. Direction doesn't matter.
>
> **Axiom 4: Triangle Inequality**
> > `d(x, z) ≤ d(x, y) + d(y, z)` for all x, y, z
> > The **direct distance** between two points is always ≤ the distance via an intermediate point. "The shortest path between two points is a straight line."

**Key Point 2: Manhattan Distance — Definition**

> For two points x = (x₁, x₂, ..., x_n) and y = (y₁, y₂, ..., y_n) in n-dimensional space:
>
> **d_Manhattan(x, y) = Σᵢ |xᵢ - yᵢ| = |x₁-y₁| + |x₂-y₂| + ... + |x_n-y_n|**
>
> Named "Manhattan" because it measures distance along axis-aligned paths — like navigating city blocks in Manhattan (no diagonal shortcuts).

**Key Point 3: Proof — Manhattan Distance Satisfies All Four Axioms**

> **Axiom 1: Non-Negativity** ✓
> > d(x, y) = Σ |xᵢ - yᵢ|
> > Since |xᵢ - yᵢ| ≥ 0 for all i (absolute value is always non-negative), and the sum of non-negative numbers is non-negative:
> > **d(x, y) ≥ 0** ✓
>
> **Axiom 2: Identity of Indiscernibles** ✓
> > **Forward:** If x = y, then xᵢ = yᵢ for all i, so |xᵢ - yᵢ| = 0 for all i, so d(x,y) = Σ 0 = 0 ✓
> > **Reverse:** If d(x,y) = 0, then Σ |xᵢ - yᵢ| = 0. Since each |xᵢ - yᵢ| ≥ 0, the only way their sum = 0 is if **every** |xᵢ - yᵢ| = 0, meaning xᵢ = yᵢ for all i, hence x = y ✓
>
> **Axiom 3: Symmetry** ✓
> > d(x, y) = Σ |xᵢ - yᵢ| = Σ |yᵢ - xᵢ| = d(y, x)
> > Because |a - b| = |b - a| (absolute value property) ✓
>
> **Axiom 4: Triangle Inequality** ✓
> > We need to show: d(x, z) ≤ d(x, y) + d(y, z)
> > i.e., Σ |xᵢ - zᵢ| ≤ Σ |xᵢ - yᵢ| + Σ |yᵢ - zᵢ|
> >
> > For each dimension i:
> > |xᵢ - zᵢ| = |xᵢ - yᵢ + yᵢ - zᵢ| ≤ |xᵢ - yᵢ| + |yᵢ - zᵢ| (by the standard triangle inequality for absolute values)
> >
> > Summing over all dimensions:
> > Σ |xᵢ - zᵢ| ≤ Σ (|xᵢ - yᵢ| + |yᵢ - zᵢ|) = Σ |xᵢ - yᵢ| + Σ |yᵢ - zᵢ|
> > ∴ **d(x, z) ≤ d(x, y) + d(y, z)** ✓
>
> **All four axioms satisfied → Manhattan distance is a valid metric** ✓

**Key Point 4: K-Means Numerical Example**

> **Data Points:** P1(2,10), P2(2,5), P3(8,4), P4(5,8), P5(7,5), P6(6,4), P7(1,2), P8(4,9)
> **K = 3**, Initial centroids: C₁ = P1(2,10), C₂ = P4(5,8), C₃ = P7(1,2)
>
> **Iteration 1 — Calculate Euclidean distances:**
>
> | Point | d to C₁(2,10) | d to C₂(5,8) | d to C₃(1,2) | Cluster |
> |-------|--------------|--------------|--------------|---------|
> | P1(2,10) | 0 | 3.61 | 8.06 | C₁ |
> | P2(2,5) | 5.0 | 4.24 | 3.16 | C₃ |
> | P3(8,4) | 8.49 | 5.0 | 7.28 | C₂ |
> | P4(5,8) | 3.61 | 0 | 7.21 | C₂ |
> | P5(7,5) | 7.07 | 3.61 | 6.71 | C₂ |
> | P6(6,4) | 7.21 | 4.12 | 5.39 | C₂ |
> | P7(1,2) | 8.06 | 7.21 | 0 | C₃ |
> | P8(4,9) | 2.24 | 1.41 | 7.62 | C₂ |
>
> Cluster 1: {P1(2,10)}
> Cluster 2: {P3(8,4), P4(5,8), P5(7,5), P6(6,4), P8(4,9)}
> Cluster 3: {P2(2,5), P7(1,2)}
>
> **New Centroids:**
> - C₁ = (2, 10)
> - C₂ = ((8+5+7+6+4)/5, (4+8+5+4+9)/5) = **(6, 6)**
> - C₃ = ((2+1)/2, (5+2)/2) = **(1.5, 3.5)**
>
> **Iteration 2 — Reassign with new centroids:**
>
> | Point | d to C₁(2,10) | d to C₂(6,6) | d to C₃(1.5,3.5) | Cluster |
> |-------|--------------|--------------|-------------------|---------|
> | P1(2,10) | 0 | 5.66 | 6.52 | C₁ |
> | P2(2,5) | 5.0 | 4.12 | 1.58 | C₃ |
> | P3(8,4) | 8.49 | 2.83 | 6.52 | C₂ |
> | P4(5,8) | 3.61 | 2.24 | 5.59 | C₂ |
> | P5(7,5) | 7.07 | 1.41 | 5.70 | C₂ |
> | P6(6,4) | 7.21 | 2.0 | 4.53 | C₂ |
> | P7(1,2) | 8.06 | 6.40 | 1.58 | C₃ |
> | P8(4,9) | 2.24 | 3.61 | 6.04 | C₁ |
>
> Cluster 1: {P1(2,10), P8(4,9)}
> Cluster 2: {P3(8,4), P4(5,8), P5(7,5), P6(6,4)}
> Cluster 3: {P2(2,5), P7(1,2)}
>
> **New Centroids:**
> - C₁ = ((2+4)/2, (10+9)/2) = **(3, 9.5)**
> - C₂ = ((8+5+7+6)/4, (4+8+5+4)/4) = **(6.5, 5.25)**
> - C₃ = ((2+1)/2, (5+2)/2) = **(1.5, 3.5)** (unchanged)
>
> **Iteration 3:** Reassign — same assignments → **Converged!**
>
> **Final Clusters:**
> - **Cluster 1:** {P1(2,10), P8(4,9)} — centroid (3, 9.5)
> - **Cluster 2:** {P3(8,4), P4(5,8), P5(7,5), P6(6,4)} — centroid (6.5, 5.25)
> - **Cluster 3:** {P2(2,5), P7(1,2)} — centroid (1.5, 3.5)

**Key Point 5: Hierarchical Clustering — Constructing a Dendrogram**

> **Data Points:** A(1,1), B(2,1), C(4,3), D(5,4), E(5,3)
> **Method:** Agglomerative with **Single Linkage** (minimum distance)
>
> **Step 1: Compute Distance Matrix (Euclidean)**
>
> |   | A | B | C | D | E |
> |---|---|---|---|---|---|
> | A | 0 | 1.0 | 3.61 | 5.0 | 4.47 |
> | B | 1.0 | 0 | 2.83 | 4.24 | 3.61 |
> | C | 3.61 | 2.83 | 0 | 1.41 | 1.0 |
> | D | 5.0 | 4.24 | 1.41 | 0 | 1.0 |
> | E | 4.47 | 3.61 | 1.0 | 1.0 | 0 |
>
> **Step 2: Merge closest pair**
> Minimum distance = 1.0 (multiple pairs: A-B, C-E, D-E)
> Merge **A and B** → Cluster {A,B} at height 1.0
>
> **Step 3: Update distance matrix (single linkage: min distance)**
>
> |       | {A,B} | C | D | E |
> |-------|-------|---|---|---|
> | {A,B} | 0 | min(3.61,2.83)=2.83 | min(5.0,4.24)=4.24 | min(4.47,3.61)=3.61 |
> | C | 2.83 | 0 | 1.41 | 1.0 |
> | D | 4.24 | 1.41 | 0 | 1.0 |
> | E | 3.61 | 1.0 | 1.0 | 0 |
>
> Minimum = 1.0 (C-E or D-E). Merge **C and E** at height 1.0
>
> **Step 4: Update matrix**
>
> |       | {A,B} | {C,E} | D |
> |-------|-------|-------|---|
> | {A,B} | 0 | min(2.83,3.61)=2.83 | 4.24 |
> | {C,E} | 2.83 | 0 | min(1.41,1.0)=1.0 |
> | D | 4.24 | 1.0 | 0 |
>
> Minimum = 1.0 ({C,E}-D). Merge **{C,E} and D** → {C,D,E} at height 1.0
>
> **Step 5: Update matrix**
>
> |       | {A,B} | {C,D,E} |
> |-------|-------|---------|
> | {A,B} | 0 | min(2.83,4.24)=2.83 |
> | {C,D,E} | 2.83 | 0 |
>
> Merge **{A,B} and {C,D,E}** at height 2.83 → {A,B,C,D,E}

---

#### 📊 Diagram / Table (if applicable)

```
  MANHATTAN DISTANCE EXAMPLE:

  Point P(2,3) and Q(5,7):
  d_Manhattan = |2-5| + |3-7| = 3 + 4 = 7

  On a grid:                 Euclidean path (diagonal):
  7 ─┤  ┌─────Q              7 ─┤      Q
  6 ─┤  │     ↑              6 ─┤     /
  5 ─┤  │     │ 4            5 ─┤    /  d = √(9+16) = 5
  4 ─┤  │     │              4 ─┤   /
  3 ─┤  P─────┘              3 ─┤  P
     └──┴──┴──┴──→               └──┴──┴──┴──→
     0  2  3  4  5               0  2  3  4  5
     Manhattan = 3+4 = 7         Euclidean = 5

  FOUR AXIOMS PROOF SUMMARY (Manhattan):
  ┌──────────────────────────────────────────────┐
  │ Axiom 1: d≥0  → |xᵢ-yᵢ|≥0 → sum≥0     ✓   │
  │ Axiom 2: d=0 ⟺ x=y → each |xᵢ-yᵢ|=0   ✓   │
  │ Axiom 3: d(x,y)=d(y,x) → |a-b|=|b-a|   ✓   │
  │ Axiom 4: d(x,z)≤d(x,y)+d(y,z) → |a-c|  ✓   │
  │          ≤|a-b|+|b-c| (abs value ineq.)      │
  └──────────────────────────────────────────────┘

  HIERARCHICAL CLUSTERING DENDROGRAM:

  Height
  2.83 ─┤          ┌──────────────┐
        │          │              │
  1.00 ─┤    ┌─────┤         ┌───┤
        │    │     │    ┌────┤   │
  0.00 ─┤    A     B    C    E   D
        └──────────────────────────

  Merge order:
  1. {A,B} at height 1.0
  2. {C,E} at height 1.0
  3. {C,E,D} at height 1.0
  4. {A,B,C,D,E} at height 2.83

  Cut at height 1.5 → 2 clusters: {A,B} and {C,D,E}
```

---

#### 💡 Example

> **Manhattan Distance Use Case:** In a city grid, a taxi at intersection (2nd Ave, 3rd St) needs to reach (5th Ave, 7th St). Manhattan distance = |5-2| + |7-3| = 3 + 4 = **7 blocks** — the actual driving distance (can't go diagonally through buildings). Euclidean distance would be √(9+16) = 5, but that's "as the crow flies" — not the actual taxi route.
>
> **Hierarchical Clustering Application:** A researcher has DNA samples from 5 species. Computing pairwise genetic distances and running agglomerative clustering produces a dendrogram showing evolutionary relationships. Cutting at different heights reveals different levels of taxonomic grouping — families, genera, or species.
>
> **Triangle Inequality Application:** If Manhattan d(A,B) = 5 and d(B,C) = 3, then d(A,C) ≤ 5 + 3 = 8. This constraint is used in database indexing — if we know A is far from B, we can prune searches for C without computing d(A,C) directly.

---

#### 🔚 Conclusion

> The four axioms of distance metrics — non-negativity, identity of indiscernibles, symmetry, and triangle inequality — are fundamental requirements for any valid distance function. Manhattan distance (sum of absolute differences) satisfies all four axioms, as proven through the properties of absolute values. K-Means clustering iteratively assigns points to nearest centroids and recalculates centroids until convergence. Hierarchical clustering builds a dendrogram through agglomerative merging, with the merge order and distances determined by the chosen linkage criterion. Both numerical examples demonstrate the step-by-step mechanics of these essential data mining algorithms.

---

> 💡 *All 8 questions for Module 6 (Classification & Prediction of Data Warehousing) have been covered with full numerical examples. Best of luck, Rishav! 🎓*

---

*Prepared with ❤️ for Rishav Raj | DWDM Module 6 — Classification & Prediction of Data Warehousing*
