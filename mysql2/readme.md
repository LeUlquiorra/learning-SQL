# MySQL

## Parte 2

## 1 Filtrando consultas

### 1.1 Consultas condicionais

É possível realizar consultas usando os operadores condicionais `AND` e `OR`, e o retorno vai conter os registros avaliados como verdadeiros para  a condição:

Recuperando registros que tenha saber "Manga" ou tamanho de "470 ml":

```sql
SELECT * FROM TABELA_DE_PRODUTOS WHERE SABOR = 'Manga' OR TAMANHO = '470 ml';
```

Recuperando registros que tenha saber "Manga" e também sejam do tamanho de "470 ml":

```sql
SELECT * FROM TABELA_DE_PRODUTOS WHERE SABOR = 'Manga' AND TAMANHO = '470 ml';
```

### 1.2 Fragmentos de texto com `LIKE`

Podemos executar consultas usando pedaçoes de texto como critério para filtrar os registros.

No exemplo abaixo serão recuperados todos os registros que o sabor comece com a sílaba `Ma`, no caso, todos que tenham sabor "Manga", "Maça" ou "Maracujá":

```sql
SELECT * FROM TABELA_DE_PRODUTOS WHERE SABOR LIKE 'Ma%';
```

Se colocarmos o símbulo de porcentagem dos dois lados do termo buscado, serão retornados todos os registros que o sabor tenha o termo "Ma", não necessáriamento no começo ou final. Neste caso, serão retornados registros com os sabores "Manga", "Maça", "Maracujá" ou "Cereja/Maça":

```sql
SELECT * FROM TABELA_DE_PRODUTOS WHERE SABOR LIKE '%Ma%';
```

## 2 Apresentação dos dados de uma consulta

### 2.1 Registros distintos

O comando `DISTINCT` tem a finalidade de retornar apenas registros que são totalmente distintos. Podemos por exemplo consultar os sabores existentes na tabela de produtos:

```sql
SELECT DISTINCT SABOR from TABELA_DE_PRODUTOS;
```

Ou podemos recuperar apenas os tamanhos para cada tipo de embalagem:

```sql
SELECT DISTINCT EMBALAGEM, TAMANHO FROM TABELA_DE_PRODUTOS;
```

### 2.2 Limitando número de registros apresentados

Se quisermos limitar a saída mostrando apenas os primeiros N registros, basta adicionar o comando `LIMIT` e passar o número de registros que queremos ao final da _query_:

```sql
SELECT * FROM TABELA_DE_PRODUTOS LIMIT 5;
```

E também é possível iniciar a apresentação a partir de qualquer registro retornado, basta passar primeiro o "número" da linha. A saber, a primeira linha é a de número zero e o número da linha é inclusivo. Ou seja, serão apresentados os cinco próximos registros a partir da linha 15 (décima sexta linha) contando com ela:

```sql
SELECT * FROM TABELA_DE_PRODUTOS LIMIT 15,5;
```

### 2.3 Ordenando a saída da consulta

Por padrão os registros recuperados em uma consulta são ordenados pela chave primária e de forma ascendente. Para especificar uma forma diferente de ordenação basta usar os comandos:

- `ORDER BY`: passando a coluna em função da qual a ordenação deve ser feita. A regra de ordenação usada vai depender do tipo de dado da coluna
- `ASC` ou `DESC`: é passado após a coluna e vai definir se a ordenão será do "menor para maior" ou "maior para menor"

Ordenando a tabela de produtos de acordo com o nome do produto, de forma ascendente (_default_) e descendente:

```sql
SELECT * from TABELA_DE_PRODUTOS ORDER BY NOME_DO_PRODUTO;
SELECT * from TABELA_DE_PRODUTOS ORDER BY NOME_DO_PRODUTO DESC;
```

É possível ordenar uma consulta em função de mais de uma coluna e definir diferentes critérios para cada uma delas. A ordenação será feita de acordo com a ordem passada na _query_:

```sql
SELECT * from TABELA_DE_PRODUTOS ORDER BY EMBALAGEM DESC, NOME_DO_PRODUTO ASC;
```

