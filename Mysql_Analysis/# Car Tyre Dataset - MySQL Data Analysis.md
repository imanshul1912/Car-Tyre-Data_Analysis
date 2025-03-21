# Car Tyre Dataset - MySQL Data Analysis

## Project Overview

This project involves cleaning, analyzing, and deriving insights from a **Car Tyre Dataset** using MySQL. The key objectives include:

- **Data Cleaning**: Handling missing values, duplicates, and incorrect data types.
- **Exploratory Data Analysis (EDA)**: Statistical analysis and value distribution analysis.
- **Data Standardization**: Renaming columns, ensuring consistency in categorical values.
- **Performance Optimization**: Using indexing and query optimization techniques.

## Dataset Description

The dataset contains information about various car tyres, including:

- **Brand, Model, Submodel**: Details of the car.
- **Tyre Brand, Tyre Model**: Specific tyre details.
- **Type, Load Index, Size**: Specifications of the tyre.
- **Selling Price, Original Price**: Pricing information.
- **Rating**: User rating of the tyre.

---

## Steps Performed

### 1. Database & Table Setup
- The table was created with columns for car and tyre details.

```sql
USE new_project;
DROP TABLE IF EXISTS CarTyreData;

CREATE TABLE CarTyreData (
    id INT AUTO_INCREMENT PRIMARY KEY,
    Brand VARCHAR(255),
    Model VARCHAR(255),
    Submodel VARCHAR(255),
    `Tyre Brand` VARCHAR(255),
    `Serial No.` VARCHAR(50),
    Type VARCHAR(100),
    `Load Index` INT,
    Size VARCHAR(50),
    `Selling Price` DECIMAL(10,2),
    `Original Price` DECIMAL(10,2),
    Rating DECIMAL(3,2)
);
```

### 2. Data Loading (CSV File)
- The CSV file was loaded into the table using the `LOAD DATA INFILE` command.

```sql
LOAD DATA INFILE 'C:\\ProgramData\\MySQL\\MySQL Server 8.0\\Uploads\\Car_Tyres_Dataset.csv'
INTO TABLE CarTyreData
FIELDS TERMINATED BY ','  
ENCLOSED BY '"'  
LINES TERMINATED BY '\n'  
IGNORE 1 ROWS  
(`Brand`, `Model`, `Submodel`, `Tyre Brand`, `Serial No.`, `Type`, `Load Index`, `Size`, `Selling Price`, `Original Price`, `Rating`);
```

### 3. Data Validation & Integrity Checks
- Verified total row counts, schema, and secure file settings.

```sql
SHOW VARIABLES LIKE 'secure_file_priv';
SELECT COUNT(*) AS total_rows FROM CarTyreData;
DESCRIBE CarTyreData;
```

### 4. Handling Missing Values
- Checked for missing values in key columns; no missing values were found.

```sql
SELECT
    SUM(CASE WHEN `Brand` IS NULL THEN 1 ELSE 0 END) AS Brand_Null,
    SUM(CASE WHEN `Model` IS NULL THEN 1 ELSE 0 END) AS Model_Null,
    SUM(CASE WHEN `Rating` IS NULL THEN 1 ELSE 0 END) AS Rating_Null
FROM CarTyreData;
```


### 5. Handling Duplicate Records
- Duplicates were identified (based on all columns) and removed from the dataset.

```sql
SELECT *
FROM CarTyreData
WHERE (Brand, Model, Submodel, `Tyre Brand`, `Serial No.`, Type, `Load Index`, Size,
       `Selling Price`, `Original Price`, Rating)
IN (
    SELECT Brand, Model, Submodel, `Tyre Brand`, `Serial No.`, Type, `Load Index`, Size,
           `Selling Price`, `Original Price`, Rating
    FROM CarTyreData
    GROUP BY Brand, Model, Submodel, `Tyre Brand`, `Serial No.`, Type, `Load Index`, Size,
             `Selling Price`, `Original Price`, Rating
    HAVING COUNT(*) > 1
)
ORDER BY Brand, Model, `Tyre Brand`;

-- Removing Duplicates
SET SQL_SAFE_UPDATES = 0;
DELETE t1
FROM CarTyreData t1
JOIN CarTyreData t2
ON t1.Brand = t2.Brand
AND t1.Model = t2.Model
AND t1.Submodel = t2.Submodel
AND t1.`Tyre Brand` = t2.`Tyre Brand`
AND t1.`Serial No.` = t2.`Serial No.`
AND t1.Type = t2.Type
AND t1.`Load Index` = t2.`Load Index`
AND t1.Size = t2.Size
AND t1.`Selling Price` = t2.`Selling Price`
AND t1.`Original Price` = t2.`Original Price`
AND t1.Rating = t2.Rating
AND t1.id > t2.id;
SET SQL_SAFE_UPDATES = 1;
```

