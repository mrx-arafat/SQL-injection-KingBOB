# Lab: [SQL injection](https://portswigger.net/web-security/sql-injection) vulnerability in WHERE clause allowing retrieval of hidden data



```
https://portswigger.net/web-security/sql-injection/lab-retrieve-hidden-data
```

SQL injection vulnerability in the product category filter

```
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```

End goal: display all products both released and unreleased.
