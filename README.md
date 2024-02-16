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

Create a stored proc that can determine the market badge based on the following logic, If the total sold quantity > 5 million that market is considered Gold else it is Silver.

My input will be,

- Market
- Fiscal Year

Output

- Market badge

**SQL Query :-**

```sql
CREATE DEFINER=`root`@`localhost` PROCEDURE `get_market_badge`(
	IN in_market VARCHAR(45),
    IN in_fiscal_year YEAR,
    OUT out_badge VARCHAR(45)
)
BEGIN
	DECLARE qty INT DEFAULT 0;
    #If value not given for market assume like default market is India
    IF in_market = "" THEN
		SET in_market = "India";
	END IF;
    #Retrive total qty for given market and fiscal year
	SELECT
		SUM(s.sold_quantity) AS total_qty
	FROM fact_sales_monthly s
	JOIN dim_customer c
	ON s.customer_code = c.customer_code
	WHERE
		get_fiscal_year(s.date) = in_fiscal_year AND
        c.market = in_market
	GROUP BY c.market;

    #Determine market badge
    IF qty > 5000000 THEN
		SET out_badge = "Gold";
	ELSE
		SET out_badge = "Silver";
	END IF;
END
```

```sql
set @out_badge = '0';
call gdb0041.get_market_badge('india', 2021, @out_badge);
select @out_badge;
```

**Result :-**

| @out_badge |
| ---------- |
| Silver     |

---

**5. Create a view for gross sales. It should have the following columns.**

**Discription:-**

date, fiscal_year, customer_code, customer, market, product_code, product, variant, sold_quanity, gross_price_per_item, gross_price_total

**SQL Query :-**

```sql
CREATE
    ALGORITHM = UNDEFINED
    DEFINER = `root`@`localhost`
    SQL SECURITY DEFINER
VIEW `gross_sales` AS
    SELECT
		S.date,
		S.fiscal_year,
		S.customer_code,
		C.customer,
		C.market,
		S.product_code,
		P.product,
		P.variant,
		S.sold_quantity,
		g.gross_price AS gross_price_per_item,
		ROUND((s.sold_quantity * g.gross_price),2) AS gross_price_total
	FROM fact_sales_monthly s
	JOIN dim_customer c
		ON s.customer_code = c.customer_code
	JOIN dim_product p
		ON s.product_code = p.product_code
	JOIN fact_gross_price g
		ON s.fiscal_year = g.fiscal_year
		AND s.product_code = g.product_code

```

```sql
SELECT * FROM gdb0041.gross_sales;
```

**Result :-**

| date     | fiscal_year | customer_code | customer        | market      | product_code | product                                                       | variant  | sold_quantity | gross_price_per_item | gross_price_total |
| -------- | ----------- | ------------- | --------------- | ----------- | ------------ | ------------------------------------------------------------- | -------- | ------------- | -------------------- | ----------------- |
| 9/1/2017 | 2018        | 70002017      | Atliq Exclusive | India       | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 51            | 15.3952              | 785.16            |
| 9/1/2017 | 2018        | 70002018      | Atliq e Store   | India       | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 77            | 15.3952              | 1185.43           |
| 9/1/2017 | 2018        | 70003181      | Atliq Exclusive | Indonesia   | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 17            | 15.3952              | 261.72            |
| 9/1/2017 | 2018        | 70003182      | Atliq e Store   | Indonesia   | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 6             | 15.3952              | 92.37             |
| 9/1/2017 | 2018        | 70006157      | Atliq Exclusive | Philiphines | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 5             | 15.3952              | 76.98             |
| 9/1/2017 | 2018        | 70006158      | Atliq e Store   | Philiphines | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 7             | 15.3952              | 107.77            |
| 9/1/2017 | 2018        | 70007198      | Atliq Exclusive | South Korea | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 29            | 15.3952              | 446.46            |
| 9/1/2017 | 2018        | 70007199      | Atliq e Store   | South Korea | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 34            | 15.3952              | 523.44            |
| 9/1/2017 | 2018        | 70008169      | Atliq Exclusive | Australia   | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 22            | 15.3952              | 338.69            |
| 9/1/2017 | 2018        | 70008170      | Atliq e Store   | Australia   | A0118150101  | AQ Dracula HDD â€“ 3.5 Inch SATA 6 Gb/s 5400 RPM 256 MB Cache | Standard | 5             | 15.3952              | 76.98             |


---