### 6. Statistical Analysis
- Summary statistics for numerical columns (e.g., Selling Price, Rating) were computed.

```sql
SELECT
    MIN(`Selling Price`) AS Min_SellingPrice,
    MAX(`Selling Price`) AS Max_SellingPrice,
    AVG(`Selling Price`) AS Avg_SellingPrice,
    STDDEV(`Selling Price`) AS StdDev_Selling_Price,
    MIN(`Rating`) AS Min_Rating,
    MAX(`Rating`) AS Max_Rating,
    AVG(`Rating`) AS Avg_Rating
FROM CarTyreData;
```

### 7. Handling Outliers (Selling Price)
- Outliers for selling price were identified using a 3-standard deviation rule.

```sql
SELECT *
FROM CarTyreData
WHERE `Selling Price` > (SELECT AVG(`Selling Price`) + 3 * STDDEV(`Selling Price`) FROM CarTyreData)
   OR `Selling Price` < (SELECT AVG(`Selling Price`) - 3 * STDDEV(`Selling Price`) FROM CarTyreData);
```
### 8. Standardizing Column Names
- The column `Serial No.` was renamed to `Tyre Model` for consistency.

```sql
ALTER TABLE CarTyreData
CHANGE COLUMN `Serial No.` `Tyre Model` VARCHAR(255);
```

### 9. Frequency Distribution Analysis
- Frequency distributions of key categorical columns were examined.

```sql
SELECT `Tyre Brand`, COUNT(*) AS Frequency
FROM CarTyreData
GROUP BY `Tyre Brand`
ORDER BY Frequency DESC
LIMIT 10;
```

### 10. Summary of Categorical Variables
- Unique counts for Brands, Tyre Brands, Models, Submodels, and Types were determined.

```sql
SELECT
    COUNT(DISTINCT Brand) AS Unique_Brands,
    COUNT(DISTINCT `Tyre Brand`) AS Unique_Tyre_Brands,
    COUNT(DISTINCT Model) AS Unique_Models,
    COUNT(DISTINCT Submodel) AS Unique_Submodels,
    COUNT(DISTINCT Type) AS Unique_Types
FROM CarTyreData;
```

### Key Findings & Next Steps

✅ Data is cleaned and standardized
✅ No missing values in key columns
✅ Duplicates removed
✅ Outliers identified
---

## **Analysis Tasks**

1. What is the demand for **Tube** vs. **Tubeless** tyres?
2. Who are the **major players** in the tyre market?
3. Which are the **top 5 brands** with the most tyre models?
4. Which **Maruti model** has the highest number of tyre models, and from which **tyre brand**?
5. Do **expensive tyres** have higher ratings?
6. Which **tyre model** has the **highest rating**?
7. What is the **average selling price** by tyre brand?
8. Which **tyre size** is most commonly used across different car models?
9. Which **car model and submodel** have the **maximum and minimum load index**?
10. What is the **total number of unique car models** in the dataset?
11. What is the **total number of unique tyre brands**, and how many are there?
12. What is the **total number of unique tyre models**, and how many are there?
13. Which **tyre brand** has the highest number of **tyre models**?
14. List **tyre brands and their tyre models** with a **price below ₹5,000**.
15. List **tyre brands and their tyre models** with a **price above ₹5,000**.
16. List each **unique car model and submodel** along with their tyre brand, tyre model, load index, size, original price, and rating. Also, determine the **total number of unique car models and submodels** in the dataset.

---

## Analysis Tasks and Results

### **Task 1: What is the demand for Tube vs. Tubeless tyres?**

