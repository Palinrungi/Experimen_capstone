# RFM Analysis - Customer Segmentation Project

## **Overview Project**
Project ini melakukan analisis komprehensif customer segmentation menggunakan RFM Analysis (Recency, Frequency, Monetary) pada dataset E-Commerce untuk mengidentifikasi berbagai segmen customer dan strategi marketing yang tepat.

---

## **Struktur Project**

### **1. Dataset**
- **File**: `E-Commerce Data.csv`
- **Format**: CSV dengan separator `;`
- **Encoding**: `latin-1`
- **Size**: 541,909 records (original) → 393,915 records (after cleaning)
- **Periode**: Desember 2010 - Desember 2011

### **2. Key Features**
- `InvoiceNo`: Nomor invoice transaksi
- `StockCode`: Kode produk
- `Description`: Deskripsi produk
- `Quantity`: Jumlah produk
- `InvoiceDate`: Tanggal transaksi
- `UnitPrice`: Harga per unit
- `CustomerID`: ID customer
- `Country`: Negara customer

---

##  **Metodologi & Langkah-Langkah**

### **Phase 1: Data Preprocessing & Cleaning**

#### **1.1 Data Loading & Inspection**
```python
# Load dataset
df = pd.read_csv('../E-Commerce Data.csv', sep=';', encoding='latin-1')
```

#### **1.2 Data Cleaning Steps**
1. **Convert Data Types:**
   - `UnitPrice`: String → Float (replace comma dengan dot)
   - `InvoiceDate`: String → Datetime

2. **Remove Invalid Data:**
   - Missing CustomerID
   - Negative quantities & prices (returns/cancellations)
   - Missing product descriptions
   - Top 1% price outliers

3. **Create Derived Features:**
   - `TotalPrice = Quantity × UnitPrice`

#### **1.3 Cleaning Results**
- **Before**: 541,909 records
- **After**: 393,915 records (72.7% retained)
- **Customers**: 4,290 unique customers
- **Products**: 3,662 unique products
- **Invoices**: 18,018 unique invoices

---

### **Phase 2: Exploratory Data Analysis (EDA)**

#### **2.1 Pre-RFM EDA**
- **Dataset Overview**: Distribusi price, quantity, countries, monthly trends
- **Customer Behavior**: Orders per customer, spending distribution, top products
- **Temporal Patterns**: Daily sales patterns, seasonal trends

#### **2.2 Key Insights**
- **Average Order Value**: $20.91
- **Total Revenue**: $8,226,222.18
- **Top Country**: United Kingdom (dominan)
- **Peak Sales Day**: Thursday
- **Customer Distribution**: Mayoritas customer dengan 1-5 orders

---

### **Phase 3: RFM Analysis Implementation**

#### **3.1 RFM Metrics Calculation**
```python
# Set reference date
reference_date = df_clean['InvoiceDate'].max() + timedelta(days=1)

# Calculate RFM metrics
rfm = df_clean.groupby('CustomerID').agg({
    'InvoiceDate': lambda x: (reference_date - x.max()).days,  # Recency
    'InvoiceNo': 'nunique',                                    # Frequency  
    'TotalPrice': 'sum'                                        # Monetary
})
```

#### **3.2 RFM Scoring (1-5 Scale)**
- **Recency**: 5 = Most recent, 1 = Least recent
- **Frequency**: 5 = Most frequent, 1 = Least frequent  
- **Monetary**: 5 = Highest value, 1 = Lowest value

#### **3.3 Customer Segmentation Logic**
| Segment | Kondisi RFM | Interpretasi |
|---------|-------------|--------------|
| **Champions** | R≥4 & F≥4 & M≥4 | Customer terbaik - recent, frequent, high value |
| **Loyal Customers** | R≥3 & F≥3 & M≥3 | Customer setia yang perlu maintenance |
| **Potential Loyalists** | R≥4 & F≥2 | Recent buyers dengan potensi loyalty |
| **New Customers** | R≥4 & F=1 | Customer baru (beli recent, pertama kali) |
| **Promising** | R≥3 & F≤2 & M≤2 | Customer dengan potensi growth |
| **Need Attention** | R≥2 & F≥2 & M≥2 | Customer butuh perhatian khusus |
| **About to Sleep** | R≥2 & F≤2 | Customer hampir tidak aktif |
| **Cannot Lose Them** | R≤2 & F≥3 | High-value customer jarang beli |
| **Hibernating** | R≤2 & F=2 | Customer "tidur" |
| **Lost** | R≤2 & F=1 | Customer hilang |

