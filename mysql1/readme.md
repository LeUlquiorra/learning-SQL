# MySQL

## Parte 1 - Introdução

## 1 O que é um Banco de Dados

- o Banco de Dados é um repositorio que armazena dados que podem ser recuperados
- é um arquivo como qualquer outro armazenado no dispositivo de armazenamento (disco rigído, SSD)

### 1.1 Entidades dentro do Banco de Dados

São as estruturas que organizam o armazenamento dentro do Banco:

- Tabelas
  - Chaves primária e estrangeira
  - Índices
- Esquemas
- _View_
- `join`
- _Procedures_ e funções
- _Trigger_

#### 1.1.1 Tabela

- É a entidade principal do Bando de Dados, onde os dados são armazenados, é organizada em colunas e linhas, como uma planilha. Existem várias **tabelas** dentro de um Banco de Dados.  
- Devemos fazer as definições sobre a **tabela** quando a criamos: quantos campos ela vai ter, qual o tipo de cada campo, etc.
- As colunas são os campos, ou seja, o que cada coluna da **tabela** vai armazenar. Não é possível armazenar valores de tipos diferentes do que foi definido em uma coluna. O número de campos de uma tabela é definido no momento de sua criação e é finito
- As linhas são chamadas de registros da **tabela** e o limite de registros que podem ser armazenados vai depender do dispositivo que armazena o Banco

##### 1.1.1.1 Chave primária

- Identificador único de um registro na **tabela**. Quando um campo é definido como chave primária, cada linha deverá ter um valor único neste campo.
- Não é obrigatória em uma **tabela**.
- É possível definir a chave primária como uma composição de dois campos e nessa situação a combinação destes dois campos deve ser única para cada linha.

##### 1.1.1.2 Chave estrangeira

Atráves de uma chave estrangeira podemos relacionar diferentes **tabelas** entre si dentro de um Banco.  
O campo que é a chave estrangeira possui uma referência para um registro em outra **tabela**. Isso é uma característica de Bancos relacionais.

##### 1.1.1.3 Índice

- Serve para facilitar consultas em uma **tabela** (...).
- Adiciona "marcadores" que pré-ordenam as linhas da **tabela** em função de um campo.
- Quando definimos uma chave estrangeira, o Banco automaticamente cria um índice para o registro na **tabela** apontada.

#### 1.1.2 Esquema (_Schema_)

- Agrupa **tabelas** relacionadas em um "assunto" comum. No MySQL, o termo _database_ é sinônimo.
- Tabelas de esquemas diferentes podem se relacionar normalmente, os **esquemas** servem muito mais para organização.

##### 1.1.2.1 _View_

- agrupamento de tabelas
- consulta que pega informaçẽs de mais de uma tabela (conjunto de tabelas relacionadas?)
- resultado dessa consulta é uma view (?), que é uma tabela lógica (?)

##### 1.1.2.2 `join`

Consulta que junta tabelas (relacionadas) usando um critério (campos comuns, filtros(?))

##### 1.1.2.3 _Procedures_ e funções

- SQL nao é uma linguagem bem estruturada: não possui comandos de controle de fluxo ou estruturas de repetição
- as _procedures_ permitem que sejam feitas lógicas estruturadas (`if`, `while`, etc) usando comandos SQL
- em cada SGBD existem diversas funções prontas:
  - tirar espaço em branco de registros
  - passar texto para minuscúla
  - etc
- é possível criar novas funções e usar em consultas

##### 1.1.2.4 _Trigger_

- Alertas programados para serem disparados quando algo acontecer.
  - Ex.: quando for criado um registro na tabela X, crie um registro com um valor default na tabela Y

## 2 Tipos de dados

Algum dia quem sabe...

## 3 Primeiros passos no MySQL

### 3.1 Acessando pela linha de comando

De forma geral, o comando para abrir o MySQL no terminal é:

```terminal
mysql -h localhost -u root -p
```

