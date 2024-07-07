# ebay-perfume-case-study
In this case study, we analyzed eBay perfume sales using the APPAA process: Ask, Prepare, Process, Analyze, and Act. The dataset included brand, type, price, availability, and location. We identified popular brands, sales trends, and price ranges, offering actionable insights to optimize inventory and marketing strategies.
# Case Study Report: Perfume E-Commerce Using Python, SQL, and Tableau

## Introduction

Embarking on this case study was like diving into the aromatic world of eBay’s perfume sales. Our journey, guided by the APPAA methodology (Ask, Prepare, Process, Analyze, Act), began with a dataset brimming with details about brands, types, prices, availability, and locations. These variables were the keys to unlocking the secrets of market trends and consumer preferences in the perfume industry.

## ASK

We started with a curiosity-driven mission: to understand the dynamics of the Perfume E-Commerce market in 2024. We sought answers to these intriguing questions:

1. What are the most popular perfume brands?
2. How does the type of perfume (e.g., Eau de Parfum vs. Eau de Toilette) influence sales?
3. What are the typical price ranges for different types of perfumes?
4. Which geographic regions have the highest sales?
5. How does availability impact the number of items sold?
6. How does the gender category (men's vs. women's) affect pricing and sales?
7. Is there a correlation between the number of items available and the number of items sold?
8. What is the impact of item location on sales and pricing?

## PREPARE

Our dataset, comprising 2000 perfume listings from eBay, was split into two CSV files—one for men’s perfumes and one for women’s. Each file held 1000 entries, offering a panoramic view of current market trends, pricing, availability, and geographical distribution in the e-commerce space.

## PROCESS

### Data Cleaning and Preparation

Using Python, we meticulously read, cleaned, and combined the datasets. Each step of this process was like refining a raw gemstone, ensuring our data was pristine and ready for analysis.

```python
import pandas as pd

# Read the CSV files
df1 = pd.read_csv("/content/drive/MyDrive/job/Project 2/perfume/ebay_mens_perfume.csv")
df2 = pd.read_csv("/content/drive/MyDrive/job/Project 2/perfume/ebay_womens_perfume.csv")

# Combine the datasets
df1['Gender'] = 'male'
df2['Gender'] = 'female'
df = pd.concat([df1, df2], ignore_index=True)

# Fill missing values
df['brand'].fillna('Unbranded', inplace=True)
df['type'].fillna('Unknown', inplace=True)
df['available'] = df['available'].fillna(df['available'].mean()).round(1)
df['sold'] = df['sold'].fillna(df['sold'].mean()).round(1)

# Convert columns to numeric
df['price'] = df['price'].replace('[\$,]', '', regex=True).astype(float)
df['available'] = df['available'].astype(float)
df['sold'] = df['sold'].astype(float)

# Standardize date formats and extract month and year
df['lastUpdated'] = pd.to_datetime(df['lastUpdated'], errors='coerce')
df['month'] = df['lastUpdated'].dt.month
df['year'] = df['lastUpdated'].dt.year

# Store the table in a database
import sqlite3
conn = sqlite3.connect("Perfume_Ecommerce.db")
if 'Index' in df.columns:
    df = df.drop('Index', axis=1)
df.to_sql('perfumes', conn, if_exists='replace')
```

## ANALYZE

### SQL Queries

Our SQL queries were the tools that uncovered the hidden gems within the data. Each query provided a piece of the puzzle:

1. **Most Popular Perfume Brands**
```sql
SELECT brand, COUNT(*) AS count
FROM perfumes
GROUP BY brand
ORDER BY count DESC
LIMIT 10;
```

2. **Type of Perfume Influence on Sales**
```sql
SELECT type, SUM(sold) AS total_sold
FROM perfumes
GROUP BY type
ORDER BY total_sold DESC;
```

3. **Typical Price Ranges for Different Types of Perfumes**
```sql
SELECT type, MIN(price) AS min_price, MAX(price) AS max_price, AVG(price) AS avg_price
FROM perfumes
GROUP BY type;
```

4. **Geographic Regions with the Highest Sales**
```sql
SELECT itemLocation, SUM(sold) AS total_sold
FROM perfumes
GROUP BY itemLocation
ORDER BY total_sold DESC
LIMIT 10;
```