---

##  **Key Results & Outputs**

### **4.1 RFM Metrics Statistics**
- **Recency**: Mean 41.1 days (0-372 days range)
- **Frequency**: Mean 4.2 orders (1-86 orders range)
- **Monetary**: Mean $1,917.96 ($3.75-$10,025.37 range)

### **4.2 Customer Segmentation Distribution**
| Segment | Count | Percentage | Avg Monetary | Avg Frequency |
|---------|-------|------------|--------------|---------------|
| **Lost** | 1,172 | 27.3% | $796.89 | 1.2 orders |
| **About to Sleep** | 1,043 | 24.3% | $1,149.44 | 2.1 orders |
| **Need Attention** | 823 | 19.2% | $2,504.28 | 4.7 orders |
| **Hibernating** | 395 | 9.2% | $1,608.19 | 2.0 orders |
| **Cannot Lose Them** | 393 | 9.2% | $4,777.17 | 8.3 orders |
| **Champions** | 319 | 7.4% | $6,781.58 | 12.2 orders |
| **Loyal Customers** | 145 | 3.4% | $4,281.87 | 8.9 orders |

### **4.3 Business Insights**
1. **51.6% Customer at Risk** (Lost + About to Sleep + Hibernating)
2. **Champions hanya 7.4%** tapi kontribusi revenue tinggi
3. **High-value segments** (Champions + Loyal + Cannot Lose) = 20% customer
4. **Opportunity segments** (Potential + Promising + New) perlu nurturing

---

## **Marketing Strategies per Segment**

| Segment | Strategy |
|---------|----------|
| **Champions** | Reward program, referral program, upselling premium |
| **Loyal Customers** | Loyalty program, personalized offers, early access |
| **Potential Loyalists** | Membership program, cross-selling, frequency incentives |
| **New Customers** | Onboarding program, first purchase discount |
| **Need Attention** | Limited time offers, reactivation campaign |
| **About to Sleep** | Win-back campaign, special discounts |
| **Cannot Lose Them** | VIP treatment, exclusive offers |
| **Hibernating** | Aggressive discounts, re-engagement |
| **Lost** | Deep discount win-back, survey exit reasons |

---

## **File Outputs**

### **1. Saved Files**
- `RFM_Analysis_Results_[timestamp].csv`: Complete RFM data with segmentation
- `RFM_Analysis_Summary.md`: This documentation file

### **2. Generated Visualizations**
- RFM metrics distributions
- Customer segment distribution (pie chart & bar chart)
- Correlation matrix heatmap
- Scatter plots (Recency vs Frequency vs Monetary)
- Customer behavior analysis charts

---

## **Data Validation & Quality Checks**

### **5.1 Consistency Verification**
- **Customer Count**: 4,290 (before) = 4,290 (after RFM)
- **Transaction Count**: 393,915 products ≠ 18,018 invoices (expected difference)
- **Frequency Logic**: RFM Frequency = Invoice sessions, bukan product count

### **5.2 Data Transformation Understanding**
- **393,915 rows** = Individual product transactions
- **4,290 rows (RFM)** = Aggregated customer-level data
- **18,018 frequency** = Total unique invoices/shopping sessions
- **Average**: 22 products per invoice session

---

##  **Technical Implementation**

### **6.1 Libraries Used**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime, timedelta
```

### **6.2 Key Functions**
- `segment_customers()`: Rule-based segmentation logic
- Data aggregation using `groupby()` and `agg()`
- Quintile-based scoring with `pd.qcut()`

### **6.3 Computational Approach**
- **Rule-Based Segmentation**: Menggunakan business logic (bukan ML clustering)
- **Quintile Scoring**: Pembagian 5 tier berdasarkan distribusi data
- **Customer-Centric**: Transformasi dari transaction-level ke customer-level

---


