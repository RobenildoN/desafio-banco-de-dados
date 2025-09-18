# 🛒 Banco de Dados de E-commerce

Este projeto consiste na modelagem e implementação de um banco de dados
para um cenário de **E-commerce**, abrangendo clientes **Pessoa Física
(PF)** e **Pessoa Jurídica (PJ)**, produtos, fornecedores, vendedores,
pedidos, pagamentos e entregas.

O objetivo é estruturar as entidades principais, aplicar restrições de
integridade, criar relacionamentos e desenvolver consultas SQL que
respondam a perguntas de negócio típicas de um sistema de vendas online.

------------------------------------------------------------------------

## 📂 Estrutura do Banco
![Estrutura Banco de dados](https://github.com/RobenildoN/desafio-banco-de-dados/blob/main/Estrutura%20Banco%20de%20dados.png)

### Principais Tabelas

-   **client** → armazena os clientes (base única, podendo ser PF ou
    PJ)\
-   **clientPF** → informações complementares de clientes Pessoa Física\
-   **clientPJ** → informações complementares de clientes Pessoa
    Jurídica\
-   **payments** → formas de pagamento cadastradas\
-   **orders** → pedidos feitos por clientes, vinculados a pagamentos\
-   **delivery** → informações de entrega, status e código de rastreio\
-   **product** → catálogo de produtos (eletrônicos, vestuário,
    brinquedos, alimentos e móveis)\
-   **productStorage** → locais e quantidades em estoque\
-   **supplier** → fornecedores que disponibilizam produtos\
-   **seller** → vendedores do marketplace\
-   **productSupplier** → tabela associativa de fornecedores e produtos\
-   **productOrder** → tabela associativa de pedidos e produtos

### Diagrama Entidade-Relacionamento (DER)

O modelo contempla todos os relacionamentos entre clientes, produtos,
pedidos, fornecedores e entregas, garantindo integridade referencial com
**chaves primárias e estrangeiras**.

------------------------------------------------------------------------

## 📊 Consultas SQL

Foram desenvolvidas queries para diferentes níveis de análise:

### 1. Recuperações simples (`SELECT`)

Exemplo: listar todos os clientes cadastrados.

``` sql
select idClient, Fname, Lname, CPF, Address from client;
```

### 2. Filtros (`WHERE`)

Exemplo: pedidos em processamento.

``` sql
select idOrder, orderDescription, orderStatus
from orders
where orderStatus = 'Em processamento';
```

### 3. Atributos derivados

Exemplo: cálculo do valor total de pedidos (produtos + frete).

``` sql
select 
    o.idOrder,
    sum(po.prodQuantity * 
        case p.category
            when 'Eletrônico' then 3000
            when 'Vestuário' then 100
            when 'Brinquedos' then 80
            when 'Móveis' then 1500
        end
    ) + o.sendValue as valor_total
from orders o
join productOrder po on o.idOrder = po.idPOorder
join product p on p.idProduct = po.idPOproduct
group by o.idOrder, o.sendValue;
```

### 4. Ordenações (`ORDER BY`)

Exemplo: fornecedores em ordem alfabética.

``` sql
select idSupplier, socialName, CNPJ
from supplier
order by socialName asc;
```

### 5. Agrupamento e filtros de grupo (`HAVING`)

Exemplo: clientes com mais de 1 pedido.

``` sql
select c.Fname, c.Lname, count(o.idOrder) as total_pedidos
from client c
join orders o on c.idClient = o.idOrderClient
group by c.idClient
having count(o.idOrder) > 1;
```

### 6. Junções (`JOIN`)

Exemplo: relação de produtos, fornecedores e estoques.

``` sql
select 
    p.Pname as Produto,
    s.socialName as Fornecedor,
    ps.quantity as QtdeFornecedor,
    st.storageLocation,
    st.quantity as QtdeEstoque
from product p
join productSupplier ps on p.idProduct = ps.idPsProduct
join supplier s on s.idSupplier = ps.idPsSupplier
left join productStorage st on st.idProdStorage = ps.idPsSupplier;
```

------------------------------------------------------------------------

## ❓ Perguntas de Negócio Respondidas

-   Quantos pedidos foram feitos por cada cliente?\
-   Algum vendedor também é fornecedor?\
-   Qual a relação de produtos, fornecedores e estoques?\
-   Quais os nomes dos fornecedores e seus respectivos produtos?\
-   Qual o status e rastreio das entregas realizadas?

------------------------------------------------------------------------

## 🚀 Tecnologias

-   **MySQL** -- modelagem e queries\
-   **MySQL Workbench** -- diagrama e manipulação visual\
-   **Git/GitHub** -- versionamento e compartilhamento do projeto

------------------------------------------------------------------------

🔗 Este repositório pode ser utilizado como base para estudos de
modelagem de bancos de dados relacionais, normalização e consultas SQL
de diferentes níveis de complexidade.
