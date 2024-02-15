# SQL Sales Analysis

For many of us in the tech world, SQL (Structured Query Language) is the backbone of data management and analysis. It's a critical skill, whether you're a data analyst, software developer, business analyst, or a professional in virtually any field that relies on data.

Now, let's understand AtliQ's business model. In the industry, domain understanding is actually more important than technical skills.

---

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

<div align="center">

| date       | gross_total_price |
| :--------- | ----------------: |
| 2021-12-01 |     $ 19537146.56 |
| 2021-10-01 |     $ 13908229.29 |
| 2021-09-01 |     $ 11192823.08 |
| 2021-08-01 |      $ 2349478.82 |
| 2021-06-01 |      $ 2288587.45 |
| 2021-05-01 |      $ 2181587.78 |
| 2021-04-01 |      $ 2253574.91 |
| 2021-02-01 |      $ 2355170.45 |
| 2021-01-01 |      $ 2303086.37 |
| 2020-12-01 |      $ 4078789.92 |

</div>

---

**2. Generate a yearly report for Croma India where there are two columns.**

**Discription:-**

- Fiscal Year

- Total Gross Sales amount in that Year from Croma

**SQL Query :-**

```sql
SELECT
    get_fiscal_year(s.date) AS Fiscal_Year,
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
GROUP BY get_fiscal_year(date)
ORDER BY fiscal_year DESC
```

**Result :-**

<div align="center">

| Fiscal Year | gross_total_price |
| ----------- | ----------------: |
| 2022        |     $ 44638198.92 |
| 2021        |     $ 23216512.22 |
| 2020        |      $ 6502181.91 |
| 2019        |      $ 3555079.02 |
| 2018        |      $ 1324097.44 |

</div>

---

**3. Created stored proc for monthly gross sales report.**

**Discription:-**

<p>

As a data analyst, I want to create a stored proc for monthly gross sales reports so that I don't have to modify the query every time manually. Stored proc can be run by other users (who have limited access to the database) and they can generate this report without having to involve the data analytics team.

</p>

The report should have the following columns,

- Month

- Total gross sales in that month from a given customer

**SQL Query :-**

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_monthly_gross_sales_for_customer`(
	in_customer_code TEXT
)
BEGIN
	SELECT
		s.date,
		ROUND(SUM(g.gross_price * s.sold_quantity),2) AS gross_monthly_sales
	FROM
		fact_sales_monthly s
	JOIN
		fact_gross_price g
	ON
		g.product_code = s.product_code
	AND
		g.fiscal_year = get_fiscal_year(s.date)
	WHERE
		FIND_IN_SET(s.customer_code, in_costomer_code)>0
	GROUP BY s.date
	ORDER BY s.date DESC;
END

```

**Result :-**

<div align="center">

| date       | gross_monthly_sales |
| ---------- | ------------------: |
| 2021-12-01 |       $ 19537146.56 |
| 2021-10-01 |       $ 13908229.29 |
| 2021-09-01 |       $ 11192823.08 |
| 2021-08-01 |        $ 2349478.82 |
| 2021-06-01 |        $ 2288587.45 |
| 2021-05-01 |        $ 2181587.78 |
| 2021-04-01 |        $ 2253574.91 |
| 2021-02-01 |        $ 2355170.45 |
| 2021-01-01 |        $ 2303086.37 |
| 2020-12-01 |        $ 4078789.92 |
| 2020-10-01 |        $ 3109316.88 |
| 2020-09-01 |        $ 2296919.63 |
| 2020-08-01 |         $ 799327.63 |
| 2020-06-01 |         $ 362545.14 |
| 2020-05-01 |         $ 145049.05 |
| 2020-04-01 |         $ 130520.92 |

</div>

---

**4. Stored proc for the market badge.**

**Discription:-**

<p style="text-align: justify">

As a data analyst, I want to create a stored proc for monthly gross sales reports so that I don't have to modify the query every time manually. Stored proc can be run by other users (who have limited access to the database) and they can generate this report without having to involve the data analytics team.

</p>

The report should have the following columns,

- Month

- Total gross sales in that month from a given customer

**SQL Query :-**

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_monthly_gross_sales_for_customer`(
	in_customer_code TEXT
)
BEGIN
	SELECT
		s.date,
		ROUND(SUM(g.gross_price * s.sold_quantity),2) AS gross_monthly_sales
	FROM
		fact_sales_monthly s
	JOIN
		fact_gross_price g
	ON
		g.product_code = s.product_code
	AND
		g.fiscal_year = get_fiscal_year(s.date)
	WHERE
		FIND_IN_SET(s.customer_code, in_costomer_code)>0
	GROUP BY s.date
	ORDER BY s.date DESC;
END

```

**Result :-**

<div align="center">

| date       | gross_monthly_sales |
| ---------- | ------------------: |
| 2021-12-01 |       $ 19537146.56 |
| 2021-10-01 |       $ 13908229.29 |
| 2021-09-01 |       $ 11192823.08 |
| 2021-08-01 |        $ 2349478.82 |
| 2021-06-01 |        $ 2288587.45 |
| 2021-05-01 |        $ 2181587.78 |
| 2021-04-01 |        $ 2253574.91 |
| 2021-02-01 |        $ 2355170.45 |
| 2021-01-01 |        $ 2303086.37 |
| 2020-12-01 |        $ 4078789.92 |
| 2020-10-01 |        $ 3109316.88 |
| 2020-09-01 |        $ 2296919.63 |
| 2020-08-01 |         $ 799327.63 |
| 2020-06-01 |         $ 362545.14 |
| 2020-05-01 |         $ 145049.05 |
| 2020-04-01 |         $ 130520.92 |

</div>

---
