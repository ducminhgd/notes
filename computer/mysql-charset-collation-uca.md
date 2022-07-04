---
type: database
subtype: mysql
---
# MySQL: Charset, Collation and UCA

This note is about sorting or filtering characters.

## Collations

Collations like utf8mb4_unicode_520_ci and utf8mb4_0900_ai_ci are based on Unicode Collation Algorithm (UCA). The number in the collation defines the UCA version:
- `0900`: UCA Version 9.0.0 http://www.unicode.org/Public/UCA/9.0.0/allkeys.txt
- `520`: UCA Version 5.2.0 http://www.unicode.org/Public/UCA/5.2.0/allkeys.txt

Case and accent sensitive:
- `ci`: case insensitive.
- `cs`: case sensitive
- `ai`: accent insensitive
- `as`: accent sensitive

## Sample snippet code

```sql
CREATE TABLE collation_ex (
   id int NOT NULL AUTO_INCREMENT,
   0900_ai_ci varchar(50) CHARACTER SET utf8mb4 
        COLLATE utf8mb4_0900_ai_ci DEFAULT NULL,
   unicode_520_ci varchar(50) CHARACTER SET utf8mb4 
        COLLATE utf8mb4_unicode_520_ci DEFAULT NULL,
   general_ci varchar(50) CHARACTER SET utf8mb4 
        COLLATE utf8mb4_general_ci DEFAULT NULL,
   PRIMARY KEY (id)
 ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci
```


```sql
SELECT 'е'='ё' collate utf8mb4_unicode_520_ci; # 1
SELECT 'е'='ё' collate utf8mb4_0900_ai_ci; # 1
SELECT 'е'='ё' collate utf8mb4_0900_as_ci; # 0
```

## Read more

1. https://lefred.be/content/mysql-character-sets-unicode-and-uca-compliant-collations/