### 2.4 Funções de agregação

São _built in functions_ do SQL (variam para cada SGDB) que permitem realizar cálculos sobre os registros recuperados em uma _query_ e retornam uma única linha com o resultado dessa operação. Como exemplo temos:

- `SUM`: soma dos valores
- `AVG`: média aritmética dos valores
- `MIN`: menor valor
- `MAX`: maior valor
- `COUNT`: quantidade de ocorrências

#### 2.4.1 Agrupando os resultados

Ao utilizar funções de agregação, estaremos realizando uma operação matemática sobre todos os registros que forem recuperados, e se quiser-mos realizar esta operação para "grupos" de registros, devemos encontrar uma forma de agrupá-los.

Podemos agrupar registros que possuem uma coluna com valor comum usando o comando `GROUP BY` e aplicar uma função de agregação em cada um desses grupos. É uma boa prática definir um _alais_ para a função de agregação, assim o resultado da consulta será apresentado de forma mais explicativa.

No exemplo abaixo o retorna da _query_ será a soma de limite de crédito por estado:

```sql
SELECT ESTADO, SUM(LIMITE_DE_CREDITO) AS LIMITE_TOTAL FROM TABELA_DE_CLIENTES GROUP BY ESTADO;
```

Agora, o exemplo é para obter o maior preço para cada tipo de embalagem:

```sql
SELECT EMBALAGEM, MAX(PRECO_DE_LISTA) AS MAIOR_PRECO FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM;
```

Quantos produtos de cada embalagem são vendidos:

```sql
SELECT EMBALAGEM, COUNT(*) AS CONTADOR FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM;
```

É possível aplicar filtros no agrupamento, aqui por exemplo, apenas os bairros da cidade "Rio de Janeiro" serão considerados:

```sql
SELECT BAIRRO, SUM(LIMITE_DE_CREDITO) AS LIMITE FROM TABELA_DE_CLIENTES WHERE CIDADE = 'Rio de Janeiro' GROUP BY BAIRRO;
```

O agrupamento também pode ser feito para mais de um campo:

```sql
SELECT ESTADO, BAIRRO, SUM(LIMITE_DE_CREDITO) AS LIMITE FROM TABELA_DE_CLIENTES GROUP BY ESTADO, BAIRRO ORDER BY ESTADO ASC;
```

##### 2.4.1.1 Quantos itens de vendas existem (quantas vendas foram feitas) com a maior quantidade do produto '1101035'?

Venda com a maior quantidade do produto com código **1101035**:

```sql
SELECT MAX(`QUANTIDADE`) as 'MAIOR QUANTIDADE' FROM ITENS_NOTAS_FISCAIS WHERE `CODIGO_DO_PRODUTO` = '1101035';
```

Quantos itens de venda obdecem os requisitos buscados:

```sql
SELECT COUNT(*) FROM ITENS_NOTAS_FISCAIS WHERE CODIGO_DO_PRODUTO = '1101035' AND QUANTIDADE = 99;
```

#### 2.4.2 `HAVING`

Para filtrar resultados em função do valor agrupado devemos usar o comando `HAVING`, no qual passamos a condição e após o `GROUP BY`.

Na consulta abaixo agrupamos os registros em função da embalagem e apresentamos: as quantidades de produtos e os maiores e menores valores:

```sql
SELECT EMBALAGEM, COUNT(*) AS QTD, MAX(PRECO_DE_LISTA) AS MAIOR_PRECO, MIN(PRECO_DE_LISTA) AS MENOR_PRECO
FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM;
```

Na consulta seguinte filtramos apenas os grupos que a soma total do preço de lista fica abaixo de **81**. Note que o critério do filtro não precisa ser um dos critérios de agrupamento:

```sql
SELECT EMBALAGEM, COUNT(*) AS QTD, MAX(PRECO_DE_LISTA) AS MAIOR_PRECO, MIN(PRECO_DE_LISTA) AS MENOR_PRECO
FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM HAVING SUM(PRECO_DE_LISTA) <= 80;
```