Mas podem existir alguns passos antes de acordo com a plataforma que você estiver usando. No Windows por exemplo, se você quiser acessá-lo logo após a instalação deve ir até o diretório `C:\Program Files\MySQL\MySQL Server 8.0\bin` onde fica o arquivo executável `mysql.exe` e rodar acima. Portanto, o procedimento seria algo como:

```terminal
> cd /
> cd Program Files\MySQL\MySQL Server 8.0\bin
C:\Program Files\MySQL\MySQL Server 8.0\bin> mysql -h localhost -u root -p
```

Para que seja possível utilizar este comando a partir de qualquer diretório é necessário (...).

Outra opção é utilizar o _shell_ fornecido pelo próprio MySQL, o "MySQL Shell".

Além disso é bom comentar que é uma boa prática criar um usuário para não ficar usando o `root`.

### 3.2. Gerenciando uma Base de Dados

#### 3.2.1 Criando

```sql
mysql> CREATE DATABASE IF NOT EXISTS db_name CHARACTER SET abc COLLATE xyz;
```

- `CHARACTER SET`:
- `COLLATE`:

#### 3.2.2 Deletando

```sql
mysql> DROP DATABASE IF EXISTS db_name;
```

#### 3.2.3 Onde ficam armazenados os arquivos referentes aos Bancos

Cada Base de Dados é representada por uma pasta de mesmo nome e dentro dessa pasta temos um arquivo de extensão `.ibd` para cada tabela. O local depende da plataforma:

- Windows: no arquivo de inicialização do MySQL `C:\ProgramData\MySQL\MySQL Server 8.0\my.ini`, encontramos esta informação armazenada na variável `datadir`, que no caso é `C:/ProgramData/MySQL/MySQL Server 8.0/Data`
- Linux:

### 3.3 Gerenciando uma tabela

#### 3.3.1 Criando

Primeiro devemos acessar a Base de Dados `sucos` e depois rodar o comando para criar a tabela de clientes dentro dessa Base:

```sql
> USE sucos;
Database changed
> CREATE TABLE clientes (
    cpf VARCHAR(11),
    nome VARCHAR(100) NOT NULL,
    endereco1 VARCHAR(150),
    endereco2 VARCHAR(150),
    bairro VARCHAR(50),
    cidade VARCHAR(50),
    estado VARCHAR(2),
    cep VARCHAR(8),
    idade SMALLINT,
    genero  VARCHAR(50),
    limite_credito FLOAT,
    volume_compra FLOAT,
    primeira_compra BIT(1),
    PRIMARY KEY (cpf)
);
```

É possível criar uma tabela sem acessar sua Base de Dados, basta passar seu nome antes do da tabela separando-os por um ponto:

```sql
CREATE TABLE sucos.vendedores (
    id int(5) AUTO_INCREMENT,
    matricula VARCHAR(5),
    nome VARCHAR(100),
    comissao FLOAT,
    PRIMARY KEY (id)
)
```

```sql
CREATE TABLE produtos (
    codigo VARCHAR(5) NOT NULL,
    nome VARCHAR(150) NOT NULL,
    embalagem VARCHAR(50) DEFAULT 'garrafa',
    tamanho VARCHAR(50) NOT NULL,
    sabor VARCHAR(50) NOT NULL,
    preco_lista FLOAT NOT NULL
)
```

e o id incrementável, a chave primária, se o campo aceita null, valor default, relacionamentos, valor único, created_at, updated_at?

#### 3.3.2 Alterando

Podemos alterar as propriedades das colunas de uma tabela: tornando uma coluna chave primária ou estrangeira, mudando o tipo da coluna, adicionando um valor padrão para uma coluna ou mesmo adicionando novas colunas.

```sql
ALTER TABLE produtos ADD PRIMARY KEY (codigo);
```

```sql
ALTER TABLE clientes ADD COLUMN (nascimento DATE);
```

