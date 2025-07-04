# Ciclo de Estudos SQL

Este documento contém um ciclo de estudos de SQL, abordando diversos tópicos e exemplos práticos. 

---
# CRIAÇÃO DE TABELAS

No SQL Server, criamos tabelas usando a instrução `CREATE TABLE`, definindo o nome da tabela, colunas, tipos de dados e possíveis restrições.

---

## Sintaxe base

```sql
CREATE TABLE nomeTabela (
  coluna1 tipo restricao,
  coluna2 tipo,
  coluna3 tipo,
  ...
);
```

---

## Principais restrições (`constraints`)

- `NOT NULL` – Não permite valores nulos.
- `UNIQUE` – Todos os valores da coluna devem ser únicos.
- `PRIMARY KEY` – Combina `NOT NULL` com `UNIQUE`. Define uma chave primária.
- `FOREIGN KEY` – Cria uma relação com uma chave primária de outra tabela.
- `CHECK` – Garante que os valores da coluna atendam a uma condição.
- `DEFAULT` – Define um valor padrão para a coluna, se nenhum valor for fornecido.

---

## Exemplo prático: modelo "YouTube Lite"

### Tabela: `Canal`

```sql
CREATE TABLE Canal (
  CanalId INT PRIMARY KEY,
  Nome VARCHAR(150) NOT NULL,
  ContagemInscritos INT DEFAULT 0,
  DataCriacao DATETIME NOT NULL
);
```

---

### Tabela: `Video`

```sql
CREATE TABLE Video (
  VideoId INT PRIMARY KEY,
  Nome VARCHAR(150) NOT NULL,
  Visualizacoes INT DEFAULT 0,
  Likes INT DEFAULT 0,
  Dislikes INT DEFAULT 0,
  Duracao INT NOT NULL,
  CanalId INT,
  FOREIGN KEY (CanalId) REFERENCES Canal(CanalId)
);
```

---

## Representação do relacionamento

```
+------------+                +--------------+
|   Canal    |                |    Video     |
|------------|                |--------------|
| CanalId PK |<-----------+   | VideoId PK   |
| Nome       |            |   | Nome         |
| Inscritos  |            +---| CanalId FK   |
| DataCriacao|                | ...          |
+------------+                +--------------+
```

---

Esse exemplo mostra como estruturar tabelas com **relacionamento 1:N**, onde **um canal pode ter vários vídeos**, e também como aplicar restrições que garantem a integridade e consistência dos dados.

---








# CHAVE PRIMÁRIA E CHAVE ESTRANGEIRA

---

## CHAVE PRIMÁRIA (`PRIMARY KEY`)

A `PRIMARY KEY` é uma ou mais colunas que identificam de forma única cada registro de uma tabela.

- Não pode ter valores repetidos.
- Não pode ser `NULL`.
- Cria automaticamente um índice único na coluna.

---

### Exemplo de criação de tabela com chave primária

```sql
CREATE TABLE Clientes (
  ClienteID INT PRIMARY KEY,
  Nome VARCHAR(100),
  Email VARCHAR(100)
)
```

`ClienteID` será único e obrigatório para cada cliente.

---

## CHAVE ESTRANGEIRA (`FOREIGN KEY`)

A `FOREIGN KEY` é usada para criar relacionamentos entre tabelas.

- Faz referência a uma `PRIMARY KEY` de outra tabela.
- Garante a integridade referencial.
- Impede que valores inválidos sejam inseridos.
- Permite relacionar registros com segurança entre tabelas.

---

### Exemplo prático com relacionamento

```sql
CREATE TABLE Categorias (
  CategoriaID INT PRIMARY KEY,
  NomeCategoria VARCHAR(100)
)

CREATE TABLE Produtos (
  ProdutoID INT PRIMARY KEY,
  NomeProduto VARCHAR(100),
  CategoriaID INT,
  FOREIGN KEY (CategoriaID) REFERENCES Categorias(CategoriaID)
)
```

Aqui, `CategoriaID` em `Produtos` depende da existência de um `CategoriaID` em `Categorias`.

---

### Diagrama de exemplo (visual)

```
+-----------------+            +----------------+
|     Produtos    |            |   Categorias   |
|-----------------|            |----------------|
| ProdutoID (PK)  |            |   GrupoID(PK)  |        
| NomeProduto     |            | CategoriaID(FK)|
| CategoriaID (FK)|----------->|  NomeCategoria |
+-----------------+            +----------------+
```

---

## Dicas rápidas

- Uma tabela só pode ter uma chave primária, mas pode ter várias chaves estrangeiras.
- Sempre verifique se o tipo de dado das colunas que se relacionam é o mesmo.
- Ao usar `FOREIGN KEY`, é possível também definir ações em caso de deleção ou atualização, como `ON DELETE CASCADE`.

---

Esse tipo de estrutura ajuda a organizar seu banco com integridade e relações bem definidas.

---


# TIPOS DE DADOS NO SQL SERVER

O SQL Server oferece diferentes tipos de dados para representar corretamente informações como texto, números, datas e valores lógicos. A escolha correta do tipo de dado garante economia de espaço, performance e integridade.

---

## 1. Boleanos

O tipo `BIT` é usado para representar valores lógicos:

- Pode armazenar `0` (falso), `1` (verdadeiro) ou `NULL` (desconhecido).
- Por padrão, se não especificado, será `NULL`.

```sql
CREATE TABLE Exemplo (
  Ativo BIT
)
```

