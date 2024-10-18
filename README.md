# Replique-a-modelagem-do-projeto-l-gico-de-banco-de-dados-para-o-cen-rio-de-e-commerce.
Replique a modelagem do projeto lógico de banco de dados para o cenário de e-commerce. Fique atento as definições de chave primária e estrangeira, assim como as constraints presentes no cenário modelado. Perceba que dentro desta modelagem haverá relacionamentos presentes no modelo EER. Sendo assim, consulte como proceder para estes casos. 

modelagem do Banco de Dados
Entidades Principais:

Cliente: Pode ser Pessoa Física (PF) ou Pessoa Jurídica (PJ).
Pedido: Relaciona-se com clientes, produtos e pagamentos.
Produto: Tem informações sobre fornecedores e estoque.
Fornecedor: Pode fornecer um ou mais produtos.
Pagamento: Relaciona-se aos pedidos e pode ter múltiplas formas de pagamento.
Entrega: Relacionada aos pedidos, possui status e código de rastreio.
Relacionamentos:

Um cliente pode fazer vários pedidos.
Um pedido pode conter vários produtos.
Um produto pode ter múltiplos fornecedores.
Um pedido pode ter múltiplas formas de pagamento.
Um pedido pode ter uma única entrega.
Modelo EER:

Para a modelagem EER, adicione heranças e generalizações para PF e PJ no Cliente.
Definição de Chaves:

Chave primária (PK) em cada tabela.
Chave estrangeira (FK) para relacionamentos.
2. Script SQL para Criação do Banco de Dados
Aqui está um exemplo do script SQL para a criação do esquema do banco de dados:

sql
Copiar código
CREATE TABLE Cliente (
    cliente_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    tipo ENUM('PF', 'PJ') NOT NULL,
    cpf_cnpj VARCHAR(14) UNIQUE NOT NULL,
    endereco VARCHAR(255),
    telefone VARCHAR(20)
);

CREATE TABLE Fornecedor (
    fornecedor_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    contato VARCHAR(255)
);

CREATE TABLE Produto (
    produto_id INT PRIMARY KEY AUTO_INCREMENT,
    nome VARCHAR(255) NOT NULL,
    preco DECIMAL(10, 2) NOT NULL,
    estoque INT NOT NULL,
    fornecedor_id INT,
    FOREIGN KEY (fornecedor_id) REFERENCES Fornecedor(fornecedor_id)
);

CREATE TABLE Pedido (
    pedido_id INT PRIMARY KEY AUTO_INCREMENT,
    cliente_id INT,
    data_pedido DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (cliente_id) REFERENCES Cliente(cliente_id)
);

CREATE TABLE Pagamento (
    pagamento_id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    tipo_pagamento VARCHAR(50),
    valor DECIMAL(10, 2) NOT NULL,
    FOREIGN KEY (pedido_id) REFERENCES Pedido(pedido_id)
);

CREATE TABLE Entrega (
    entrega_id INT PRIMARY KEY AUTO_INCREMENT,
    pedido_id INT,
    status VARCHAR(50),
    codigo_rastreio VARCHAR(50),
    FOREIGN KEY (pedido_id) REFERENCES Pedido(pedido_id)
);
3. Persistência de Dados
Para testar o banco de dados, você pode inserir dados de exemplo. Por exemplo:

sql
Copiar código
INSERT INTO Cliente (nome, tipo, cpf_cnpj, endereco, telefone) VALUES
('Maria Silva', 'PF', '12345678909', 'Rua A, 123', '1234-5678'),
('Empresa X', 'PJ', '12345678000195', 'Av. B, 456', '9876-5432');

INSERT INTO Fornecedor (nome, contato) VALUES
('Fornecedor A', 'fornecedorA@exemplo.com'),
('Fornecedor B', 'fornecedorB@exemplo.com');

INSERT INTO Produto (nome, preco, estoque, fornecedor_id) VALUES
('Produto 1', 100.00, 10, 1),
('Produto 2', 150.00, 20, 2);

INSERT INTO Pedido (cliente_id) VALUES
(1), (2);

INSERT INTO Pagamento (pedido_id, tipo_pagamento, valor) VALUES
(1, 'Cartão de Crédito', 100.00),
(2, 'Boleto', 150.00);

INSERT INTO Entrega (pedido_id, status, codigo_rastreio) VALUES
(1, 'Em Rota', 'ABC123'),
(2, 'Entregue', 'XYZ456');
4. Exemplos de Queries SQL
Aqui estão algumas queries que podem ser realizadas:

1. Quantos pedidos foram feitos por cada cliente?

sql
Copiar código
SELECT c.nome, COUNT(p.pedido_id) AS total_pedidos
FROM Cliente c
LEFT JOIN Pedido p ON c.cliente_id = p.cliente_id
GROUP BY c.nome;
2. Algum vendedor também é fornecedor?

sql
Copiar código
SELECT DISTINCT f.nome
FROM Fornecedor f
JOIN Produto pr ON f.fornecedor_id = pr.fornecedor_id;
3. Relação de produtos, fornecedores e estoques:

sql
Copiar código
SELECT p.nome AS produto, f.nome AS fornecedor, p.estoque
FROM Produto p
JOIN Fornecedor f ON p.fornecedor_id = f.fornecedor_id;
4. Relação de nomes dos fornecedores e nomes dos produtos:

sql
Copiar código
SELECT f.nome AS fornecedor, p.nome AS produto
FROM Fornecedor f
JOIN Produto p ON f.fornecedor_id = p.fornecedor_id;
5. Filtros com WHERE e ordenação com ORDER BY:

sql
Copiar código
SELECT nome, preco
FROM Produto
WHERE estoque > 0
ORDER BY preco DESC;
6. Condições de filtros aos grupos – HAVING:

sql
Copiar código
SELECT f.nome, COUNT(p.produto_id) AS total_produtos
FROM Fornecedor f
JOIN Produto p ON f.fornecedor_id = p.fornecedor_id
GROUP BY f.nome
HAVING total_produtos > 1;
7. Junções entre tabelas:

sql
Copiar código
SELECT c.nome AS cliente, p.data_pedido, e.status
FROM Cliente c
JOIN Pedido p ON c.cliente_id = p.cliente_id
JOIN Entrega e ON p.pedido_id = e.pedido_id;
