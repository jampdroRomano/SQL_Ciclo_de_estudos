# Ciclo de Estudos SQL

Este documento contém um ciclo de estudos de SQL, abordando diversos tópicos e exemplos práticos. O conteúdo está organizado por datas, refletindo o progresso do aprendizado.

---

## Tópicos Abordados:




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

*   Use aliases (A, B) para diferenciar as duas instâncias da tabela.
*   Use condições como `A.ID < B.ID` para evitar repetições invertidas e comparações do registro com ele mesmo.
*   O `DISTINCT` pode ajudar a limpar repetições idênticas.

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

*   `INNER JOIN`: 19118
*   `LEFT JOIN`: 19972
*   `SELECT 19972 - 19118`

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

*   Seleciona as colunas no `SELECT` e apelida cada uma (por exemplo, `pp` para `Person.Person` e `pe` para `Person.EmailAddress`) para identificar a qual tabela pertence cada coluna.
*   Seleciona a tabela principal e usa o `INNER JOIN` para selecionar a outra tabela. Use um identificador em comum (por exemplo, `BusinessEntityID` existe em ambas as tabelas) usando o `ON` para juntá-las, lembrando de separar sempre por apelidos.

---




## SQL 19/05

### Exemplo de INNER JOIN com tabelas Cliente e Endereço

**TABELA CLIENTE**

*   `CLIENTE ID` (chave primária)
*   `NOME`
*   `ENDERECOID` (chave estrangeira)

**TABELA ENDERECO**

*   `ENDERECOID` (chave primária)
*   `RUA`
*   `CIDADE`

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

*   **Objetivo:** Queremos saber quais nomes no sistema têm uma ocorrência maior que 10 vezes.

    ```sql
    SELECT FirstName, count(FirstName) as "Quantidade"
    FROM person.person
    GROUP BY FirstName
    HAVING count(firstname) > 10
    ```

*   **Objetivo:** Queremos saber quais produtos que no total de vendas estão entre 162k a 500k.

    ```sql
    SELECT productid, sum(linetotal) as "TOTAL"
    FROM Sales.SalesOrderDetail
    GROUP BY ProductID
    HAVING sum(linetotal) between 162000 and 500000
    ```

*   **Objetivo:** Você quer saber quais nomes no sistema têm uma ocorrência maior que 10 vezes, porém somente onde o título é 'Mr.'.

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

*   O `GROUP BY` basicamente divide o resultado da sua pesquisa em grupos.
*   Para cada grupo você pode aplicar uma função de agregação, por exemplo:
    *   Calcular a soma de itens.
    *   Contar o número de itens naquele grupo.

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

*   `%`: não importa o conteúdo

```sql
SELECT *
FROM Person.Person
WHERE FirstName like '%ro%'
```

*   `_`: limita apenas um caractere ou mais dependendo da quantidade de `_`

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

*   `weight` = peso

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

*   Retorna quantas linhas tem na Tabela `Person.Person`.

```sql
SELECT COUNT (Title)
FROM Person.Person
```

*   Retorna todas as linhas da coluna `Title`.

```sql
SELECT count (DISTINCT Title)
FROM Person.Person
```

*   Retorna o número de linhas que a informação não se repete na coluna `Title`.

### DESAFIO 1

**Objetivo:** Saber quantos produtos temos cadastrados em nossa tabela de produtos.

**R:**
```sql
SELECT count (*)
FROM Production.Product
```
*   Resultado: 504

### DESAFIO 2

**Objetivo:** Saber quantos tamanhos de produtos temos cadastrado em nossa tabela.

**R:**
```sql
SELECT count (Size)
FROM Production.Product
```
*   Resultado: 211

---

### TOP

```sql
SELECT Top (10) *
FROM Production.Product
```

*   Retorna uma quantidade de linhas limitadas conforme informado no `()`.

### ORDER BY

```sql
SELECT Top (20)*
FROM Person.Person
ORDER BY FirstName asc
```

*   Ordena os dados de uma coluna em ordem crescente (`asc`) ou decrescente (`desc`).

---