---

## 2. Caracteres (Strings)

### Tamanho fixo

- `CHAR(n)` – armazena **sempre o número fixo de caracteres** definidos, completando com espaços se necessário.

```sql
CHAR(10) -- sempre ocupa 10 caracteres, mesmo se usar 3
```

### Tamanho variável

- `VARCHAR(n)` – armazena até `n` caracteres, **ocupando apenas o espaço utilizado**.
- `NVARCHAR(n)` – igual ao `VARCHAR`, mas aceita **caracteres Unicode** (como emojis e acentos internacionais).

```sql
VARCHAR(50)  -- ocupa só o necessário até 50
NVARCHAR(100) -- ideal para textos com acentos ou emojis
```

---

## 3. Números

### Valores Exatos

1. `TINYINT` – Inteiro de 0 a 255
2. `SMALLINT` – Inteiro de -32.768 a 32.767
3. `INT` – Inteiro de -2 bilhões até +2 bilhões
4. `BIGINT` – Para números inteiros gigantes
5. `NUMERIC(p,s)` ou `DECIMAL(p,s)` – Números com **precisão (`p`)** e **escala (`s`)**, ou seja, permite frações.

```sql
NUMERIC(5,2) -- até 5 dígitos, sendo 2 após a vírgula → Ex: 999.99
```

---

### Valores Aproximados

1. `REAL` – Aproximado, menos preciso (~7 dígitos)
2. `FLOAT` – Mais preciso que o `REAL`, até ~15 dígitos, mas ambos são usados para cálculos científicos ou onde não se exige precisão exata.

---

## 4. Temporais

Tipos usados para representar **datas, horas ou ambos**:

- `DATE` – Apenas a data (formato: `AAAA-MM-DD`)
- `DATETIME` – Data e hora (`AAAA-MM-DD hh:mm:ss`)
- `DATETIME2` – Igual ao `DATETIME`, mas com precisão de **milissegundos** (`AAAA-MM-DD hh:mm:ss.fffffff`)
- `SMALLDATETIME` – Intervalo limitado entre `'1900-01

Por padrão ele é inicializado como nulo, e pode receber tanto 1 ou 0 BIT

## 2. Caracteres

# Tamanho fixo: 
`Char` Permite inserir até uma quantidade fixa de caracteres e sempre ocupa todo o espaço reservado 10/50

# Tamanhos variáveis:

`Varchar` ou `Nvarchar` Permite inserir até uma quantidade que for definida, porem só usa o espaço que for preenchido 10/50

## 3. Numeros

# Valores Exatos

1. `TYNYINT` - não tem parte valor fracionados (ex: 1.43, 24.23) somente 1,123123,324234,313123 etc...
2. `SMALLINT` - mesma coisa porem limite maior
3. `INT` - mesma coisa porem limite maior
4. `BIGINT` - mesma coisa porem limite maior
5. `NUMERIC ou DECIMAL` - valores exatos, porem permite ter parte fraciondos, que tambem pode ser especificado a precisão e escala (escala é o numero de digitos na parte fracional) -ex: Numeric (5,2) 113,44

# Valores Aproximados

1. `REAL` - Tem precisão aproximada de até 15 digitos
2. `FLOAT` - Mesmo conceito de REAL

## 4.Temporais

`Date` - armazena data no formato aaaa/mm/dd
`DATETIME` - armazena data e horas no formato aaaa/mm/dd:hh:mm:ss
`DATETIME2` - data e horas com adição de milissegundos no formato aaaa/mm/dd:hh:mm:sssssss
`SMALLDATETIME` - data e hora respeitando o limite entre '1900-01-01:00:00:00' até '2079-06-06:23:59:59'.
`TIME` - horas, minutos, segundos e milissegundos respeitando o limite de '00:00:00.0000000' to '23:59:59.9999999'
`DATETIMEOFFSET` - permite armazenar informações de data e horas incluindo o fuso horário




# OPERAÇÕES MATEMÁTICAS NO SQL SERVER

O SQL Server permite usar operadores e funções matemáticas direto nas consultas, o que é útil pra **calcular preços, lucros, totais, descontos e mais**, direto do banco de dados.

---

## `+` Soma – Ex: valor total + frete

```sql
SELECT 
  SalesOrderID,
  SubTotal,
  Freight,
  SubTotal + Freight AS TotalComFrete
FROM Sales.SalesOrderHeader
```

---

## `-` Subtração – Ex: lucro (preço - custo)

```sql
SELECT 
  ProductID,
  ListPrice,
  StandardCost,
  ListPrice - StandardCost AS LucroBruto
FROM Production.Product
WHERE ListPrice > 0 AND StandardCost > 0
```

---

## `*` Multiplicação – Ex: total por item (preço × quantidade)

```sql
SELECT 
  SalesOrderID,
  UnitPrice,
  OrderQty,
  UnitPrice * OrderQty AS TotalItem
FROM Sales.SalesOrderDetail
```

---

## `/` Divisão – Ex: valor médio por item

```sql
SELECT 
  SalesOrderID,
  SubTotal,
  TotalDue,
  SubTotal / TotalDue AS ProporcaoProdutos
FROM Sales.SalesOrderHeader
WHERE TotalDue > 0
```

> Dica: se quiser garantir decimal, usa `1.0`:
> 
> ```sql
> SELECT 10 / 3.0 AS ResultadoDecimal
> ```

---

## `%` Módulo – Ex: saber se um ID é par ou ímpar

```sql
SELECT 
  BusinessEntityID,
  CASE 
    WHEN BusinessEntityID % 2 = 0 THEN 'Par'
    ELSE 'Ímpar'
  END AS TipoID