5. **Impact of Availability on Number of Items Sold**
```sql
SELECT brand, available, gender, SUM(sold) AS total_sold
FROM perfumes
GROUP BY available
ORDER BY available DESC;
```

6. **Effect of Gender Category on Pricing and Sales**
```sql
SELECT gender, AVG(price) AS avg_price, SUM(sold) AS total_sold
FROM perfumes
GROUP BY gender;
```

7. **Correlation Between Number of Items Available and Number of Items Sold**
```sql
SELECT brand, available/sold AS avail_percent
FROM perfumes
GROUP BY brand
ORDER BY avail_percent DESC;
```

8. **Impact of Item Location on Sales and Pricing**
```sql
SELECT itemLocation, AVG(price) AS avg_price, SUM(sold) AS total_sold
FROM perfumes
GROUP BY itemLocation
ORDER BY total_sold DESC;
```

## VISUALIZATION

The crowning jewel of our analysis was the dynamic dashboard created using Tableau.

## ACT

### Inferences and Recommendations

**1. Most Popular Perfume Brands**
- The Perfume E-Commerce Dataset 2024 provides valuable insights into the popularity of perfume brands, highlighting Giorgio Armani as the leading brand with 72 listings, indicative of its strong market presence and consumer preference. Dolce&Gabbana follows closely with 62 listings, underscoring robust brand loyalty and appeal in the luxury fragrance segment. Yves Saint Laurent and Versace maintain competitive positions with 55 and 53 listings respectively, reflecting enduring brand prestige and consistent consumer demand. The significant presence of 52 listings for unbranded perfumes signals a distinct market segment driven by cost-conscious consumers and unique product offerings.

-To leverage these findings, brands should implement targeted marketing strategies tailored to reinforce their distinctive brand identities and resonate with consumer preferences. Strategies such as exclusive product launches and personalized customer interactions can enhance brand loyalty, particularly for Giorgio Armani and Dolce&Gabbana. Exploring the unbranded perfume market with competitively priced alternatives presents an opportunity to expand market share. Continuous market analysis and customer feedback monitoring are essential to adapt strategies and maintain competitiveness in the dynamic perfume industry landscape.

**2. Type of Perfume Influence on Sales**
-The analysis of the Perfume E-Commerce Dataset 2024 reveals significant variations in sales based on the type of perfume. Eau de Toilette emerges as the top-selling category, totaling 735,011 units sold. This dominance can be attributed to its lighter composition and affordability, appealing to a broader consumer base seeking everyday fragrances.
-In contrast, Eau de Parfum, with 276,315 units sold, commands a considerable market share, known for its higher concentration and longer-lasting scent, appealing to consumers looking for stronger fragrances and premium quality.
-The presence of 54,731 units sold under the "Unknown" category indicates a need for improved categorization or clarity in product listings to enhance consumer understanding and confidence.
-Eau de Cologne and Eau de Perfume follow with 51,536 and 22,917 units sold respectively, catering to specific preferences for lighter scents and moderate longevity.


**3. Typical Price Ranges for Different Types of Perfumes**
- Eau de Toilette: The average price for Eau de Toilette perfumes is approximately $30.96, with prices ranging from $15.89 to $60.99. This category likely appeals to consumers seeking affordable yet quality daily fragrances.
-Eau de Parfum: With an average price of $24.50 and a price range from $24.50 to $54.99, Eau de Parfum offers a slightly higher-priced alternative known for its higher concentration and longer-lasting scent.
-Aftershave: Aftershave products show a broader price range from $17.99 to $44.00, with an average price of approximately $28.59. This category caters to consumers looking for grooming products with fragrance elements.
-Unknown and Miscellaneous Categories: Some categories like "1", "3 Pc", "ASST", and others have specific price points ranging from $13.37 to $60.99, indicating a diverse range of offerings with varying consumer appeal and pricing strategies.

**4. Geographic Regions with the Highest Sales**
The Perfume E-Commerce Dataset 2024 reveals significant sales disparities across various geographic regions, highlighting key markets for perfume sales. Hackensack, New Jersey, leads with a substantial 617,390 units sold, indicating strong consumer demand likely driven by local affluence and market saturation. Dallas, Texas, follows closely with 435,567 units sold, showcasing a robust market influenced by population density and economic activity. Smaller markets like LaGrange, Georgia, also demonstrate notable interest with 18,882 units sold, suggesting localized consumer engagement and purchasing power. To capitalize on these insights, perfume retailers should tailor marketing strategies to resonate with regional preferences, optimize inventory management to meet demand fluctuations, and forge strategic partnerships to enhance distribution in high-performing regions. This approach will enable retailers to maximize sales potential and strengthen their competitive position in diverse regional markets