```sql
SELECT Type, count(*) as Demand from cartyredata
group by Type
order by Demand DESC; 
```

**Result:**
- **Tubeless:** 3,932  
- **Tube:** 284

*Observation:* The vast majority of tyres in the dataset are tubeless.

---

### **Task 2: Who are the major players in the tyre market?**

```sql
select `Tyre Brand`, count(*) as Total_Model from cartyredata
Group by `Tyre Brand`
Order by Total_Model DESC;
```

**Result:** (Tyre brands and their total occurrences)
- **BridgeStone:** 593  
- **GoodYear:** 487  
- **CEAT:** 435  
- **JKTyre:** 434  
- **Apollo:** 386  
- **Yokohama:** 362  
- **Continental:** 331  
- **Falken:** 259  
- **MRF:** 259  
- **Firestone:** 167  
- **UltraMile:** 144  
- **Michelin:** 125  
- **Pirelli:** 99  
- **Hankook:** 64  
- **Kumho:** 53  
- **Maxxis:** 18

*Observation:* BridgeStone and GoodYear lead the market in terms of overall occurrences, indicating their dominance.

---

### **Task 3: Which are the top 5 brands with the most tyre models?**

```sql
select `Tyre Brand` , count(distinct `Tyre Model`) as Total_Tyre_Model from cartyredata
group by `Tyre Brand`
order by Total_Tyre_Model DESC
LIMIT 5;
```

**Result:**
- **GoodYear:** 17 models  
- **Apollo:** 14 models  
- **Yokohama:** 11 models  
- **JKTyre:** 11 models  
- **BridgeStone:** 10 models

*Observation:* GoodYear has the most diverse range of tyre models in the dataset.

---

### **Task 4: Which Maruti model has the highest number of tyre models, and from which tyre brand?**

```sql
select Model, `Tyre Brand`,count(`Tyre Model`) as total_tyre_models from cartyredata
group by Model, `Tyre Brand`
order by total_tyre_models desc
limit 1;
```

**Result:**
- **Maruti Model:** Ciaz  
- **Tyre Brand:** BridgeStone  
- **Total Tyre Models:** 98

*Observation:* The Ciaz stands out among Maruti models for its extensive range of tyre models, predominantly from BridgeStone.

---

### **Task 5: Do expensive tyres have higher ratings?**

```sql
SELECT 
    CASE 
        WHEN `Selling Price` <= 3000 THEN 'Low Price'
        WHEN `Selling Price` BETWEEN 3001 AND 7000 THEN 'Mid Price'
        ELSE 'High Price'
    END AS Price_Category,
    ROUND(AVG(Rating), 2) AS Avg_Rating
FROM CarTyreData
GROUP BY Price_Category
ORDER BY Avg_Rating DESC;
```

**Result:**
- **Mid Price:** Average Rating = 4.31  
- **Low Price:** Average Rating = 4.30  
- **High Price:** Average Rating = 4.21  

*Observation:* All price ranged tyres having almost same average ratings, suggesting that there's no correlation between ratings and price range.

---

### **Task 6: Which tyre model has the highest rating?**

```sql
select distinct `Tyre Brand`,`Tyre Model`, Rating from cartyredata
where Rating = (select max(Rating) from cartyredata);
```

**Result:**
- There are **40 unique tyre models** across various tyre brands that have the highest rating of **5.0**.

*Observation:* A significant number of tyre models across various brands achieve the top rating.

---

### **Task 7: What is the average selling price by tyre brand?**

```sql
select `Tyre Brand`, round(avg(`Selling Price`),0) as Avg_Selling_Price from cartyredata
group by `Tyre Brand`
order by Avg_Selling_Price DESC;
```

**Result:**

| **Tyre Brand** | **Avg. Selling Price (₹)** |
|----------------|----------------------------|
| Pirelli        | 7,398                      |
| Michelin       | 6,869                      |
| Maxxis         | 6,187                      |
| Yokohama       | 6,091                      |
| Continental    | 5,217                      |
| BridgeStone    | 5,154                      |
| Falken         | 5,025                      |
| Hankook        | 4,936                      |
| Kumho          | 4,642                      |
| Apollo         | 4,614                      |
| UltraMile      | 4,453                      |
| CEAT           | 4,097                      |
| GoodYear       | 3,967                      |
| MRF            | 3,703                      |
| JKTyre         | 3,542                      |
| Firestone      | 3,456                      |

