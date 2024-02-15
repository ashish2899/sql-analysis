# SQL Sales Analysis

For many of us in the tech world, SQL (Structured Query Language) is the backbone of data management and analysis. It's a critical skill, whether you're a data analyst, software developer, business analyst, or a professional in virtually any field that relies on data.

Now, let's understand AtliQ's business model. In the industry, domain understanding is actually more important than technical skills.

**1. Gross monthly total sales report for Croma**

**Discription:-**


As a product owner, I need an aggregate monthly gross sales report for Croma India customers so that I can track how much sales this particular customer is generating for AtliQ and manage our relationships accordingly.

The report should have the following fields,

- Month
- Total gross sales amount to Croma India in this month

**SQL Query :-**

```sql
SELECT
    S.date,
    ROUND (SUM(g.gross_price * s.sold_quantity),2) AS gross_total_price
FROM fact_sales_monthly s
JOIN fact_gross_price g
    ON g.product_code = s.product_code
    AND g.fiscal_year = get_fiscal_year(s.date)
WHERE customer_code = 90002002
GROUP BY s.date
ORDER BY s.date DESC

```

**Result :-**

| date | gross_total_price |
| :- | -: |
| 2021-12-01 | 19537146.56
| 2021-10-01 | 13908229.29 |
| 2021-09-01 | 11192823.08 |
| 2021-08-01 | 2349478.82 |
| 2021-06-01 | 2288587.45 |
| 2021-05-01 | 2181587.78 |
| 2021-04-01 | 2253574.91 |
| 2021-02-01 | 2355170.45 |
| 2021-01-01 | 2303086.37 |
| 2020-12-01 | 4078789.92 |

---



