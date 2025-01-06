## Português

### Descrição
A UnitedHealth Group (UHG) possui o programa Advocate4Me, que permite que beneficiários entrem em contato com um representante para diversas necessidades de saúde. As ligações recebidas neste call center são classificadas em categorias como:
- Sinistros e benefícios  
- Cobertura de medicamentos  
- Autorização  
- Prontuários médicos  
- Assistência de emergência  
- Serviços do portal do beneficiário  

Entretanto, algumas chamadas não se encaixam em nenhuma dessas categorias, sendo marcadas como `n/a` ou ficando como NULL no banco de dados. Sua tarefa é escrever uma consulta SQL que calcule a porcentagem de ligações que não podem ser categorizadas (ou seja, aquelas cujo `call_category = 'n/a'` ou `call_category IS NULL`).

#### Esquema  
Tabela: `callers`

| Nome da Coluna      | Tipo      |
|---------------------|-----------|
| policy_holder_id    | integer   |
| case_id             | varchar   |
| call_category       | varchar   |
| call_date           | timestamp |
| call_duration_secs  | integer   |

#### Fórmula

Porcentagem de ligações não categorizadas = (Número de ligações não categorizadas / Total de ligações) * 100


#### Requisitos
1. Contar quantas chamadas têm `call_category` como `n/a` ou NULL.  
2. Contar o número total de chamadas na tabela.  
3. Calcular a porcentagem aplicando a fórmula acima.  
4. Arredondar o resultado para uma casa decimal.  

### Exemplo
Dado o conjunto de dados:

| policy_holder_id | case_id                              | call_category         | call_date                 | call_duration_secs |
|------------------|--------------------------------------|-----------------------|---------------------------|--------------------|
| 1                | f1d012f9-9d02-4966-a968-bf6c5bc9a9fe | emergency assistance | 2023-04-13T19:16:53Z      | 144                |
| 1                | 41ce8fb6-1ddd-4f50-ac31-07bfcce6aaab | authorisation        | 2023-05-25T09:09:30Z      | 815                |
| 2                | 9b1af84b-eedb-4c21-9730-6f099cc2cc5e | n/a                  | 2023-01-26T01:21:27Z      | 992                |
| 2                | 8471a3d4-6fc7-4bb2-9fc7-4583e3638a9e | emergency assistance | 2023-03-09T10:58:54Z      | 128                |
| 2                | 38208fae-bad0-49bf-99aa-7842ba2e37bc | benefits             | 2023-06-05T07:35:43Z      | 619                |

- Total de 5 ligações  
- 1 ligação com `call_category = 'n/a'`  

A porcentagem seria:  
(1 / 5) * 100 = 20.0%



---

### Solução em SQL (Exemplo para PostgreSQL 14)

```sql
WITH uncategorised_callers AS (
    SELECT COUNT(case_id) AS uncategorised_calls
    FROM callers
    WHERE call_category IS NULL
      OR call_category = 'n/a'
)
SELECT 
    ROUND(
        (100.0 * uncategorised_calls / (SELECT COUNT(case_id) FROM callers))::numeric,
        1
    ) AS uncategorised_call_pct
FROM uncategorised_callers;