FROM HumanResources.Employee
```

---

## `POWER()` – Potência (ex: crescimento exponencial)

```sql
SELECT 
  ProductID,
  POWER(ListPrice, 2) AS PrecoAoQuadrado
FROM Production.Product
WHERE ListPrice > 0
```

---

## `SQRT()` – Raiz quadrada (ex: cálculo técnico)

```sql
SELECT 
  ProductID,
  SQRT(ListPrice) AS RaizDoPreco
FROM Production.Product
WHERE ListPrice > 0
```

---

## `ROUND()` – Arredondar valores

```sql
SELECT 
  ProductID,
  ListPrice * 1.13 AS ComImposto,
  ROUND(ListPrice * 1.13, 2) AS ComImpostoArredondado
FROM Production.Product
WHERE ListPrice > 0
```

---

## `CEILING()` – Arredonda pra cima (ex: embalagens)

```sql
SELECT 
  ProductID,
  Weight,
  CEILING(Weight) AS PesoFinalEmbalado
FROM Production.Product
WHERE Weight IS NOT NULL
```

---

## `FLOOR()` – Arredonda pra baixo (ex: lote inteiro)

```sql
SELECT 
  ProductID,
  Weight,
  FLOOR(Weight) AS PesoCortado
FROM Production.Product
WHERE Weight IS NOT NULL
```

---

## Exemplo completo – Cálculo de desconto, imposto e valor total

```sql
SELECT 
  ProductID,
  ListPrice,
  ListPrice * 0.9 AS PrecoComDesconto,          -- 10% de desconto
  ROUND(ListPrice * 1.12, 2) AS PrecoComImposto, -- 12% de imposto
  ROUND((ListPrice * 0.9) * 1.12, 2) AS ValorFinal
FROM Production.Product
WHERE ListPrice > 0
```

---

Esses cálculos te ajudam a **evitar exportar pra Excel** ou fazer regras fora do banco. Tudo pode ser feito direto na consulta SQL!

---


# OPERAÇÕES EM STRING

O `CONCAT()` Junta dois ou mais campos em uma única string. É comum usar para montar nomes completos, endereços, etc.

```sql
SELECT PP.BusinessEntityID, CONCAT(FirstName,' ', LastName), PPH.PhoneNumber, PE.EmailAddress
FROM Person.Person PP
JOIN Person.PersonPhone PPH ON PP.BusinessEntityID = PPH.BusinessEntityID
JOIN Person.EmailAddress PE ON PP.BusinessEntityID = PE.BusinessEntityID
where FirstName like 'Pi%' and LastName like 'a%'
```

---

## `LEN()` – Tamanho da string (número de caracteres)

A função `LEN()` retorna a quantidade de caracteres de uma string (sem contar espaços à direita).

```sql
SELECT
  FirstName,
  LEN(FirstName) AS Tamanho
FROM Person.Person
```

---

## `LOWER()` e `UPPER()` – Tudo minúsculo ou tudo maiúsculo

As funções `LOWER()` e `UPPER()` são usadas para padronizar textos e evitar diferenciação de letras maiúsculas e minúsculas.

```sql
SELECT
  FirstName,
  LOWER(FirstName) AS Minusculo,
  UPPER(FirstName) AS Maiusculo
FROM Person.Person
```

---

## `SUBSTRING()` – Pega parte de uma string

A função `SUBSTRING()` permite extrair parte de uma string, informando a posição inicial e quantos caracteres você quer pegar.

```sql
SELECT
  FirstName,
  SUBSTRING(FirstName, 1, 3) AS InicioDoNome
FROM Person.Person
```

> Neste exemplo, pega os 3 primeiros caracteres do nome.

---

## `REPLACE()` – Substitui partes da string

A função `REPLACE()` substitui um pedaço da string por outro valor informado.

```sql
SELECT
  FirstName,
  REPLACE(FirstName, 'a', '*') AS NomeAlterado
FROM Person.Person
```

> Troca todas as letras "a" por "\*".

---

## Exemplo combinado – várias funções juntas

```sql
SELECT
  CONCAT(UPPER(FirstName), ' ', LOWER(LastName)) AS NomeFormatado,
  LEN(FirstName) AS Tamanho,
  SUBSTRING(FirstName, 1, 2) AS Iniciais,
  REPLACE(LastName, 'a', '@') AS SobrenomeEditado
FROM Person.Person
WHERE LEN(FirstName) > 3
```

---

Essas funções são muito úteis para **formatar dados, realizar buscas mais flexíveis, gerar relatórios e transformar textos de forma dinâmica.**

---

# Estudo de `DATEPART` no SQL Server

O `DATEPART` é uma função do SQL Server usada para extrair partes específicas de uma data, como ano, mês, dia, semana, hora, minuto, etc. É muito útil para agrupar, filtrar ou analisar dados temporais.

---

## Exemplo 1 – Extraindo o mês da data com a `SalesOrderID`

```sql
SELECT SalesOrderID, DATEPART(month, OrderDate) AS Mes
FROM Sales.SalesOrderHeader
```

---

## Exemplo 2 – Média do valor total por ano

```sql
SELECT AVG(TotalDue) AS Media, DATEPART(year, OrderDate) AS Mes
FROM Sales.SalesOrderHeader
GROUP BY DATEPART(year, OrderDate)
ORDER BY Mes
```

---

## Exemplo 3 – Mês e ano separados

```sql
SELECT
  SalesOrderID,
  DATEPART(YEAR, OrderDate) AS Ano,
  DATEPART(MONTH, OrderDate) AS Mes
