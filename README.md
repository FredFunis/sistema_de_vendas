# 🛒 Mini Projeto SQL - Sistema de Vendas

Este projeto tem como objetivo simular um **Sistema de Vendas**
utilizando **MySQL**, incluindo modelagem de dados, inserção de
informações fictícias, consultas analíticas e uso de **Views, Functions
e Procedures**.

O sistema foi projetado para controlar **clientes, produtos, pedidos e
itens de pedido**, permitindo análises de vendas e relatórios.

------------------------------------------------------------------------

## 🗂 Criação do Banco de Dados

``` sql
DROP DATABASE IF EXISTS SistemaVendas;
CREATE DATABASE SistemaVendas;
USE SistemaVendas;
```

------------------------------------------------------------------------

## 🏗 Estrutura das Tabelas

### 1. Tabela `Clientes`

``` sql
CREATE TABLE Clientes (
  ClienteID INT PRIMARY KEY AUTO_INCREMENT,
  Nome VARCHAR(100) NOT NULL,
  Email VARCHAR(100) UNIQUE,
  DataCadastro DATE DEFAULT CURDATE()
);
```

### 2. Tabela `Produtos`

``` sql
CREATE TABLE Produtos (
  ProdutoID INT PRIMARY KEY AUTO_INCREMENT,
  Nome VARCHAR(100) NOT NULL,
  Preco DECIMAL(10,2) NOT NULL,
  Estoque INT NOT NULL
);
```

### 3. Tabela `Pedidos`

``` sql
CREATE TABLE Pedidos (
  PedidoID INT PRIMARY KEY AUTO_INCREMENT,
  ClienteID INT,
  DataPedido DATETIME DEFAULT NOW(),
  FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);
```

### 4. Tabela `ItensPedido`

``` sql
CREATE TABLE ItensPedido (
  ItemID INT PRIMARY KEY AUTO_INCREMENT,
  PedidoID INT,
  ProdutoID INT,
  Quantidade INT NOT NULL,
  FOREIGN KEY (PedidoID) REFERENCES Pedidos(PedidoID),
  FOREIGN KEY (ProdutoID) REFERENCES Produtos(ProdutoID)
);
```

------------------------------------------------------------------------

## 📥 Inserindo Dados Fictícios

### Clientes

``` sql
INSERT INTO Clientes (Nome, Email) VALUES
('João Silva', 'joao@email.com'),
('Maria Souza', 'maria@email.com'),
('Carlos Pereira', 'carlos@email.com');
```

### Produtos

``` sql
INSERT INTO Produtos (Nome, Preco, Estoque) VALUES
('Café em pó 500g', 12.50, 50),
('Leite 1L', 5.80, 80),
('Açúcar 1kg', 4.20, 100),
('Biscoito recheado', 3.50, 60);
```

### Pedidos

``` sql
INSERT INTO Pedidos (ClienteID) VALUES (1), (2);
```

### Itens de Pedido

``` sql
INSERT INTO ItensPedido (PedidoID, ProdutoID, Quantidade) VALUES
(1, 1, 2), -- João comprou 2 cafés
(1, 3, 1), -- João comprou 1 açúcar
(2, 2, 3), -- Maria comprou 3 leites
(2, 4, 2); -- Maria comprou 2 biscoitos
```

------------------------------------------------------------------------

## 🔎 Consultas Analíticas

### Total gasto por cliente

``` sql
SELECT c.Nome, SUM(p.Preco * i.Quantidade) AS TotalGasto
FROM Clientes c
JOIN Pedidos pe ON c.ClienteID = pe.ClienteID
JOIN ItensPedido i ON pe.PedidoID = i.PedidoID
JOIN Produtos p ON i.ProdutoID = p.ProdutoID
GROUP BY c.Nome;
```

### Produtos mais vendidos

``` sql
SELECT p.Nome, SUM(i.Quantidade) AS TotalVendido
FROM ItensPedido i
JOIN Produtos p ON i.ProdutoID = p.ProdutoID
GROUP BY p.Nome
ORDER BY TotalVendido DESC;
```

### Ticket médio dos clientes

``` sql
SELECT c.Nome, AVG(p.Preco * i.Quantidade) AS TicketMedio
FROM Clientes c
JOIN Pedidos pe ON c.ClienteID = pe.ClienteID
JOIN ItensPedido i ON pe.PedidoID = i.PedidoID
JOIN Produtos p ON i.ProdutoID = p.ProdutoID
GROUP BY c.Nome;
```

------------------------------------------------------------------------

## 🛠 Recursos Avançados

### VIEW: Relatório de Vendas

``` sql
CREATE OR REPLACE VIEW RelatorioVendas AS
SELECT c.Nome AS Cliente,
       p.Nome AS Produto,
       i.Quantidade,
       (p.Preco * i.Quantidade) AS ValorTotal,
       pe.DataPedido
FROM ItensPedido i
JOIN Produtos p ON i.ProdutoID = p.ProdutoID
JOIN Pedidos pe ON i.PedidoID = pe.PedidoID
JOIN Clientes c ON pe.ClienteID = c.ClienteID;
```

### FUNCTION: Estoque Restante

``` sql
DELIMITER //
CREATE FUNCTION EstoqueRestante(produto INT) RETURNS INT
DETERMINISTIC
BEGIN
  DECLARE qtd_vendida INT;
  SELECT COALESCE(SUM(Quantidade),0) INTO qtd_vendida
  FROM ItensPedido
  WHERE ProdutoID = produto;

  RETURN (SELECT Estoque FROM Produtos WHERE ProdutoID = produto) - qtd_vendida;
END//
DELIMITER ;
```

### PROCEDURE: Novo Pedido

``` sql
DELIMITER //
CREATE PROCEDURE NovoPedido(IN cliente INT, IN produto INT, IN qtd INT)
BEGIN
  DECLARE novoPedidoID INT;

  -- Cria pedido
  INSERT INTO Pedidos (ClienteID) VALUES (cliente);
  SET novoPedidoID = LAST_INSERT_ID();

  -- Adiciona item
  INSERT INTO ItensPedido (PedidoID, ProdutoID, Quantidade)
  VALUES (novoPedidoID, produto, qtd);
END//
DELIMITER ;
```

------------------------------------------------------------------------

## 🚀 Tecnologias Utilizadas

-   **MySQL**
-   **SQL (DDL, DML, DQL)**
-   Views, Functions, Procedures
-   Modelagem de dados relacional aplicada a vendas

------------------------------------------------------------------------

## 🎯 Objetivo do Projeto

-   Criar um modelo de banco de dados para **gestão de vendas**\
-   Simular **clientes, produtos e pedidos**\
-   Utilizar consultas SQL para **análises de vendas e relatórios
    gerenciais**

------------------------------------------------------------------------

## 📖 Próximos Passos

-   Criar **dashboards em Power BI** conectados ao banco de dados\
-   Expandir o sistema para incluir **formas de pagamento e descontos**\
-   Adicionar **módulos de estoque avançado e relatórios financeiros**

------------------------------------------------------------------------

👤 **Autor:** [Frederico Feitosa
Funis](www.linkedin.com/in/frederico-funis-952123215)\
📧 **Contato:** disponível via LinkedIn
