# Questão 1
Salvando resposta

Valor da questão: 1,50

Explique que alterações no esquema relacional abaixo poderiam ser feitas para acrescentar informações sobre o histórico de gerentes (informando o início e o fim da gestão do gerente) do departamento e informação do gerente atual.
﻿
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, depto, supervisor)
departamento(codigo, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, titulo, projeto, descrição, data_inicio, data_fim)


Deveria ser criado uma nova tabela com nome "historicoGerentes", armazenando o histórico de todos os gerentes que já participou de determinado departamento. Sendo assim, os atributos dessa tabela poderiam ser: 
  
- codigo (chave primária),
- depto (chave estrangeira que referencia a tabela de departamento),
- gerente (chave estrangeira que referencia a tabela de funcionario),
- data_inicio,
- data_fim (para indicar que o gerente está no cargo, pode utilizar null)

Assim, todos os gerentes que passarem por determinado departamento, ficará salva seu registro e não será excluído, quando ele sair do cargo.



# Questão 2
Valor da questão: 1,00

Faça uma sequência numérica em SQL, que começa em 1 e incrementando em 1 a cada execução. 
Depois, Crie uma tabela Contato que contenha os atributos codigo, nome, dt_nasc. Faça o código utilizar a sequência para incrementar o valor (equivalente a usar serial).
~~~sql
-- Criando a sequencia numérica
CREATE SEQUENCE sequencia_numerica
    START WITH 1
    INCREMENT BY 1;

-- Criando a tabela
CREATE TABLE Contato (
    codigo INT DEFAULT nextval('sequencia_numerica'),
    nome VARCHAR(100),
    dt_nasc DATE,
	PRIMARY KEY (codigo)
)

-- Inserindo os dados para teste
INSERT INTO Contato (nome, dt_nasc) VALUES ('Anderson', '2002-08-31');
~~~



# Questão 3
Resposta salva

Valor da questão: 1,50

Faça uma consulta que selecione o nome, o salário e o departamento dos funcionários que são gerentes ou supervisores, ordenados pelo nome do funcionário.
﻿
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, depto, supervisor)
departamento(codigo, nome, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, titulo, projeto, descrição, data_inicio, data_fim)

~~~sql
SELECT f.nome, f.salario, f.depto
FROM funcionario f
WHERE f.codigo IN (
    SELECT gerente
    FROM departamento
    UNION
    SELECT supervisor
    FROM funcionario
)
ORDER BY f.nome
~~~

# Questão 4
Valor da questão: 1,00

Crie uma visão que forneça o código do funcionário, o nome do funcionário, o código do departamento e a sigla do departamento.
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, supervisor, depto)
dependente(codigo, nome, dt_nasc, funcionario)
departamento(codigo, nome, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, nome, descrição, projeto, data_inicio, data_fim)

~~~sql
-- Criando view
CREATE VIEW Funcionario_Departamento_View AS
SELECT
    f.codigo AS codigo_funcionario,
    f.nome AS nome_funcionario,
    f.depto AS codigo_departamento,
    d.sigla AS sigla_departamento
FROM funcionario f LEFT JOIN departamento d 
	ON f.depto = d.codigo

-- Testando a view
SELECT * FROM Funcionario_Departamento_View
~~~




# Questão 5
Valor da questão: 1,00

Faça uma consulta que selecione o nome do funcionário, o salário do funcionário, o nome do supervisor, e o salário do supervisor, todos os funcionários devem ser listados mesmo que eles não tenham supervisores.
﻿
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, depto, supervisor)
departamento(codigo, nome, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, titulo, projeto, descrição, data_inicio, data_fim)

~~~sql
SELECT 
	f1.nome AS "Func.", 
	COALESCE(f1.salario, '') AS "Sal. Func.", 
	COALESCE(f2.nome, 'Sem supervisor') AS "Supe.", 
	COALESCE(f2.salario, '') AS "Sal. Supe."
FROM funcionario f1 LEFT JOIN funcionario f2
ON f1.supervisor = f2.codigo
ORDER BY f1.nome
~~~

#  Questão 6
Valor da questão: 1,50

Faça uma consulta que selecione o nome do funcionário, a sigla do departamento, o nome do supervisor, a sigla do departamento do supervisor, todos os funcionários devem ser listados mesmo que eles não tenham supervisores. Faça uma view para listar o código do supervisor, o nome do supervisor, código do departamento e a sigla do departamento. Utilize a view na sua consulta.
﻿
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, depto, supervisor)
departamento(codigo, nome, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, titulo, projeto, descrição, data_inicio, data_fim)

~~~sql
-- Criando a view
CREATE VIEW Supervisor_Departamento_View AS
SELECT 
    f.codigo AS codigo_supervisor,
    f.nome AS nome_supervisor,
    f.depto AS codigo_departamento,
    d.sigla AS sigla_departamento
FROM funcionario f JOIN departamento d 
	ON f.depto = d.codigo

-- Criando a consulta
SELECT 
    f1.nome AS nome_funcionario,
    d1.sigla AS sigla_departamento,
    f2.nome AS nome_supervisor,
    d2.sigla AS sigla_departamento_supervisor
FROM funcionario f1 LEFT JOIN departamento d1 
	ON f1.depto = d1.codigo
LEFT JOIN Supervisor_Departamento_View sdv 
	ON f1.supervisor = sdv.codigo_supervisor
LEFT JOIN funcionario f2 
	ON f1.supervisor = f2.codigo
LEFT JOIN departamento d2 
	ON f2.depto = d2.codigo
~~~


# Questão 7
Valor da questão: 2,50

Faça uma consulta que selecione a sigla do departamento, o nome gerente, a quantidade de projetos e a quantidade de atividades do departamento. Faça uma view para calcular a quantidade de atividades de um projeto, que retorne o código do projeto, o nome do projeto, o código do departamento do projeto e o número de atividades do projeto.
﻿
Considere o seguinte esquema relacional (chave primária estão sublinhadas e chaves estrangeiras estão em negrito):
﻿
funcionario(codigo, nome, sexo, dt_nasc, salario, depto, supervisor)
departamento(codigo, nome, sigla, descricao, gerente)
projeto(codigo, nome, descricao, depto, responsavel, data_inicio, data_fim) 
atividade(codigo, titulo, projeto, descrição, data_inicio, data_fim)


~~~sql
-- Criando consulta
SELECT 
	d.sigla AS Sigla_Dep,
	f.nome AS Gerente,
	COUNT(DISTINCT p.codigo) AS Quant_Projetos,
	COUNT(a.codigo) AS Quant_Atividades
FROM departamento d JOIN funcionario f 
	ON d.gerente = f.codigo
LEFT JOIN projeto p 
	ON d.codigo = p.depto
LEFT JOIN atividade a 
	ON p.codigo = a.projeto
GROUP BY d.sigla, f.nome

-- Criando view	
CREATE VIEW Atividades_Projeto AS
SELECT
	p.codigo AS Cod_Projeto,
	p.nome AS Projeto,
	p.depto AS Cod_Depto_Projeto,
	COUNT(a.codigo) AS Numero_Atividades_Projeto
FROM projeto p LEFT JOIN atividade a 
	ON p.codigo = a.projeto
GROUP BY p.codigo, p.nome, p.depto

-- Testando a view
SELECT * FROM Atividades_Projeto
~~~