*Observation:* Pirelli has the highest average selling price, suggesting a premium positioning, while Firestone's prices are on the lower end.

---

### **Task 8: Which tyre size is most commonly used across different car models?**

```sql
select Size, count(distinct Submodel) as Number_of_Submodels from cartyredata
group by Size
order by Number_of_Submodels desc;
```

**Result:**
- **Most Common Tyre Size:** 185/65 R 15  
- **Number of Models Available:** 46 submodels (highest in dataset)

*Observation:* The 185/65 R 15 size is the most prevalent, indicating a market standard.

---

### **Task 9: Which car model and submodel have the maximum and minimum load index?**

```sql
SELECT 
    MIN(`Load Index`) AS Minimum_LI, 
    MAX(`Load Index`) AS Maximum_LI 
FROM cartyredata;

SELECT Model, Submodel, `Load Index`
FROM cartyredata
WHERE `Load Index` = (SELECT MIN(`Load Index`) FROM cartyredata);

SELECT Model, Submodel, `Load Index`
FROM cartyredata
WHERE `Load Index` = (SELECT MAX(`Load Index`) FROM cartyredata);
```

**Result:**

- **Minimum Load Index (69):**  
  - Multiple entries for the **Alto** (e.g., Green LX, Green LXi (CNG), LX, LXi, Std, etc.), **Omni** (E MPI STD, LPG variants, MPI Ambulance, MPI CARGO, MPI STD), **800 (Petrol variants)**, **Alto K10** (VXi [2010 - 2014], LXi [2010 - 2014]), **Zen Estilo** (LXI Green (CNG), LXI(Petrol), VXI variants, Petrol), and **Zen (Petrol)**.
  
- **Maximum Load Index (106):**  
  - **Grand Vitara:** 2.4 AT (Petrol), 2.4 MT (Petrol), Diesel

  *Observation:* While many models operate at a load index of 69, the Grand Vitara stands out with a load index of 106.

---

### **Task 10: What is the total number of unique car models in the dataset?**

```sql
select Count(Distinct Model) as Number_of_Models from cartyredata;

select distinct Model from cartyredata;
```

**Result:**
- **Total Unique Car Models:** 34  
- **List:** Swift Dzire, Kizashi, Swift, Alto, Baleno, Wagon R, Vitara Brezza, Celerio, Omni, Eeco, Ertiga, Ciaz, 800, Alto K10, A-Star, Grand Vitara, Gypsy, Ritz, Swift Dzire 2008, SX4, Zen Estilo, Esteem, Zen, Celerio X, Dzire, Fronx, Grand Vitara 2003-2007 XI, New Swift (2018), Stingray, Swift Deca

*Observation:* The dataset contains a diverse range of 34 unique car models.

---

### **Task 11: What is the total number of unique tyre brands, and how many are there?**

```sql
select count(distinct `Tyre Brand`) from cartyredata;

select distinct `Tyre Brand` from cartyredata;

```

**Result:**
- **Total Unique Tyre Brands:** 16  
- **List:** JKTyre, CEAT, Apollo, Continental, GoodYear, Firestone, BridgeStone, Falken, MRF, UltraMile, Yokohama, Kumho, Pirelli, Michelin, Hankook, Maxxis

*Observation:* There is a healthy diversity of 16 tyre brands represented.

---

### **Task 12: What is the total number of unique tyre models, and how many are there?**

```sql
select count(distinct `Tyre Model`) from cartyredata;

select `Tyre Brand`,`Tyre Model` from cartyredata
group by `Tyre Brand`,`Tyre Model`
order by `Tyre Model`;
```

**Result:**
- **Total Unique Tyre Models:** 113  
- **List of Total Tyre Models along with their Tyre Brands:**

