# MySQL

## Parte 2

Os exemplos apresentados nesta parte das notas são baseados no Banco que é criado a partir dos _scripts_ SQL que estão na pasta `mysql2/scripts`, basta executá-los na ordem que são apresentados.

### Índice

1. [Filtrando consultas](#1-filtrando-consultas)
2. [Apresentação dos dados de uma consulta](#2-apresentação-dos-dados-de-uma-consulta)
3. [Juntando tabelas](#3-juntando-tabelas)
4. [](#4-)
5. [](#5-)

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

[↑ voltar ao topo](#mysql)

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
```[↑ voltar ao topo](#mysql)

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

[↑ voltar ao topo](#mysql)

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

##### 2.4.2.1 Clientes que fizeram mais de 1400 compras em 2015

```sql
SELECT CPF, COUNT(*) AS COMPRAS FROM NOTAS_FISCAIS WHERE YEAR(DATA_VENDA) = 2015 GROUP BY CPF HAVING COUNT(*) > 1400;
```

[↑ voltar ao topo](#mysql)

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

[↑ voltar ao topo](#mysql)

## 3 Juntando tabelas e consultas

### 3.1 Juntando tabelas

A forma mais adequada de realizar consultas em mais de uma tabela com apenas uma _query_ é USANDO o comando `JOIN`, que permite unir diferentes tabelas através de colunas que possuam o mesmo conteúdo. Existem diferentes tipos de `JOIN` e formas de "juntar" que não o utilizam:

- `INNER JOIN`: retorna apenas os registros que possuem correspondência
- `LEFT JOIN`: retorna todos os registros da tabela a esquerda e apenas os correspondêntes da tabela a direita
- `RIGHT JOIN`: retorna todos os registros da tabela a direita e apenas os correspondêntes da tabela a esquerda
- `FULL JOIN`: retorna todos os registros das duas tabelas, é o `LEFT` e `RIGHT` mesmo tempo
- `CROSS JOIN`: retorna um "produto cartesiano" de todos os registros

Vamos considerar as duas tabelas que seguem para ilustrar cada uma das formas de juntá-las em uma _query_:

Nome|ID
:-|:-:
João|1
Maria|3
PEdro|4
Claudia|5

ID|Hobby
:-:|:-
1|Praia
3|Futebol
4|Fotografia
5|Artesanato

#### 3.1.1 `INNER JOIN`

Com o `INNER JOIN` recuperamos todos os registros que possuem correspondência de acordo com a coluna comum a ambas as tabelas. No `SELECT` passamos as culunas que devem ser apresentadas, acessando-as através do _alias_ definido para cada tabela; no `FROM` passamos a tabela da esquerda com um _alias_; no `INNER JOIN` passamos a tabela da direita, também com um _alias_; e a condição de união das tabelas é passada no `ON`.

```sql
SELECT A.NOME, B.HOBBY FROM TABELA_DE_NOMES A INNER JOIN TABELA_DE_HOBBIES B ON A.ID = B.ID;
```

Nome|Hobby
:-:|:-:
João|Praia
Maria|Futebol

Alguns pontos que merecem ser mencionados são:

- não é obrigatório definir _aliases_ para as tabelas, mas se isso não for feito é necessário passar o nome completo da tabela no `SELECT`
- a necessidade desse prefixo que referência a tabela da qual o campo é oriundo, existe apenas se o nome do campo for comum para as duas tabelas. Ou seja, se quero selecionar um campo cujo nome existe em penas uma das tabelas, posso passar apenas o nome desse campo. Por questões de boas práticas e para facilitar o entendimento, é aconselhável o uso de _aliases_
- as colunas que fazem a ligação entre as duas tabelas não precisam ter o mesmo nome, apenas o conteúdo que deve ser comum.

##### 3.1.1.1 Exemplo prático: Quantidade vendida

Passando para a Base de Dados da empresa de sucos, verificando as tabelas de vendedores e notas fiscais, observamos que ambas as tabelas possuem uma coluna para armazenar a mátricula do vendedor. Com a _query_ abaixo vamos obter como resultado as duas tabelas unidas:

```sql
SELECT * FROM TABELA_DE_VENDEDORES A INNER JOIN NOTAS_FISCAIS B ON A.MATRICULA = B.MATRICULA;
```

Podemos agrupar os registros para obter quantas notas fiscais cada vendedor emitiu:

```sql
SELECT A.MATRICULA, A.NOME, COUNT(*) FROM TABELA_DE_VENDEDORES A INNER JOIN NOTAS_FISCAIS B ON A.MATRICULA = B.MATRICULA GROUP BY A.MATRICULA, A.NOME;
```

Ou encontrar qual o faturamento anual:

```sql
SELECT YEAR(DATA_VENDA), SUM(QUANTIDADE * PRECO) AS FATURAMENTO
FROM NOTAS_FISCAIS NF INNER JOIN ITENS_NOTAS_FISCAIS INF ON NF.NUMERO = INF.NUMERO
GROUP BY YEAR(DATA_VENDA);
```

[↑ voltar ao topo](#mysql)

#### 3.1.2 `LEFT JOIN`

```sql
SELECT A.NOME, B.HOBBY FROM TABELA_DE_NOMES A LEFT JOIN TABELA_DE_HOBBIES B ON A.ID = B.ID;
```

Nome|Hobby
:-:|:-:
João|Praia
Maria|Futebol
Pedro|NULL
Claudia|NULL

##### 3.1.2.1 Exemplo prático: Cliente que não compra

Existem situações onde existem registros que não possuem correspondência e podemos usar o `LEFT JOIN` para descobrir que registro é esse.

Executando as _queries_ que seguem percebemos que existem 15 clientes cadastrados, mas 14 clientes com notas emitidas.

```sql
SELECT COUNT(*) FROM TABELA_DE_CLIENTES;
SELECT CPF, COUNT(*) FROM NOTAS_FISCAIS GROUP BY CPF;
```

Se fizermos um `LEFT JOIN` abaixo, podemos facilmente identificar qual dos clientes nunca comprou nada, pois todos os registros da tabela a esquerda serão recuperados e um deles não vai ter correspondência na tabela a direita:

```sql
SELECT DISTINCT C.CPF, C.NOME, NF.CPF FROM TABELA_DE_CLIENTES C LEFT JOIN NOTAS_FISCAIS NF ON C.CPF = NF.CPF;
```

Essa informação pode ser obtida de forma mais imediata usando a condição de que o campo `CPF` deve ser nulo na tabela a direita. Assim a consulta irá retornar apenas o cliente que nunca comprou nada.

```sql
SELECT DISTINCT C.CPF, C.NOME, NF.CPF
FROM TABELA_DE_CLIENTES C LEFT JOIN NOTAS_FISCAIS NF ON C.CPF = NF.CPF
WHERE NF.CPF IS NULL;
```

#### 3.1.3 `RIGHT JOIN`

```sql
SELECT A.NOME, B.HOBBY FROM TABELA_DE_NOMES A RIGHT JOIN TABELA_DE_HOBBIES B ON A.ID = B.ID;
```

Nome|Hobby
:-:|:-:
João|Praia
Maria|Futebol
NULL|Fotografia
NULL|Artesanato

[↑ voltar ao topo](#mysql)

#### 3.1.4 `FULL JOIN`

`FULL JOIN` é o nome do comando de acordo com o padrão ANSI, mas nem todos SGBDs seguem 100% do que o padrão especifica, O MySQL por exemplo não implementa o `FULL JOIN`.

Para contornar a falta desse recurso, devem ser feitos o `LEFT JOIN` e `RIGHT JOIN` ao mesmo tempo, juntando duas consultas SQL em uma só usando o `JOIN`.

Mas a sintaxe padrão será a seguinte:

```sql
SELECT A.NOME, B.HOBBY FROM TABELA_DE_NOMES A FULL JOIN TABELA_DE_HOBBIES B ON A.ID = B.ID;
```

Nome|Hobby
:-:|:-:
João|Praia
Maria|Futebol
Pedro|NULL
Claudia|NULL
NULL|Fotografia
NULL|Artesanato

##### 3.1.4.1 Exemplo prático: Localização

Agora como exemplo vamos analizar o bairro (localização dos escritórios) dos vendedores e os bairros dos clientes. Realizando uma consulta com `INNER JOIN` primeiro, podemos descobrir todos os bairros que possuem clientes e escritórios:

```sql
SELECT V.BAIRRO, V.NOME AS VENDEDOR, DE_FERIAS, C.NOME AS CLIENTE
FROM TABELA_DE_VENDEDORES V INNER JOIN TABELA_DE_CLIENTES C ON V.BAIRRO = C.BAIRRO;
```

Com o `LEFT JOIN` podemos ver quais bairros possuem escritórios mas não clientes:

```sql
SELECT V.BAIRRO, V.NOME AS VENDEDOR, DE_FERIAS, C.NOME AS CLIENTE
FROM TABELA_DE_VENDEDORES V LEFT JOIN TABELA_DE_CLIENTES C ON V.BAIRRO = C.BAIRRO;
```

E com o `RIGHT JOIN` podemos ver quais bairros tem cliente mas não há escritórios:

```sql
SELECT V.BAIRRO, V.NOME AS VENDEDOR, DE_FERIAS, C.NOME AS CLIENTE
FROM TABELA_DE_VENDEDORES V LEFT JOIN TABELA_DE_CLIENTES C ON V.BAIRRO = C.BAIRRO;
```

Usando o `UNION`, conseguimos executar o "`FULL JOIN`" e identificar quais bairros não possuem escritório ou vendedor em apenas uma consulta:

```sql
SELECT V.BAIRRO, V.NOME AS VENDEDOR, DE_FERIAS, C.NOME AS CLIENTE
FROM TABELA_DE_VENDEDORES V LEFT JOIN TABELA_DE_CLIENTES C ON V.BAIRRO = C.BAIRRO
UNION
SELECT V.BAIRRO, V.NOME AS VENDEDOR, DE_FERIAS, C.NOME AS CLIENTE
FROM TABELA_DE_VENDEDORES V RIGHT JOIN TABELA_DE_CLIENTES C ON V.BAIRRO = C.BAIRRO;
```

#### 3.1.5 `CROSS JOIN`

```sql
SELECT A.NOME, B.HOBBY FROM TABELA_DE_NOMES A, TABELA_DE_HOBBIES B;
```

Nome|Hobby
:-:|:-:
João|Praia
Maria|Praia
Pedro|Praia
Claudia|Praia
João|Futebol
Maria|Futebol
Pedro|Futebol
Claudia|Futebol
...|...
Claudia|Artesanato

[↑ voltar ao topo](#mysql)

### 3.2 Juntando consultas

O comando `UNION` permite combinar várias _queries_ em apenas uma, trazendo os registros resultantes de cada uma delas em apenas uma consulta.

Para usar o `UNION` existe a restrição de que o número e ordem das colunas em relação ao tipo de dado devem ser compátives em todas as _queries_. Os nomes das colunas podem até ser diferentes em cada uma das _queries_, desde que coerentes na quantidade e tipos, nesse caso o resultado será apresentado com o nome das colunas passadas na primeira _query_.

Podemos identificar quais bairros possuem escritórios ou clientes de forma clara. Note que com o comando `UNION`, apenas registros distintos serão retornados:

```sql
SELECT BAIRRO FROM TABELA_DE_CLIENTES;
UNION
SELECT BAIRRO FROM TABELA_DE_VENDEDORES;
```

Para recuperar **todos** os registros devemos usar o comando `UNION ALL`:

```sql
SELECT BAIRRO FROM TABELA_DE_CLIENTES;
UNION ALL
SELECT BAIRRO FROM TABELA_DE_VENDEDORES;
```

Agora um exemplo que apresenta mais informações:

```sql
SELECT BAIRRO, NOME, 'CLIENTE' AS TIPO FROM TABELA_DE_CLIENTES
UNION
SELECT BAIRRO, NOME, 'VENDEDOR' AS TIPO FROM TABELA_DE_VENDEDORES;
```

### 3.3 Sub consultas

É quando usamos uma consulta dentro de outra consulta.

Considere a situação em que temos a `TABELA1` com as colunas `X1` e `Y1`, sendo `X1` uma coluna que os valores são compostos por apenas uma letra e `Y1` é uma coluna de valores inteiros. Além disso temos a `TABELA2` que possui apenas a coluna `Y2` que também armazena inteiros. É possível selecionar apenas os registros da `TABELA1` cujo `Y1` existe em `Y2`:

```sql
SELECT X1, Y1 FROM TABELA1 WHERE Y1 IN (SELECT Y2 FROM TABELA2);
```

Também é possível fazer agrupamentos nas sub consultas. Se agruparmos os valores de `X1` com a soma dos `Y1`, teremos usado seguinte _query_:

```sql
SELECT X1, SUM(Y1) as SOMA_DE_Y1 FROM TABELA1 GROUP BY X1;
```

Agora podemos usar essa sub consulta para obter apenas os grupos que tiveram uma soma igual a 3 para os valores `Y1`. Note que a sub consulta é tratada como uma tabela pela consulta principal. Se a _query_ tivesse apenas uma função de agregação, assim retornando um único valor, a consulta principal resultará em erro ao ser executada:

```sql
SELECT Z.X1, Z.SOMA_DE_Y1 FROM (
    SELECT X1, SUM(Y1) as SOMA_DE_Y1 FROM TABELA1 GROUP BY X1
) Z WHERE Z.SOMA_DE_Y1 = 3;
```

#### 3.3.1 Bairros com presença

Clientes dos bairros em que há escritórios:

```sql
SELECT * FROM TABELA_DE_CLIENTES WHERE BAIRRO IN (SELECT DISTINCT BAIRRO FROM TABELA_DE_VENDEDORES);
```

#### 3.3.2 Embalagens caras

Podemos identificar produtos caros de acordo com a embalagem:

```sql
SELECT X.EMBALAGEM, X.MAIOR_PRECO FROM (
    SELECT EMBALAGEM, MAX(PRECO_DE_LISTA) AS MAIOR_PRECO FROM TABELA_DE_PRODUTOS GROUP BY EMBALAGEM
) X WHERE X.MAIOR_PRECO > 10;
```

#### 3.3.3 Alternativa ao `HAVING`

Na seção **2.4.2.1** verificamos quantos clientes fizeram mais de 1400 compras em 2015, usando o comando `HAVING` no `GROUP BY`. O uso das sub consultas pode ser uma alternativa ao uso do `HAVING`.

A sub consulta pode ser um `GROUP BY` do `CPF` e em lugar do `HAVING` é feito um `WHERE` na consulta principal. No caso eu também fiz um `INNER JOIN` na tabela de clientes só para apresentar os nomes também.

```sql
SELECT X.CPF, C.NOME, X.CONTADOR FROM (
    SELECT CPF, COUNT(*) AS CONTADOR FROM NOTAS_FISCAIS
    WHERE YEAR(DATA_VENDA) = 2015
    GROUP BY CPF
) X INNER JOIN TABELA_DE_CLIENTES C ON X.CPF = C.CPF WHERE X.CONTADOR > 1400;
```

[↑ voltar ao topo](#mysql)

### 3.4 _Views_

Uma _view_ é uma tabela lógica, uma consulta pré-definida que pode ser acessada conforma haja necessidade como se fosse uma tabela. É apenas uma "visão" dos dados, que é carregada a partir dos dados originais quando for necessária e existe apenas enquanto está sendo usada.

É um recurso muito utilizado para "simplificar" _queries_ complexas, quebrando-a em _queries_ menores (?).

A sintaxe para criar uma _view_ é aprsentada a seguir e é uma boa prática utilizar prefixo `VW` antes do nome da _view_:

```sql
CREATE OR REPLACE VIEW `VW_NOME_DA_VIEW` AS query;
```

E para acessar essa _view_, basta tratá-la como uma tabela:

```sql
SELECT * FROM `VW_NOME_DA_VIEW`;
```

[↑ voltar ao topo](#mysql)
