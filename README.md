# desafio-banco-de-dados

Segue a Query para gerar o banco de dados é para popular

-- ==============================
-- Criar banco de dados
-- ==============================

drop database if exists ecommerce;
create database ecommerce;
use ecommerce;

-- ==============================
-- CRIAÇÃO DAS TABELAS
-- ==============================

create table client (
    idClient int auto_increment primary key,
    Fname varchar(45) not null,
    Minit char(1),
    Lname varchar(45),
    CPF char(11) not null unique,
    Address varchar(100)
);

create table clientPF (
    idClientPF int primary key,
    CPF char(11) not null unique,
    birthDate date,
    foreign key (idClientPF) references client(idClient)
);

create table clientPJ (
    idClientPJ int primary key,
    CNPJ char(15) not null unique,
    socialReason varchar(100),
    foreign key (idClientPJ) references client(idClient)
);

create table payments (
    idPayment int auto_increment primary key,
    typePayment enum('Boleto','Cartão','Pix','Dois cartões') not null
);

create table orders (
    idOrder int auto_increment primary key,
    idOrderClient int,
    idPayment int,
    orderStatus enum('Em processamento','Confirmado','Cancelado') not null,
    orderDescription varchar(255),
    sendValue float default 10,
    foreign key (idOrderClient) references client(idClient),
    foreign key (idPayment) references payments(idPayment)
);

create table delivery (
    idDelivery int auto_increment primary key,
    idOrder int,
    deliveryStatus enum('A Caminho','Entregue','Atrasado','Cancelado') not null,
    trackingCode varchar(50),
    foreign key (idOrder) references orders(idOrder)
);

create table product (
    idProduct int auto_increment primary key,
    Pname varchar(100) not null,
    classification_kids bool default false,
    category enum('Eletrônico','Vestuário','Brinquedos','Alimentos','Móveis') not null,
    avaliação float default 0,
    size varchar(10)
);

create table productStorage (
    idProdStorage int auto_increment primary key,
    storageLocation varchar(100),
    quantity int default 0
);

create table supplier (
    idSupplier int auto_increment primary key,
    socialName varchar(100) not null,
    CNPJ char(15) not null unique,
    contact varchar(45)
);

create table seller (
    idSeller int auto_increment primary key,
    socialName varchar(100) not null,
    AbstName varchar(100),
    CNPJ char(15) unique,
    CPF char(11) unique,
    location varchar(100),
    contact varchar(45)
);

create table productSupplier (
    idPsSupplier int,
    idPsProduct int,
    quantity int not null,
    primary key (idPsSupplier, idPsProduct),
    foreign key (idPsSupplier) references supplier(idSupplier),
    foreign key (idPsProduct) references product(idProduct)
);

create table productOrder (
    idPOproduct int,
    idPOorder int,
    prodQuantity int default 1,
    primary key (idPOproduct, idPOorder),
    foreign key (idPOproduct) references product(idProduct),
    foreign key (idPOorder) references orders(idOrder)
);

-- ==============================
-- INSERÇÃO DE DADOS
-- ==============================

-- CLIENTES (PF)
insert into client (Fname, Minit, Lname, CPF, Address) values
('João','A','Silva','12345678901','Rua A, 100'),
('Maria','B','Oliveira','23456789012','Rua B, 200'),
('Carlos','C','Pereira','34567890123','Rua C, 300');

insert into clientPF (idClientPF, CPF, birthDate) values
(1,'12345678901','1990-05-12'),
(2,'23456789012','1985-08-25'),
(3,'34567890123','1978-02-10');

-- CLIENTES (PJ)
insert into client (Fname, Minit, Lname, CPF, Address) values
('Empresa','X','Tecnologia','00000000000','Av. Paulista, 1000'),
('Comércio','Y','Alimentos','00000000001','Rua das Indústrias, 200');

insert into clientPJ (idClientPJ, CNPJ, socialReason) values
(4,'999999999999999','Empresa X Tecnologia LTDA'),
(5,'888888888888888','Comércio Y Alimentos SA');

-- PAGAMENTOS
insert into payments (typePayment) values 
('Boleto'),
('Cartão'),
('Pix'),
('Dois cartões');

-- PRODUTOS
insert into product (Pname, classification_kids, category, avaliação, size) values
('Notebook', false, 'Eletrônico', 4.8, '15'),
('Camiseta', false, 'Vestuário', 4.5, 'M'),
('Boneca', true, 'Brinquedos', 4.7, 'Único'),
('Sofá', false, 'Móveis', 4.2, '3L');

-- ESTOQUE
insert into productStorage (storageLocation, quantity) values
('Centro SP', 50),
('RJ Depósito', 30),
('MG Depósito', 20);

-- FORNECEDORES
insert into supplier (socialName, CNPJ, contact) values
('Tech Eletrônicos','111111111111111','tech@contato.com'),
('Moda Brasil','222222222222222','moda@contato.com'),
('Brinquedos SA','333333333333333','brinq@contato.com'),
('Moveis LTDA','444444444444444','moveis@contato.com');

-- VENDEDORES
insert into seller (socialName, AbstName, CNPJ, CPF, location, contact) values
('Loja 1 Vendas','Loja1','555555555555555',null,'São Paulo','loja1@contato.com'),
('Loja 2 Vendas','Loja2',null,'99999999999','Rio de Janeiro','loja2@contato.com');

-- PRODUTOS-FORNECEDORES
insert into productSupplier (idPsSupplier, idPsProduct, quantity) values
(1,1,10),
(2,2,50),
(3,3,40),
(4,4,15);

-- PEDIDOS
insert into orders (idOrderClient, idPayment, orderStatus, orderDescription, sendValue) values
(1,2,'Confirmado','Compra de notebook e camiseta',20),
(2,2,'Em processamento','Compra de boneca',15),
(3,4,'Confirmado','Compra de sofá',50);

-- PRODUTOS-PEDIDOS
insert into productOrder (idPOproduct, idPOorder, prodQuantity) values
(1,1,1),  -- João comprou 1 notebook
(2,1,2),  -- João comprou 2 camisetas
(3,2,1),  -- Maria comprou 1 boneca
(4,3,1);  -- Carlos comprou 1 sofá

-- ENTREGAS
insert into delivery (idOrder, deliveryStatus, trackingCode) values
(1,'Entregue','BR123456789BR'),
(2,'A Caminho','BR987654321BR'),
(3,'Atrasado','BR555444333BR');