| **Tyre Brand**  | **Tyre Model**                     |
|-----------------|-----------------------------------|
| Yokohama       | A348                              |
| Apollo         | Ainac 4G                          |
| Apollo         | Ainac 4GS                         |
| Apollo         | Altrust                           |
| Apollo         | Amazer 3G Maxx                    |
| Apollo         | Amazer 4G                         |
| Apollo         | Amazer 4G Eco                     |
| Apollo         | Amazer 4G Life                    |
| Apollo         | Amazer XL                         |
| Apollo         | AMAZER3G                          |
| Apollo         | Apterra Cross                     |
| Apollo         | Apterra HP                        |
| Apollo         | Aspire 4G                         |
| GoodYear       | Assurance Armorgrip               |
| GoodYear       | Assurance Duraplus                |
| GoodYear       | Assurance Duraplus 2              |
| GoodYear       | Assurance Triplemax               |
| GoodYear       | Assurance Triplemax 2             |
| Falken         | Azenis PT 722                     |
| BridgeStone    | B-series B250                     |
| BridgeStone    | B-Series B290                     |
| Yokohama       | BluEarth AE50                     |
| Yokohama       | BluEarth RV2                      |
| Yokohama       | BluEarth-GT AE51                  |
| Pirelli        | Cinturato P1 Verde                |
| Pirelli        | Cinturato P4                      |
| Pirelli        | Cinturato P6                      |
| Pirelli        | Cinturato P7                      |
| Continental    | Comfort Contact CC5               |
| Continental    | Comfort Contact CC6               |
| Continental    | Conti 4X4 Contact                 |
| Continental    | ContiComfort Contact CC5          |
| Continental    | ContiMax Contact MC5              |
| CEAT           | Czar HP                           |
| CEAT           | Czar Sports                       |
| Yokohama       | DB E70B                           |
| GoodYear       | Ducaro Hi-Miler                   |
| GoodYear       | Duraplus                          |
| GoodYear       | Duraplus DP-M1                    |
| Hankook        | Dynapro HP (RA23)                 |
| GoodYear       | Eagle F1 Directional 5            |
| GoodYear       | Eagle F1 GSD3                     |
| GoodYear       | Eagle NCT5                        |
| Yokohama       | Earth-1 E400                      |
| BridgeStone    | Ecopia EP150                      |
| Falken         | EcoRun ZE914                      |
| Kumho          | Ecowing KH27                      |
| GoodYear       | Efficient Grip SUV                |
| JKTyre         | Elanzo Nxt                        |
| JKTyre         | Elanzo Touring                    |
| Michelin       | Energy XM2                        |
| GoodYear       | Excellence                        |
| Firestone      | FR500                             |
| Firestone      | FS100                             |
| CEAT           | FuelSmart                         |
| Yokohama       | Geolandar A/T G015                |
| Yokohama       | Geolandar A/T G016                |
| Yokohama       | Geolandar G92C                    |
| Yokohama       | Geolandar SUV G055                |
| GoodYear       | GPS2                              |
| GoodYear       | GT3                               |
| BridgeStone    | L607                              |
| Michelin       | Latitude Tour                     |
| Falken         | Linam R 51                        |
| Apollo         | Manchester United                 |
| Maxxis         | MAP3                              |
| CEAT           | Milage X3                         |
| CEAT           | Milaze                            |
| CEAT           | Milaze HD                         |
| Maxxis         | MS300                             |
| Hankook        | Optimo K415                       |
| Hankook        | Optimo K715                       |
| Hankook        | Optimo ME02                       |
| BridgeStone    | Potenza G3                        |
| Michelin       | Primacy 4ST                       |
| Michelin       | Primacy SUV                       |
| JKTyre         | Ranger H/T                        |
| BridgeStone    | S-Series S248                     |
| BridgeStone    | S-Series S322                     |
| Yokohama       | S.Drive AS01                      |
| Pirelli        | Scorpion Verde All Season         |
| CEAT           | SecuraDrive                       |
| Falken         | Sincera835                        |
| Falken         | Sincera845                        |
| JKTyre         | Taximaxx                          |
| JKTyre         | Tornado                           |
| BridgeStone    | Turanza ER60                      |
| BridgeStone    | Turanza T001                      |
| BridgeStone    | Turanza T005                      |
| JKTyre         | Ultima Neo                        |
| JKTyre         | Ultima NXT                        |
| JKTyre         | Ultima Sport                      |
| JKTyre         | Ultima XP                         |
| JKTyre         | Ultima XPC                        |
| Continental    | Ultra Contact UC6                 |
| Continental    | UltraContact UC6                  |
| UltraMile      | UM 551                            |
| UltraMile      | UM 787 LT                         |
| JKTyre         | UX Royale                         |
| Maxxis         | Victra I-Pro MA I-Pro             |
| MRF            | ZCC                               |
| MRF            | ZCT                               |
| Falken         | Ziex ZE914 EcoRun                 |
| MRF            | ZLO                               |
| MRF            | ZLX                               |
| MRF            | ZTX                               |
| MRF            | ZV2K                              |
| MRF            | ZVTS                              |
| MRF            | ZVTSM                             |
| MRF            | ZVTV                              |


