# Projeto_Rox
O presente problema se refere aos dados de uma empresa que produz bicicletas. 
O objetivo deste desafio é compreender os seus conhecimentos e experiência analisando os seguintes aspectos:
1. Fazer a modelagem conceitual dos dados;
2. Criação da infraestrutura necessária;
3. Criação de todos os artefatos necessários para carregar os arquivos para o banco criado;
4. Desenvolvimento de SCRIPT para análise de dados;
5. (opcional) Criar um relatório em qualquer ferramenta de visualização de dados.
<br />

# 1. Fazer a modelagem conceitual dos dados

Para resolução do modelo conceitual foi utilizado o BR modelo e desconsiderado os demais atributos, exceto a chave primária. Um segundo modelo foi realizado utilizadno o Power Query.

![Modelo Conceitual](https://user-images.githubusercontent.com/97460254/168407159-f90697f5-b9fe-4591-9b93-f11aa1cf2778.PNG)

![Modelo Power Query](https://user-images.githubusercontent.com/97460254/168407467-52ea3a59-eb01-44d8-b641-1f89f586748a.jpeg)
<br />

# 2. Criação da infraestrutura necessária

Para a parte de infraestrutura foi utilizado o banco de dados SQL server hospedado em nuvem (Azure). Para o manuseio do mesmo foi utilizado o Microsoft SQL Server Management Studio. <br /> <br />
Caso haja a necessidade de coneção com o Banco de dados será disponilibizado o usuario admin 

**Server Name:** projeto-rox.database.windows.net <br />
**Login:** Gustavo <br />
**Password:** @Testerox <br />
<br />

![image](https://user-images.githubusercontent.com/97460254/168407847-94845d44-fad2-4e32-b9d0-d838c72b89ac.png)
<br />

# 3. Criação de todos os artefatos necessários para carregar os arquivos para o banco criado

A importação dos arquivos .CSV foi feita manualmente atravez do Microsoft SQL Server Management Studio. <br />  <br />
**ATENÇÂO:** Para importação do Dataset Person.Person.csv 2 colunas (AdditionalContactInfo e Demographics) tiveram de ser removidas para carregamento no SQL server pois conflitavam fazendo com que todos os dados, do Dataset, fossem carregados como nulos para o banco de dados.

| DataSet |
|:-------:|
|Person.Person.csv|
|Production.Product|
|Sales.Customer|
|Sales.SalesOrderDetail|
|Sales.SalesOrderHeader|
|Sales.SpecialOfferProduct|

![image](https://user-images.githubusercontent.com/97460254/168408124-ec9f5634-95fd-4db9-abe1-4dcc3cb84e4f.png)
<br />

# 4. Desenvolvimento de SCRIPT para análise de dados;

-	Escreva uma query que retorna a quantidade de linhas na tabela Sales.SalesOrderDetail pelo campo SalesOrderID, desde que tenham pelo menos três linhas de detalhes.
 
```SQL 
select SalesOrderID, count(SalesOrderID) as Contagem from [Sales.SalesOrderDetail]
group by SalesOrderID
having count(SalesOrderID) >=3;
```

|Sales Order ID|Count|
|:-------:|:-------:|
|47999|	30|
|53548|	27|
|50265|	48|
|52365|	3|
|46985|	15|
|57042|	26|
|58433|	3|
|60596|	4|
|61232|	10|
|65866|	5|

<br />

- Escreva uma query que ligue as tabelas Sales.SalesOrderDetail, Sales.SpecialOfferProduct e Production.Product e retorne os 3 produtos (Name) mais vendidos (pela soma de OrderQty), agrupados pelo número de dias para manufatura (DaysToManufacture).

```SQL
select top 3 p.Name, p.DaysToManufacture , SUM(cast(sod.OrderQty as int)) as qnt from [Sales.SalesOrderDetail] sod
left join [Sales.SpecialOfferProduct] sop on sod.SpecialOfferID = sop.SpecialOfferID
left join [Production.Product] p on sop.ProductID = p.ProductID
group by p.Name, p.DaysToManufacture
order by SUM(cast(sod.OrderQty as int)) desc;
```

|Name|Days To Manufactory|Qnt|
|:----:|:----:|:----:|
|Women's Mountain Shorts, L| 0 |270244|
|Full-Finger Gloves, L	| 0 |	270244|
|Women's Tights, S	| 0 |	270159|

<br />

- Escreva uma query ligando as tabelas Person.Person, Sales.Customer e Sales.SalesOrderHeader de forma a obter uma lista de nomes de clientes e uma contagem de pedidos efetuados.
```SQL
select p.FirstName+ ' ' +p.LastName as Nome, COUNT(soh.CustomerID) as Pedidos from [Sales.SalesOrderHeader] soh
left join [Sales.Customer] c on soh.CustomerID = c.CustomerID
left join [Person.Person] p on c.PersonID = p.BusinessEntityID
group by p.FirstName, p.LastName
order by Pedidos desc;
```

|Name|Order|
|:---:|:---:|
|Dalton Perez|	28|
|Mason Roberts|	28|
|Fernando Barnes|	27|
|Ashley Henderson|	27|
|Samantha Jenkins|	27|
|Nancy Chapman|	27|
|Daniel Davis|	27|
|Charles Jackson|	27|
|Jennifer Simmons|	27|
|Hailey Patterson|	27|

<br />

- Escreva uma query usando as tabelas Sales.SalesOrderHeader, Sales.SalesOrderDetail e Production.Product, de forma a obter a soma total de produtos (OrderQty) por ProductID e OrderDate.
```SQL
select p.ProductID, p.name, soh.OrderDate, sum(cast(sod.OrderQty as int)) as Qnt from [Sales.SalesOrderDetail] sod
left join [Sales.SalesOrderHeader] soh on sod.SalesOrderID=soh.SalesOrderID
left join [Production.Product] p on sod.ProductID=p.ProductID
group by p.ProductID, p.name, soh.OrderDate
order by sum(cast(sod.OrderQty as int)) desc;
```

|Product ID|Product|Order Date|Order|
|:---:|:---:|:---:|:---:|
|864|	Classic Vest, S	|2013-06-30 00:00:00.000	|498|
|864|	Classic Vest, S	|2013-07-31 00:00:00.000	|465|
|884|	Short-Sleeve Classic Jersey, XL	|2013-06-30 00:00:00.000|	444|
|867|	Women's Mountain Shorts, S	|2013-06-30 00:00:00.000	|427|
|864|	Classic Vest, S	|2014-03-31 00:00:00.000	|424|
|884|	Short-Sleeve Classic Jersey, XL	|2013-07-31 00:00:00.000	|420|
|712|	AWC Logo Cap	|2013-06-30 00:00:00.000	|415|
|863|	Full-Finger Gloves, L	|2012-06-30 00:00:00.000	|409|
|715|	Long-Sleeve Logo Jersey, L	|2013-06-30 00:00:00.000	|406|
|876|	Hitch Rack - 4-Bike	|2013-07-31 00:00:00.000	|397|

<br />

- Escreva uma query mostrando os campos SalesOrderID, OrderDate e TotalDue da tabela Sales.SalesOrderHeader. Obtenha apenas as linhas onde a ordem tenha sido feita durante o mês de setembro/2011 e o total devido esteja acima de 1.000. Ordene pelo total devido decrescente.

```SQL
select SalesOrderID, OrderDate, cast(replace(TotalDue, ',', '.') as float) as TotalDue from [Sales.SalesOrderHeader]
where OrderDate between '2011-09-01' and '2011-10-01'
and cast(replace(TotalDue, ',', '.') as float) >= 1000
order by OrderDate desc;
```

|Sales Ordre ID|Order Date|Total Due|
|:---:|:---:|:---:|
|44479|	2011-09-30 |00:00:00.000	|3729,364|
|44480|	2011-09-30 |00:00:00.000	|3729,364|
|44472|	2011-09-29 |00:00:00.000	|3953,9884|
|44473|	2011-09-29 |00:00:00.000	|3953,9884|
|44475|	2011-09-29 |00:00:00.000	|3756,989|
|44476|	2011-09-29 |00:00:00.000	|3953,9884|
|44477|	2011-09-29 |00:00:00.000	|3953,9884|
|44478|	2011-09-29 |00:00:00.000	|3953,9884|
|44462|	2011-09-28 |00:00:00.000	|3953,9884|
|44464|	2011-09-28 |00:00:00.000	|3953,9884|
