## Perguntas:

1- Os 10 produtos que mais venderam em cada nicho com recuperação ativada.

```sql
SELECT 
    p.niche,
    COUNT(p.product_id) AS total_vendas
FROM 
    `product` AS p
JOIN 
    `sales` AS s 
ON 
    p.product_id = s.product_id
WHERE 
    p.recovery_active = 1
GROUP BY 
    p.niche, p.recovery_active
ORDER BY 
    total_vendas DESC
LIMIT 10;
```

2- Qual é a receita por nicho?

```sql
SELECT 
    p.niche,
    SUM(s.product_price) AS total_receita
FROM 
    `product` AS p
JOIN 
    `sales` AS s 
ON 
    p.product_id = s.product_id
GROUP BY 
    p.niche
ORDER BY 
    total_receita DESC;
```

3- Qual é a receita gerada por tipo de produto?

```sql
SELECT 
    p.type,
    SUM(s.product_price) AS total_receita
FROM 
    `product` AS p
JOIN 
    `sales` AS s 
ON 
    p.product_id = s.product_id
GROUP BY 
    p.type
ORDER BY 
    total_receita DESC;
```

4- Qual é o ticket médio das vendas? 

```sql
SELECT 
    ROUND(AVG(product_price), 2) AS ticket_medio
FROM 
    `sales`;
```

5- Os descontos influenciam o volume de vendas?

```sql
SELECT
    p.niche,
    SUM(CASE WHEN s.discount > 0 THEN 1 ELSE 0 END) AS vendas_com_desconto,
    SUM(CASE WHEN s.discount <= 0 THEN 1 ELSE 0 END) AS vendas_sem_desconto,
    ROUND(
        (SUM(CASE WHEN s.discount > 0 THEN 1 ELSE 0 END) / COUNT(*)) * 100, 
        2
    ) AS pct_vendas_com_desconto
FROM
    `sales` AS s
JOIN
    `product` AS p
ON 
    s.product_id = p.product_id
GROUP BY
    p.niche
ORDER BY
    pct_vendas_com_desconto DESC;
```

6- Quais nichos de produtos com maior número de cancelamentos e reembolsos?

```sql
SELECT
    p.niche,
    SUM(s.cancelled) AS total_cancelamentos,
    SUM(s.refund) AS total_reembolsos
FROM
    `product` AS p
JOIN
    `sales` AS s
ON
    p.product_id = s.product_id
GROUP BY
    p.niche
ORDER BY
    SUM(s.cancelled) + SUM(s.refund) DESC;
```

7- Total perdido pelo produtor devido a cancelamentos e reembolsos.

```sql
SELECT
    SUM(CASE 
        WHEN cancelled = 1 THEN product_price 
        ELSE 0 
    END) AS valor_perdido_cancelamentos,
    SUM(CASE 
        WHEN refund = 1 THEN product_price 
        ELSE 0 
    END) AS valor_perdido_reembolsos
FROM
    `sales`;
```

8- Qual é a receita gerada por produtor?

```sql
SELECT
    p.producer_id,
    SUM(s.product_price) AS receita_total
FROM
    `sales` AS s
JOIN
    `product` AS p
ON 
    s.product_id = p.product_id
GROUP BY
    p.producer_id
ORDER BY
    receita_total DESC;
```

9- Quais meses do ano tiveram o maior e o menor volume de vendas?

```sql
SELECT
    EXTRACT(YEAR FROM purchase_date) AS ano,
    EXTRACT(MONTH FROM purchase_date) AS mes,
    COUNT(*) AS total_vendas,
    SUM(product_price) AS total_faturamento
FROM
    `sales`
GROUP BY
    ano, mes
ORDER BY
    total_vendas DESC;
```
