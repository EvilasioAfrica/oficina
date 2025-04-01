# oficina
Projeto lógico de banco de dados do zero
-- Criando o banco de dados
CREATE DATABASE OficinaDB;
USE OficinaDB;

-- Criando a tabela de Clientes
CREATE TABLE Clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    telefone VARCHAR(15),
    email VARCHAR(100) UNIQUE,
    endereco VARCHAR(255)
);

-- Criando a tabela de Veículos
CREATE TABLE Veiculos (
    id_veiculo INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT,
    marca VARCHAR(50) NOT NULL,
    modelo VARCHAR(50) NOT NULL,
    ano INT NOT NULL,
    placa VARCHAR(10) UNIQUE NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES Clientes(id_cliente)
);

-- Criando a tabela de Serviços
CREATE TABLE Servicos (
    id_servico INT AUTO_INCREMENT PRIMARY KEY,
    descricao VARCHAR(255) NOT NULL,
    preco DECIMAL(10,2) NOT NULL
);

-- Criando a tabela de Ordens de Serviço
CREATE TABLE OrdensServico (
    id_ordem INT AUTO_INCREMENT PRIMARY KEY,
    id_veiculo INT,
    data_abertura DATE NOT NULL,
    data_fechamento DATE,
    status ENUM('Aberto', 'Em andamento', 'Concluído', 'Cancelado') NOT NULL,
    FOREIGN KEY (id_veiculo) REFERENCES Veiculos(id_veiculo)
);

-- Criando a tabela de Serviços por Ordem de Serviço
CREATE TABLE ServicosOrdem (
    id_servicos_ordem INT AUTO_INCREMENT PRIMARY KEY,
    id_ordem INT,
    id_servico INT,
    quantidade INT NOT NULL,
    preco_total DECIMAL(10,2) NOT NULL,
    FOREIGN KEY (id_ordem) REFERENCES OrdensServico(id_ordem),
    FOREIGN KEY (id_servico) REFERENCES Servicos(id_servico)
);

-- Inserindo dados para teste
INSERT INTO Clientes (nome, telefone, email, endereco) VALUES
('Carlos Silva', '11987654321', 'carlos@email.com', 'Rua A, 123'),
('Mariana Souza', '11923456789', 'mariana@email.com', 'Rua B, 456');

INSERT INTO Veiculos (id_cliente, marca, modelo, ano, placa) VALUES
(1, 'Toyota', 'Corolla', 2018, 'ABC-1234'),
(2, 'Honda', 'Civic', 2020, 'DEF-5678');

INSERT INTO Servicos (descricao, preco) VALUES
('Troca de óleo', 100.00),
('Alinhamento e balanceamento', 150.00),
('Revisão completa', 500.00);

INSERT INTO OrdensServico (id_veiculo, data_abertura, status) VALUES
(1, '2025-03-01', 'Aberto'),
(2, '2025-03-05', 'Concluído');

INSERT INTO ServicosOrdem (id_ordem, id_servico, quantidade, preco_total) VALUES
(1, 1, 1, 100.00),
(2, 2, 1, 150.00),
(2, 3, 1, 500.00);

-- Consultas SQL complexas

-- 1. Recuperação simples com SELECT
SELECT * FROM Clientes;

-- 2. Filtros com WHERE
SELECT * FROM Veiculos WHERE ano > 2019;

-- 3. Criando expressões para atributos derivados (total gasto por cliente)
SELECT C.nome, SUM(SO.preco_total) AS total_gasto
FROM Clientes C
JOIN Veiculos V ON C.id_cliente = V.id_cliente
JOIN OrdensServico OS ON V.id_veiculo = OS.id_veiculo
JOIN ServicosOrdem SO ON OS.id_ordem = SO.id_ordem
GROUP BY C.nome;

-- 4. Ordenação dos dados com ORDER BY
SELECT * FROM OrdensServico ORDER BY data_abertura DESC;

-- 5. Filtro nos grupos com HAVING (clientes que gastaram acima de R$200)
SELECT C.nome, SUM(SO.preco_total) AS total_gasto
FROM Clientes C
JOIN Veiculos V ON C.id_cliente = V.id_cliente
JOIN OrdensServico OS ON V.id_veiculo = OS.id_veiculo
JOIN ServicosOrdem SO ON OS.id_ordem = SO.id_ordem
GROUP BY C.nome
HAVING total_gasto > 200;

-- 6. Junção entre tabelas para visão mais complexa
SELECT OS.id_ordem, C.nome AS cliente, V.modelo AS veiculo, OS.status, S.descricao AS servico
FROM OrdensServico OS
JOIN Veiculos V ON OS.id_veiculo = V.id_veiculo
JOIN Clientes C ON V.id_cliente = C.id_cliente
JOIN ServicosOrdem SO ON OS.id_ordem = SO.id_ordem
JOIN Servicos S ON SO.id_servico = S.id_servico;