#### 3.3.3 Deletando

```sql
DROP TABLE vendedores;
```

### 3.4 Gerenciando registros de uma tabela

#### 3.4.1 Inserindo registros

Inserindo um registro por vez:

```sql
INSERT INTO produtos (codigo, nome, embalagem, tamanho, sabor, preco_lista)
    VALUES ('00001', 'Light - 350 ml - Melância', 'lata', '350 ml', 'Melância', 4.56);

INSERT INTO vendedores (matricula, nome, comissao)
    VALUES ('v0001', 'Bruno Cesar', '0.3');

INSERT INTO produtos (sabor, embalagem, codigo, tamanho, nome, preco_lista)
    VALUES ('Laranja', 'garrafa', '00015', '350 ml', 'Polpa - 500 ml - Laranja', 2.64);

INSERT INTO clientes ( cpf, nome, endereco1, endereco2, bairro, cidade, estado, cep, idade, genero, limite_credito, volume_compra, primeira_compra, nascimento)
    VALUES ('12345678900', 'Bruno Cesar 2', 'Rua dos Alfeneiros n 4', '', 'Novo Mundo', 'Das Carmelitas', 'MG', '12345678', 18, 'masculino', 10000.00, 2000, 0, '2002-07-13');
```

Inserindo vários registro por vez:

```sql

```

#### 3.4.2 Alterando registros

```sql
UPDATE produtos
    SET embalagem='pet', preco_lista=3.87
    WHERE codigo='00204';
```

#### 5.4.3 Deletando registros

```sql
DELETE FROM produtos WHERE codigo='01003';
```

### 3.5 Consultando registros

#### 3.5.1 Comando de seleção

Recuperando todos os registros da tabela, apresentando todas as colunas:

```sql
SELECT * FROM produtos;
```

Recuperando apenas os cinco primeiros registros da tabela, apresentando todas as colunas:

```sql
SELECT * FROM produtos LIMIT 5;
```

Recuperando todos os registros da tabela, apresentando apenas algumas colunas:

```sql
SELECT cpf, nome, cidade, volume_compra FROM produtos;
```

Passando _alias_ para as colunas:

```sql
SELECT cpf AS 'cpf do cliente', nome AS 'nome do cliente' FROM produtos;
```

#### 3.5.2 Filtrando registros

Recuperando todos os registros de clientes da cidade de Curitiba:

```sql
SELECT * FROM clientes WHERE cidade = 'Curitiba';
```

Recuperando todos os registros de clientes com volume de compra maior que `50000.00`:

```sql
SELECT * FROM clientes WHERE volume_compra > 50000.00;
```

Recuperando todos os registros de clientes com idade de até 25 anos:

```sql
SELECT * FROM clientes WHERE idade <= 25;
```

Registros de produtos que não vem em `lata`:

```sql
SELECT * FROM produtos WHERE embalagem <> 'lata';
```

Produtos com preço de lista entre R$ 4,00 e R$ 5,00:

```sql
SELECT * FROM produtos WHERE preco_lista BETWEEN 4.00 AND 5.00;
```

Filtrando de acordo com uma data:

```sql
SELECT * FROM clientes WHERE nascimento = '2002-07-13';
SELECT * FROM clientes WHERE nascimento > '2002-07-13';
SELECT * FROM clientes WHERE nascimento <= '2002-07-13';
SELECT * FROM clientes WHERE YEAR(nascimento) = 2002;
SELECT * FROM clientes WHERE MONTH(nascimento) = 10;
```

Também podemos fazer filtros compostos ligando as condições com `AND` OU `OR`.

Usar exemplos de filtros com DELETE e UPDATE.

#### 3.5.3 Usando _aliases_

Nem sempre o nome das colunas podem ser adequados para apresentação, nesse caso é interessante usar um "apelido" para esses campos:

```sql
SELECT cpf as id, nome as cliente, cidade as localidade FROM produtos;
```