Também é possível usar operadores condicionais na condição do `HAVING`:

```sql
SELECT EMBALAGEM, COUNT(*) AS QTD, MAX(PRECO_DE_LISTA) AS MAIOR_PRECO, MIN(PRECO_DE_LISTA) AS MENOR_PRECO
FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM HAVING SUM(PRECO_DE_LISTA) <= 80 AND MAX(PRECO_DE_LISTA) >= 5;
```

##### 2.4.2.1 Quais foram os clientes que fizeram mais de 1400 compras em 2015?

```sql
SELECT CPF, COUNT(*) AS COMPRAS FROM NOTAS_FISCAIS WHERE YEAR(DATA_VENDA) = 2015 GROUP BY CPF HAVING COUNT(*) > 1400;
```

#### 2.4.3 Classificando resultados

Podemos executar diferentes ações para cada um dos registros usando o comando `CASE`. Podemos por exemplo, classificar os produtos em uma escala de proços um tanto subjetiva:

```sql
SELECT NOME_DO_PRODUTO, PRECO_DE_LISTA,
CASE
   WHEN PRECO_DE_LISTA >= 12 THEN 'PRODUTO CARO'
   WHEN PRECO_DE_LISTA >= 7 AND PRECO_DE_LISTA < 12 THEN 'PRODUTO EM CONTA'
   ELSE 'PRODUTO BARATO'
END AS STATUS_PRECO
FROM TABELA_DE_PRODUTOS;
```

Ou ainda, definir o desconto aplicado em função da escala de preços anterior. Note que o tipo dos dados retornados não precisam ser iguais e que podemos ter mais de uma `CASE` na mesma _query_:

```sql
SELECT NOME_DO_PRODUTO, PRECO_DE_LISTA,
CASE
   WHEN PRECO_DE_LISTA >= 12 THEN 'PRODUTO CARO'
   WHEN PRECO_DE_LISTA >= 7 AND PRECO_DE_LISTA < 12 THEN 'PRODUTO EM CONTA'
   ELSE 'PRODUTO BARATO'
END AS STATUS_PRECO,
CASE
   WHEN PRECO_DE_LISTA >= 12 THEN PRECO_DE_LISTA * 0.9
   WHEN PRECO_DE_LISTA >= 7 AND PRECO_DE_LISTA < 12 THEN PRECO_DE_LISTA * 0.75
   ELSE 'SEM DESCONTO'
END AS PRECO_COM_DESCONTO
FROM TABELA_DE_PRODUTOS;
```

Também podemos usar o `CASE` como critério de agrupamento:

```sql
SELECT EMBALAGEM,
CASE
   WHEN PRECO_DE_LISTA >= 12 THEN 'PRODUTO CARO'
   WHEN PRECO_DE_LISTA >= 7 AND PRECO_DE_LISTA < 12 THEN 'PRODUTO EM CONTA'
   ELSE 'PRODUTO BARATO'
END AS STATUS_PRECO, AVG(PRECO_DE_LISTA) AS PRECO_MEDIO
FROM TABELA_DE_PRODUTOS
GROUP BY EMBALAGEM,
CASE
   WHEN PRECO_DE_LISTA >= 12 THEN 'PRODUTO CARO'
   WHEN PRECO_DE_LISTA >= 7 AND PRECO_DE_LISTA < 12 THEN 'PRODUTO EM CONTA'
   ELSE 'PRODUTO BARATO'
END
ORDER BY EMBALAGEM;
```

##### 2.4.3.1 Classifique os clientes de acordo com o ano de nascimento

```sql
SELECT NOME,
CASE
    WHEN YEAR(DATA_DE_NASCIMENTO) < 1990 THEN 'Velho'
    WHEN YEAR(data_de_nascimento) >= 1990 AND YEAR(DATA_DE_NASCIMENTO) <= 1995 THEN 'Jovem'
    ELSE 'Criança'
END as GRUPO_DE_IDADE
FROM TABELA_DE_CLIENTES;
```