*Observation:* The dataset contains a wide variety of tyre models across many brands, with a total of 113 unique models.

---

### **Task 13: Which tyre brand has the highest number of tyre models?**

```sql
select `Tyre Brand`, count(distinct `Tyre Model`) as Total_Tyre_Models from cartyredata
group by `Tyre Brand`
order by Total_Tyre_Models desc
limit 1;
```

**Result:**
- **GoodYear** with **17 tyre models**

*Observation:* GoodYear stands out as the most diversified tyre brand in terms of model offerings.

---

### **Task 14: List tyre brands and their tyre models with a price below ₹5,000.**

```sql
select `Tyre Brand`,`Tyre Model`, `Selling Price` as Price from cartyredata where `Selling Price` <= 5000
group by `Tyre Brand`,`Tyre Model`, `Selling Price`
order by `Selling Price` desc;
```

**Result:**
- **There are 250 tyre models** with a price below ₹5,000.  

*Observation:* A significant portion of the market is priced under ₹5,000, indicating a focus on budget-friendly options.

---

### **Task 15: List tyre brands and their tyre models with a price above ₹5,000.**

```sql
select `Tyre Brand`,`Tyre Model`,`Selling Price` as Price from cartyredata where `Selling Price` > 5000
group by `Tyre Brand`,`Tyre Model`,`Selling Price`
order by Price desc ;

```

**Result:**
- **There are 165 tyre models** with a price above ₹5,000.  

*Observation:* Premium tyre models (above ₹5,000) are less frequent but tend to come from high-end brands.

---

### **Task 16: List each unique car model and submodel along with their tyre brand, tyre model, load index, size, original price, and rating; also, determine the total number of unique car models and submodels in the dataset.**

```sql
select count(distinct Brand, Model, Submodel, `Tyre Brand`, `Tyre Model`, Type, `Load Index`, Size, `Selling Price`, `Original Price`, Rating) 
as unique_entries 
from cartyredata;
```

**Result:**
- **Unique Car Models:** 34 (as listed in Task 10)  
- **Unique Submodels:** 162  

*Observation:* The dataset is rich in both car models and submodels, reflecting a diverse range of vehicle types and configurations.

---

## Conclusion and Key Insights

1. **Market Dominance:**  
   - **Tubeless tyres** are predominant, and **BridgeStone** and **GoodYear** are major market players.
  
2. **Model Diversity:**  
   - **GoodYear** leads in model diversity, and **Ciaz** (a Maruti model) uses a wide range of tyre models, especially from BridgeStone.
  
3. **Price and Quality Relationship:**  
   - All price range tyres receive almost the same average ratings, implying a balance between cost and quality.
  
4. **Premium Segment:**  
   - Brands like **Pirelli** and **Michelin** have higher average selling prices, catering to a premium market.
  
5. **Standardization:**  
   - The 185/65 R 15 tyre size is standard across many car models, suggesting industry-wide compatibility.
  
6. **Load Index:**  
   - The majority of common car models have tyres with a load index of 69, while premium models, such as those on the Grand Vitara, use tyres with a load index of 106.
  
7. **Overall Diversity:**  
   - With 34 unique car models, 16 tyre brands, 113 unique tyre models, and 162 unique submodels, the dataset reflects a comprehensive view of the car tyre market.

---

## Final Remarks

This documentation comprehensively covers the data cleaning, EDA, and analysis tasks performed on the Car Tyre Dataset using MySQL. Each task’s result has been documented, and key insights have been derived to inform further business decisions.

---