**5. Impact of Availability on Number of Items Sold**
The Perfume E-Commerce Dataset 2024 reveals a clear correlation between product availability and sales across various brands and genders. Brands like Calvin Klein and Polo Ralph Lauren, with ample availability of 842 and 756 units respectively targeting male consumers, demonstrate significantly higher sales volumes—1,362 units and 5,023 units, respectively. Conversely, brands such as Dior, with only 6 units available for males, achieved remarkably high sales of 11,744 units, suggesting strong brand loyalty and demand elasticity. These insights underscore the importance of strategic inventory management and demand forecasting to optimize availability and capitalize on consumer demand. Perfume retailers can enhance sales performance by maintaining optimal stock levels, leveraging availability as a marketing tool for exclusivity, and prioritizing customer satisfaction through consistent product availability and strategic marketing initiatives.

**6. Effect of Gender Category on Pricing and Sales**
The Perfume E-Commerce Dataset 2024 illustrates distinct patterns in pricing and sales between men's and women's perfumes. Women's perfumes, priced at an average of $39.89, achieved total sales of 499,484 units, indicating a strong market presence driven by affordability and consumer appeal. In contrast, men's perfumes, with an average price of $46.48, recorded higher total sales of 765,464 units, suggesting robust demand and potentially higher perceived value or quality in this segment. These findings highlight the importance of tailoring pricing strategies and product offerings to each gender's preferences and expectations. Perfume retailers can optimize sales by strategically aligning pricing with perceived value, enhancing product assortment to cater to specific demographic preferences, and developing targeted marketing campaigns that resonate with both male and female consumers. Continuous analysis of sales data and consumer insights will be essential to adapt strategies and maintain competitiveness in the dynamic perfume market.


**7. Correlation Between Number of Items Available and Number of Items Sold**
- The Perfume E-Commerce Dataset 2024 reveals varying availability-to-sales ratios across brands, highlighting important insights into inventory management and consumer demand. Brands like Ulric de Varens with a high ratio of 49.0% indicate potential overstocking or lower-than-expected demand, necessitating adjustments in inventory levels or promotional strategies. Conversely, brands such as Perry Ellis and Kenneth Cole, with ratios below 0.2%, suggest limited availability relative to high sales, indicating the need for improved supply chain efficiency or increased stock to meet demand.
-To optimize sales and inventory management, perfume retailers should focus on robust demand forecasting to align stock levels with consumer demand patterns. Strategic marketing and promotions can leverage availability data to promote products effectively and manage inventory of popular items efficiently. By maintaining balanced inventory and responding swiftly to market dynamics, retailers can enhance customer satisfaction and profitability in the competitive perfume market.


**8. Impact of Item Location on Sales and Pricing**
The Perfume E-Commerce Dataset 2024 reveals significant variations in pricing and sales performance based on item location across different regions:
-Hackensack, New Jersey, United States: Perfumes command an average price of $46.46, with substantial sales of 617,390 units, indicating a robust market with higher-priced offerings likely targeting affluent consumers.
-Dallas, Texas, United States: Shows an average price of $31.05 and sales of 435,567 units, highlighting a price-sensitive market with strong consumer demand and competitive pricing dynamics.
-LaGrange, Georgia, United States: Despite a lower average price of $6.65, records 18,882 units sold, reflecting affordability and potentially niche market appeal in a smaller regional setting.

By leveraging these insights, perfume retailers can optimize strategies, manage inventory efficiently, and improve sales performance. Continuous market analysis and customer feedback will help maintain competitiveness in the dynamic perfume industry.

## Conclusion

Our journey through the Perfume E-Commerce Dataset 2024 has revealed valuable insights into eBay’s perfume market. By answering key questions through thorough data analysis, we’ve uncovered trends and factors that can help retailers enhance their strategies and thrive in a competitive landscape.

## References

- Perfume E-Commerce Dataset 2024. Retrieved from [Kaggle](https://www.kaggle.com/datasets/kanchana1990/perfume-e-commerce-dataset-2024)