FROM Sales.SalesOrderHeader
```

---

## Exemplo 4 – Semana do ano (1 a 53)

```sql
SELECT
  SalesOrderID,
  DATEPART(WEEK, OrderDate) AS Semana
FROM Sales.SalesOrderHeader
```

---

## Exemplo 5 – Trimestre

```sql
SELECT
  SalesOrderID,
  DATEPART(QUARTER, OrderDate) AS Trimestre
FROM Sales.SalesOrderHeader
```

---

## Exemplo 6 – Dia do mês

```sql
SELECT
  SalesOrderID,
  DATEPART(DAY, OrderDate) AS Dia
FROM Sales.SalesOrderHeader
```

---

## Exemplo 7 – Dia da semana (1 = domingo, 7 = sábado)

```sql
SELECT
  SalesOrderID,
  DATEPART(WEEKDAY, OrderDate) AS DiaDaSemana
FROM Sales.SalesOrderHeader
```

Obs.: Você pode usar `SET DATEFIRST` para alterar qual dia começa a semana.

---

## Exemplo 8 – Hora, minuto e segundo

```sql
SELECT
  SalesOrderID,
  DATEPART(HOUR, OrderDate) AS Hora,
  DATEPART(MINUTE, OrderDate) AS Minuto,
  DATEPART(SECOND, OrderDate) AS Segundo
FROM Sales.SalesOrderHeader
```

---

## Exemplo 9 – Agrupando por trimestre e ano

```sql
SELECT
  DATEPART(YEAR, OrderDate) AS Ano,
  DATEPART(QUARTER, OrderDate) AS Trimestre,
  COUNT(*) AS TotalPedidos
FROM Sales.SalesOrderHeader
GROUP BY DATEPART(YEAR, OrderDate), DATEPART(QUARTER, OrderDate)
ORDER BY Ano, Trimestre
```

---

**Dica extra:** para retornar nomes como "Janeiro", "Segunda-feira" etc., você pode usar:

- `DATENAME(MONTH, OrderDate)` → retorna "Janeiro", "Fevereiro"...
- `FORMAT(OrderDate, 'MMMM')` → mesmo efeito

---

## Tópicos Abordados:

### SUBQUERY (SUBSELECT) - 19/06

Monte um relatório onde todos os produtos cadastrados que têm o preço de venda acima da média

Sem SUBQUERY:

```sql
SELECT AVG(ListPrice)
FROM Production.Product

SELECT *
FROM Production.Product
WHERE ListPrice > 438.66
```

Com SUBQUERY:

```sql
SELECT *
FROM Production.Product
WHERE ListPrice > (SELECT AVG(ListPrice) FROM Production.Product)
```

**Explicação:**

- A consulta sem subquery calcula a média (`AVG(ListPrice)`) separadamente e depois faz outra consulta comparando com esse valor.
- A subquery faz essa média diretamente na cláusula `WHERE`, tornando o código mais dinâmico e limpo.
- `(SELECT AVG(ListPrice) FROM Production.Product)` é a subconsulta que retorna o valor médio do preço.

---

### SUBQUERY vs JOIN – Funcionários com cargo "Design Engineer"

Aqui estamos fazendo uma **SUBQUERY** e depois comparando com uma **JOIN** que retorna o mesmo resultado. Ambas trazem os nomes dos funcionários que têm o cargo de **Design Engineer**.

Consulta de exemplo com filtro por IDs (exemplo básico):

```sql
SELECT *
FROM HumanResources.Employee
WHERE BusinessEntityID IN (5,6,15)
```

Consulta usando **SUBQUERY**:

```sql
SELECT FirstName
FROM Person.Person
WHERE BusinessEntityID IN (
    SELECT BusinessEntityID
    FROM HumanResources.Employee
    WHERE JobTitle = 'Design Engineer'
)
```

Consulta equivalente usando **JOIN**:

```sql
SELECT A.BusinessEntityID, B.FirstName, B.LastName, A.JobTitle
FROM HumanResources.Employee A
JOIN Person.Person B ON A.BusinessEntityID = B.BusinessEntityID
WHERE A.JobTitle = 'Design Engineer'
```

**Explicação:**

- A **SUBQUERY** faz a seleção dos `BusinessEntityID` de quem tem o cargo "Design Engineer" e usa esses IDs para buscar os nomes na tabela `Person.Person`.
- Já o **JOIN** conecta diretamente a tabela de funcionários com a tabela de pessoas, retornando todos os dados desejados numa só consulta.
- Ambas são válidas, mas o JOIN costuma ser mais performático em muitos cenários, além de mais direto quando você precisa de várias colunas relacionadas.

---

### SUBQUERY vs JOIN – Endereços no estado de "Alberta"

Aqui temos um **desafio** onde buscamos todos os endereços localizados no estado de **Alberta**. Para isso, comparamos duas abordagens: uma usando **SUBQUERY** e outra usando **JOIN**.

Consulta usando **SUBQUERY**:

```sql
SELECT *
FROM Person.Address
WHERE StateProvinceID IN (
	SELECT StateProvinceID
	FROM Person.StateProvince
	WHERE Name = 'Alberta'
)
```

Consulta equivalente usando **JOIN**:

```sql
SELECT *
FROM Person.Address A
JOIN Person.StateProvince B ON A.StateProvinceID = B.StateProvinceID
WHERE B.Name = 'Alberta'
```

**Explicação:**

- A **SUBQUERY** busca o `StateProvinceID` correspondente ao nome "Alberta" e usa esse ID para filtrar os endereços.
- A **JOIN** conecta diretamente as tabelas `Person.Address` e `Person.StateProvince`, filtrando os resultados pelo nome do estado.
- Ambas as abordagens retornam os mesmos dados, mas a JOIN tende a ser mais clara quando precisamos de colunas de ambas as tabelas.

---

### SELF JOIN

Um **SELF JOIN** ocorre quando uma tabela é relacionada com ela mesma. É útil para comparar registros da **mesma tabela**, com base em alguma condição lógica entre os dados.

#### Exemplo 1: Comparando produtos com o mesmo desconto

```sql
SELECT DISTINCT A.ProductID, A.Discount, B.ProductID, B.Discount
FROM [Order Details] A, [Order Details] B
WHERE A.Discount = B.Discount
```

#### Exemplo 2: Funcionários contratados no mesmo ano (sem repetições invertidas)

```sql
SELECT A.FirstName, A.HireDate, B.FirstName, B.HireDate
FROM Employees A, Employees B
WHERE DATEPART(YEAR, A.HireDate) = DATEPART(YEAR, B.HireDate)
 AND A.EmployeeID < B.EmployeeID
