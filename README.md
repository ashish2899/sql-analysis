# SQL Sales Analysis

For many of us in the tech world, SQL (Structured Query Language) is the backbone of data management and analysis. It's a critical skill, whether you're a data analyst, software developer, business analyst, or a professional in virtually any field that relies on data.

Now, let's understand AtliQ's business model. In the industry, domain understanding is actually more important than technical skills.

That is so funny! =:joy:=

<div align="center">


| Syntax | Description |
| :-----------: | :-----------: |
| Header | Title |
| Paragraph | Text |



</div>

| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |
   ## Table 3
**Discussion option 3**
| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |

> blockquote Ashish Kamble


[title](https://www.example.com)

    ![alt text](image.jpg)

---

```sql
SELECT
    S.date,
    ROUND (SUM(g.gross_price * s.sold_quantity),2) AS gross_total_price
FROM
	fact_sales_monthly s
JOIN
	fact_gross_price g
ON
	g.product_code = s.product_code
AND
	g.fiscal_year = get_fiscal_year(s.date)
WHERE customer_code = 90002002
GROUP BY s.date
ORDER BY s.date DESC
```

---****
