# Test Servier


## Partie 2 : SQL

### Table Creation
```
CREATE TABLE `project-id-tool-007016.test_servier.TRANSACTION` (
  date Date,
  client_id INT64,
  order_id  INT64,
  prop_id INT64,
  prod_price FLOAT64,
  prod_qty INT64
);
```

```
CREATE TABLE `project-id-tool-007016.test_servier.PRODUCT_NOMENCLATURE` (
  product_id INT64,
  product_type STRING,
  product_name STRING
);
```


### Data sample insertion
```
INSERT INTO `project-id-tool-007016.test_servier.TRANSACTION` VALUES ('2019-01-05',1234,999,490756,50,1), ('2019-06-01',3456,845,490756,50,2), ('2020-01-01',1234,999,490756,50,1),('2020-01-01',1234,999,389728,3.56,4),('2020-01-01',3456,845,490756,50,2),('2020-01-01',3456,845,549380,300,1),('2020-01-01',3456,845,293718,10,6);
INSERT INTO `project-id-tool-007016.test_servier.PRODUCT_NOMENCLATURE` VALUES (490756, "MEUBLE", "Chaise"),(389728, "DECO", "Boule de Noël"),(549380, "MEUBLE", "Canapé"), (293718, "DECO", "Mug");
```

### Simple SQL Query: Daily Revenue Calculation
```
SELECT date, sum(prod_price*prod_qty) AS ventes
FROM `project-id-tool-007016.test_servier.TRANSACTION`
WHERE date between  '2019-01-01' and '2019-12-31'
GROUP BY date
ORDER BY date;
```

### Complex SQL Query: Revenue Calculation by Product Type and by Customer with Results Displayed in Columns
```
SELECT client_id, MEUBLE AS ventes_meuble, DECO AS ventes_deco,
FROM (
  SELECT C.client_id, C.product_type, sum(C.prod_price*C.prod_qty) AS ventes
  FROM (
    SELECT *
    #A.client_id, B.product_type, B.product_name, 
    FROM `kmino-tangai-tool-007016.test_servier.TRANSACTION` AS A
    INNER JOIN `project-id-tool-007016.test_servier.PRODUCT_NOMENCLATURE` AS B 
    ON A.prop_id=B.product_id
    WHERE date between  '2019-01-01' and '2019-12-31'
  ) AS C 
  GROUP BY  C.client_id, C.product_type
)
PIVOT(SUM(ventes) FOR product_type IN ("MEUBLE","DECO"));
```