```

**O que a consulta faz:**

Realiza um self join na tabela `Employees`.
Compara os anos de contratação entre pares de funcionários.
A condição `A.EmployeeID < B.EmployeeID` evita que os pares sejam repetidos ao contrário e também impede que o funcionário seja comparado consigo mesmo.

**Situação de uso:** Essa consulta é útil para identificar funcionários que foram contratados no mesmo ano e cruzar seus dados para análises de integração, perfil por período de contratação ou eventos internos.

#### Exemplo 3: Clientes que pertencem à mesma região

```sql
SELECT A.ContactName, A.Region, B.ContactName, B.Region
FROM Customers A, Customers B
WHERE A.Region = B.Region
ORDER BY A.ContactName ASC, B.ContactName ASC
```

**O que a consulta faz:**

Realiza um self join na tabela `Customers`.
Compara os clientes que estão na mesma região.
O resultado é ordenado alfabeticamente pelos nomes dos clientes A e B.

**Situação de uso:** Esse tipo de consulta pode ser usado para identificar possíveis parcerias, ações de marketing regionalizadas ou verificar a concentração de clientes por localidade. Observação: essa versão da consulta não filtra comparações do cliente com ele mesmo, nem evita pares duplicados em ordem invertida.

**Dica geral ao usar SELF JOINs:**

- Use aliases (A, B) para diferenciar as duas instâncias da tabela.
- Use condições como `A.ID < B.ID` para evitar repetições invertidas e comparações do registro com ele mesmo.
- O `DISTINCT` pode ajudar a limpar repetições idênticas.

---

## SQL 03/06

### UNION

O operador `UNION` combina dois ou mais resultados de um `SELECT` em um resultado apenas.

```sql
SELECT coluna1, coluna2
FROM tabela1
UNION
SELECT coluna1, coluna2
FROM tabela2
```

**Exemplos:**

```sql
SELECT ProductID, Name, ProductNumber
FROM Production.Product
WHERE Name LIKE '%Chain%'
UNION
SELECT ProductID, Name, ProductNumber
FROM Production.Product
WHERE Name LIKE '%Decal%'
```

```sql
SELECT FirstName, Title, MiddleName
FROM Person.Person
WHERE Title = 'Mr.'
UNION
SELECT FirstName, Title, MiddleName
FROM Person.Person
WHERE MiddleName = 'A'
```

### LEFT OUTER JOIN (LEFT JOIN)

**Prática:** Quero descobrir quais pessoas têm um cartão de crédito registrado.

```sql
SELECT *
FROM Person.Person PP
LEFT JOIN Sales.PersonCreditCard SPC ON PP.BusinessEntityID = SPC.BusinessEntityID
WHERE SPC.BusinessEntityID IS NULL
```

- `INNER JOIN`: 19118
- `LEFT JOIN`: 19972
- `SELECT 19972 - 19118`

---

### INNER JOIN

Retorna apenas os resultados que correspondem (existem) tanto na Tabela A como na Tabela B.

**Exemplo:**

Tabela A:

```
id  | nome
--- | ---
1   | Robo
2   | Macaco
3   | Samurai
4   | Monitor
```

Tabela B:

```
id  | nome
--- | ---
1   | Espada
2   | Robo
3   | Mario
4   | Samurai
```

```sql
SELECT *
FROM TabelaA
INNER JOIN TabelaB ON TabelaA.nome = TabelaB.nome
```

**Resultado:**

```
id  | nome    | id  | nome
--- | ------- | --- | ------
1   | Robo    | 2   | Robo
3   | Samurai | 4   | Samurai
```

---

### FULL OUTER JOIN

Retorna um conjunto de todos os registros correspondentes da Tabela A e Tabela B quando são iguais. E, além disso, se não houver valores correspondentes, ele simplesmente irá preencher esse lado com `NULL`.

**Exemplo:**

Tabela A:

```
id  | nome
--- | ---
1   | Robo
2   | Macaco
3   | Samurai
4   | Monitor
```

Tabela B:

```
id  | nome
--- | ---
1   | Espada
2   | Robo
3   | Mario
4   | Samurai
```

```sql
SELECT *
FROM TabelaA
FULL OUTER JOIN TabelaB ON TabelaA.nome = TabelaB.nome
```

**Resultado:**

```
id   | nome    | id   | nome
---- | ------- | ---- | ------
1    | Robo    | 2    | Robo
2    | Macaco  | NULL | NULL
3    | Samurai | 4    | Samurai
4    | Monitor | NULL | NULL
NULL | NULL    | 1    | Espada
NULL | NULL    | 3    | Mario
```

---

### LEFT OUTER JOIN

Retorna um conjunto de todos os registros da Tabela A, e, além disso, os registros correspondentes (quando disponíveis) na Tabela B. Se não houver registros correspondentes, ele simplesmente vai preencher com `NULL`.

**Exemplo:**

Tabela A:

```
id  | nome
--- | ---
1   | Robo
2   | Macaco
3   | Samurai
4   | Monitor
```

Tabela B:

```
id  | nome
--- | ---
1   | Espada
2   | Robo
3   | Mario
4   | Samurai
```

```sql
SELECT *
FROM TabelaA
LEFT OUTER JOIN TabelaB ON TabelaA.nome = TabelaB.nome
```

**Resultado:**

```
id  | nome    | id   | nome
--- | ------- | ---- | ------
1   | Robo    | 2    | Robo
2   | Macaco  | NULL | NULL
3   | Samurai | 4    | Samurai
4   | Monitor | NULL | NULL
```

---

### RIGHT OUTER JOIN

O contrário do `LEFT OUTER JOIN`. Irá considerar os dados da Tabela B e se na Tabela A não houver registros correspondentes, ele simplesmente vai preencher com `NULL`.

---

## SQL 02/06

```sql
SELECT PP.BusinessEntityID, PN.Name, PN.PhoneNumberTypeId, PP.PhoneNumber
FROM Person.PhoneNumberType PN
INNER JOIN Person.PersonPhone PP ON PP.PhoneNumberTypeID = PN.PhoneNumberTypeID
```

```sql
SELECT TOP 10 PA.AddressID, PA.City, PS.StateProvinceID, PS.Name
FROM Person.StateProvince PS
INNER JOIN Person.Address PA ON PA.StateProvinceID = PS.StateProvinceID
```

---

## SQL 23/05

### Exemplo de JOIN para obter informações de subcategorias de produtos

**Objetivo:** Obter os nomes dos produtos e as informações de suas subcategorias (ListPrice, Nome do produto, Nome da Subcategoria).

```sql
SELECT TOP 10 *
FROM Production.Product p
```

```sql
SELECT TOP 10 *
FROM Production.ProductSubcategory ps
```

```sql
SELECT p.ProductSubcategoryID, p.ListPrice, p.Name AS "Nome do Produto", ps.Name AS "Nome categoria"
FROM Production.Product p
INNER JOIN Production.ProductSubcategory ps ON ps.ProductSubcategoryID = p.ProductSubcategoryID
```

### Exemplos de JOIN com tabelas de endereço

```sql
SELECT TOP 10 *
FROM Person.BusinessEntityAddress
```

```sql
SELECT TOP 10 *
FROM Person.Address
```

```sql
SELECT TOP 10 *
FROM Person.BusinessEntityAddress bea
INNER JOIN Person.Address pa ON pa.AddressID = bea.AddressID
```

---

## SQL 20/05

### INNER JOIN

```sql
SELECT pp.BusinessEntityID, pp.FirstName, pp.LastName, pe.EmailAddress, pe.rowguid, pe.ModifiedDate
FROM Person.Person pp
INNER JOIN Person.EmailAddress pe ON pp.BusinessEntityID = pe.BusinessEntityID
```

**Explicação:**

- Seleciona as colunas no `SELECT` e apelida cada uma (por exemplo, `pp` para `Person.Person` e `pe` para `Person.EmailAddress`) para identificar a qual tabela pertence cada coluna.
- Seleciona a tabela principal e usa o `INNER JOIN` para selecionar a outra tabela. Use um identificador em comum (por exemplo, `BusinessEntityID` existe em ambas as tabelas) usando o `ON` para juntá-las, lembrando de separar sempre por apelidos.

---

## SQL 19/05

### Exemplo de INNER JOIN com tabelas Cliente e Endereço

**TABELA CLIENTE**

- `CLIENTE ID` (chave primária)
- `NOME`
- `ENDERECOID` (chave estrangeira)

**TABELA ENDERECO**

- `ENDERECOID` (chave primária)
- `RUA`
- `CIDADE`

```sql
SELECT C.ClienteId, C.Nome, E.Rua, E.Cidade
FROM Cliente C
INNER JOIN Endereco E ON E.EnderecoId = C.EnderecoId
```

---

## SQL 30/04

### Consultas com GROUP BY e HAVING

1.  **Objetivo:** Identificar as províncias (`StateProvinceID`) com o maior número de cadastros no sistema, ou seja, aquelas que estão registradas mais de 1000 vezes.

    ```sql
    SELECT StateProvinceID, count(StateProvinceID) as "CONTEGEM"
    FROM Person.Address
    GROUP BY StateProvinceID
    HAVING count(StateProvinceID) > 1000
    ```

2.  **Objetivo:** Sendo uma multinacional, os gerentes querem saber quais produtos (`ProductID`) não estão trazendo em média no mínimo 1 milhão em total de vendas (`LineTotal`).

    ```sql
    SELECT ProductID,AVG(LineTotal) AS "Media de Vendas"
    FROM Sales.SalesOrderDetail
    GROUP BY ProductID
    HAVING AVG(LineTotal) < 1000000
    ```

---

### Prática com Funções de Agregação e GROUP BY

1.  **Objetivo:** Saber quantas pessoas têm o mesmo `MiddleName`, agrupadas por `MiddleName`.

    ```sql
    SELECT color, AVG(listPrice) AS "PRECO"
    FROM Production.Product
    WHERE Color = 'silver'
    GROUP BY Color
    ```

2.  **Objetivo:** Saber em média qual é a quantidade (`Quantity`) que cada produto é vendido na loja.

    ```sql
    SELECT ProductID, AVG(OrderQty) AS "Media"
    FROM Sales.SalesOrderDetail
    GROUP BY ProductID
    ORDER by ProductID desc
    ```

3.  **Objetivo:** Saber quais foram as 10 vendas que no total tiveram os maiores valores de venda (`LineTotal`) por produto, do maior valor para o menor.

    ```sql
    SELECT TOP (10) ProductID, SUM(LineTotal) AS "Valor de Venda"
    FROM Sales.SalesOrderDetail
    GROUP by ProductID
    ORDER BY SUM(LineTotal) desc;
    ```

4.  **Objetivo:** Saber quantos produtos e qual é a quantidade média de produtos temos cadastrados nas nossas ordens de serviço (`WorkOrder`), agrupados por `ProductID`.

    ```sql
    SELECT ProductID, COUNT(ProductID) AS "Quantidade", AVG(OrderQty) AS "MEDIA"
    FROM Production.WorkOrder
    GROUP BY ProductID
    ```

---

### HAVING

O `HAVING` é basicamente muito usado em junção com o `GROUP BY` para filtrar resultados de um agrupamento.

De uma forma mais simples, dá para entender ele como basicamente um `WHERE` para dados agrupados.

```sql
SELECT coluna1, funcaoAgregacao(coluna2)
FROM nomeTabela
GROUP BY coluna1
HAVING condicao;
```

A grande diferença entre `HAVING` e `WHERE` é que o `HAVING` é aplicado depois que os dados já foram agrupados, enquanto o `WHERE` é aplicado antes dos dados serem agrupados.

**Exemplos:**

- **Objetivo:** Queremos saber quais nomes no sistema têm uma ocorrência maior que 10 vezes.

  ```sql
  SELECT FirstName, count(FirstName) as "Quantidade"
  FROM person.person
  GROUP BY FirstName
  HAVING count(firstname) > 10
  ```

- **Objetivo:** Queremos saber quais produtos que no total de vendas estão entre 162k a 500k.

  ```sql
  SELECT productid, sum(linetotal) as "TOTAL"
  FROM Sales.SalesOrderDetail
  GROUP BY ProductID
  HAVING sum(linetotal) between 162000 and 500000
  ```

- **Objetivo:** Você quer saber quais nomes no sistema têm uma ocorrência maior que 10 vezes, porém somente onde o título é 'Mr.'.

  ```sql
  SELECT FirstName, count(FirstName) as "QUANTIDADE"
  FROM Person.Person
  WHERE Title = 'Mr.'
  GROUP BY FirstName
  HAVING count(FirstName) > 10
  ```

---

## SQL 28/04

### COMANDOS SUM, MIN, MAX, AVG

#### AS

Dá nome à tabela consultada/personalizada.

```sql
SELECT TOP 10 SUM(LineTotal) AS "Soma dos valores"
FROM Sales.SalesOrderDetail
```

```sql
SELECT TOP 10 MIN(LineTotal) AS "MIN menor valor"
FROM Sales.SalesOrderDetail
```

```sql
SELECT TOP 10 MAX(LineTotal) AS "MAX maior valor"
FROM Sales.SalesOrderDetail
```

```sql
SELECT TOP 10 AVG(LineTotal) AS "AVG media total"
FROM Sales.SalesOrderDetail
```

### GROUP BY

- O `GROUP BY` basicamente divide o resultado da sua pesquisa em grupos.
- Para cada grupo você pode aplicar uma função de agregação, por exemplo:
  - Calcular a soma de itens.
  - Contar o número de itens naquele grupo.

```sql
SELECT coluna1, funcaoAgregacao(coluna2)
FROM nomeTabela
GROUP BY coluna1;
```

**EXEMPLO:**

```sql
SELECT FirstName, COUNT(FirstName) AS "CONTAGEM"
FROM Person.Person
GROUP BY FirstName
ORDER BY CONTAGEM, FirstName ASC
```

```sql
SELECT color, AVG(listPrice) AS "PRECO"
FROM Production.Product
WHERE Color = 'silver'
GROUP BY Color
```

---

## SQL 26/04

### DESAFIO 2

**Objetivo:** Obter o nome e número do produto dos produtos que têm o `ProductID` entre 1 e 4.

**R:**

```sql
SELECT top 4 ProductID, Name, ProductNumber
FROM Production.Product
ORDER BY ProductID asc
```

### BETWEEN

O `BETWEEN` é usado para encontrar valor entre um valor mínimo e valor máximo.

`valor BETWEEN minimo AND maximo`

É a mesma coisa que dizer:
`Valor >= minimo AND valor <= maximo`

```sql
SELECT BusinessEntityID, HireDate
FROM HumanResources.Employee
WHERE HireDate BETWEEN '2009/01/01' and '2010/01/01'
ORDER BY HireDate desc
```

### IN

Usamos o operador `IN` junto com o `WHERE`, para verificar se um valor corresponde com qualquer valor passado na lista de valores.

`valor IN (valor1, valor2)`
`valor IN (SELECT valor FROM nomeDaTabela)`

```sql
SELECT *
FROM Person.Person
WHERE BusinessEntityID IN (2,7,13)
```

### LIKE

Vamos dizer que você quer encontrar uma pessoa no banco de dados que você sabe que o nome dela era `ovi...` alguma coisa.

```sql
SELECT *
FROM Person.Person
WHERE FirstName like 'ovi%'
```

- `%`: não importa o conteúdo

```sql
SELECT *
FROM Person.Person
WHERE FirstName like '%ro%'
```

- `_`: limita apenas um caractere ou mais dependendo da quantidade de `_`

```sql
SELECT *
FROM Person.Person
WHERE FirstName LIKE '%ro_'
```

### Exercícios de Prática

1.  **Objetivo:** Quantos produtos temos cadastrados no sistema que custam mais que 1500 dólares?

    ```sql
    SELECT COUNT (ListPrice)
    FROM Production.Product
    WHERE ListPrice >= 1500
    ```

2.  **Objetivo:** Quantas pessoas temos com o sobrenome que inicia com a letra P?

    ```sql
    SELECT COUNT (LastName)
    FROM Person.Person
    WHERE LastName LIKE 'P%'
    ```

3.  **Objetivo:** Em quantas cidades únicas estão cadastrados nossos clientes?

    ```sql
    SELECT COUNT (DISTINCT CITY)
    FROM person.Address
    ```

4.  **Objetivo:** Quais são as cidades únicas que temos cadastradas em nosso sistema?

    ```sql
    SELECT City
    FROM person.Address
    ```

5.  **Objetivo:** Quantos produtos vermelhos têm o preço entre 500 a 1000 dólares?

    ```sql
    SELECT COUNT(ListPrice)
    FROM Production.Product
    WHERE ListPrice <= '1000' and ListPrice > '500'
    ```

6.  **Objetivo:** Quantos produtos cadastrados têm a palavra 'road' no nome deles?

    ```sql
    SELECT COUNT(Name)
    FROM Production.Product
    WHERE Name Like '%road%';
    ```

---

## SQL 21/04

### Operadores Lógicos e de Comparação

```
OPERADOR    -    DESCRIÇÃO
=                           IGUAL
>                           MAIOR QUE
<                           MENOR QUE
>=                         MAIOR QUE OU IGUAL
<=                         MENOR QUE OU IGUAL
<>                         DIFERENTE QUE
AND                     OPERADOR LÓGICO E
OR                        OPERADOR LÓGICO OU
```

### DESAFIO 1

**Objetivo:** A equipe de produção de produtos precisa do nome de todas as peças que pesam mais que 500kg mas não mais que 700kg para inspeção.

- `weight` = peso

**R:**

```sql
SELECT weight
FROM Production.Product
WHERE Weight >= '500' and Weight <= '700'
```

### DESAFIO 2

**Objetivo:** Foi pedido pelo marketing uma relação de todos os empregados (`employees`) que são casados (`single` = solteiro, `married` = casado) e são assalariados (`salaried`).

**R:**

```sql
SELECT *
FROM HumanResources.Employee
WHERE MaritalStatus = 'M' and SalariedFlag = '1'
```

### DESAFIO 3

**Objetivo:** Um usuário chamado Peter Krebs está devendo um pagamento. Consiga o e-mail dele para que possamos enviar uma cobrança!

**R:**

```sql
SELECT *
FROM Person.Person
WHERE FirstName = 'Peter' and LastName = 'Krebs'
```

```sql
SELECT *
FROM Person.EmailAddress
WHERE BusinessEntityID = '26'
```

---

### COUNT

**Exemplo:**

```sql
SELECT COUNT (*)
FROM Person.Person
```

- Retorna quantas linhas tem na Tabela `Person.Person`.

```sql
SELECT COUNT (Title)
FROM Person.Person
```

- Retorna todas as linhas da coluna `Title`.

```sql
SELECT count (DISTINCT Title)
FROM Person.Person
```

- Retorna o número de linhas que a informação não se repete na coluna `Title`.

### DESAFIO 1

**Objetivo:** Saber quantos produtos temos cadastrados em nossa tabela de produtos.

**R:**

```sql
SELECT count (*)
FROM Production.Product
```

- Resultado: 504

### DESAFIO 2

**Objetivo:** Saber quantos tamanhos de produtos temos cadastrado em nossa tabela.

**R:**

```sql
SELECT count (Size)
FROM Production.Product
```

- Resultado: 211

---

### TOP

```sql
SELECT Top (10) *
FROM Production.Product
```

- Retorna uma quantidade de linhas limitadas conforme informado no `()`.

### ORDER BY

```sql
SELECT Top (20)*
FROM Person.Person
ORDER BY FirstName asc
```

- Ordena os dados de uma coluna em ordem crescente (`asc`) ou decrescente (`desc`).

---
