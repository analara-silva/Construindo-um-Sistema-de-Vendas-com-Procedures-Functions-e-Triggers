# Projeto: Construindo um Sistema de Vendas com Procedures, Functions e Triggers

## Objetivo

O objetivo deste projeto é construir um sistema de vendas para um e-commerce fictício, utilizando **procedures**, **functions** e **triggers** para automação de processos. O sistema deverá realizar as seguintes operações de forma automatizada:

- Verificação da disponibilidade de estoque.
- Registro de vendas e pagamentos.
- Aplicação de descontos.
- Manutenção de histórico de mudanças no estoque.

---

## Passo a Passo

### 1. Introdução

**Leitura do material teórico**: Todos os alunos devem ler o material fornecido sobre como usar **procedures**, **functions** e **triggers** no contexto de um banco de dados relacional.

**Objetivo do projeto**: Criar um sistema que utilize essas três ferramentas para automatizar processos de vendas, incluindo:

- Verificação de estoque.
- Registros de vendas e pagamentos.
- Manutenção de histórico de mudanças no estoque.

### 2. Divisão em Grupos

Divida a turma em **6 grupos**, com cada grupo responsável por uma parte do sistema. Cada grupo pode ter **4 alunos**, que serão responsáveis por diferentes funcionalidades do sistema de vendas.

---

## Resolução

Faço parte do grupo 2 e aqui esta a resolução:

`DELIMITER //
CREATE PROCEDURE registrar_venda(
	IN id_produto INT,
	IN quantidade int,
	IN id_cliente INT,
	IN tipo_pagamento INT
)
 BEGIN
	DECLARE total DECIMAL(10, 2);
	DECLARE estoque_suficiente BOOLEAN;
	DECLARE nova_quantidade_estoque INT;
	declare id_venda INT;
 SET estoque_suficiente = verificar_estoque(id_produto, quantidade);
 if NOT estoque_suficiente then
	SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = 'Estoque insuficiente!';
END if;
 SET total = calcular_total_venda(id_produto, quantidade);
 INSERT INTO venda (id_produto, quantidade, id_cliente, data_venda)
VALUES (id_produto, quantidade, id_cliente, NOW());
 SET id_venda = LAST_INSERT_ID();
 INSERT INTO pagamento (id_venda, valor_pago, data_pagamento)
VALUES (id_venda, total, NOW());
 SELECT quantidade_estoque INTO nova_quantidade_estoque
   FROM produtos
   WHERE id_produto = id_produto;
 UPDATE produtos
  SET quantidade_estoque = nova_quantidade_estoque - quantidade
   WHERE id_produto = id_produto;
 INSERT INTO estoque_histórico (id_produto, quantidade_alterada, data_alteracao)
    VALUES (id_produto, -quantidade, NOW());
END //
 DELIMITER ;`
