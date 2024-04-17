# Lista de Exercícios - Consultas Aninhadas e JOIN

#

### 1. Faça uma consulta que selecione o nome dos funcionários que recebem salários superiores aos salários pagos no departamento 2. 

~~~sql
SELECT f.nome, f.depto, f.salario
FROM funcionario f
WHERE f.salario > (
        SELECT max(salario)
        FROM funcionario
        WHERE depto = 2
    )   
~~~

#

### 2. Faça uma consulta que selecione o nome de todos os funcionários, exceto o mais idoso.

~~~sql
SELECT f.nome
FROM funcionario f
WHERE f.dt_nasc != (
        SELECT MIN(dt_nasc)
        FROM funcionario
    ) 
~~~

#

### 3. Faça uma consulta que selecione o nome e a data de nascimento dos funcionários com idade superior a 21 anos que não são gerentes.

~~~sql
SELECT f.nome, f.dt_nasc
FROM funcionario f 
WHERE NOT EXISTS (
	SELECT gerente
	FROM departamento d
	WHERE f.codigo = d.gerente
	) AND 21 < EXTRACT (YEAR FROM AGE(CURRENT_DATE, f.dt_nasc))
~~~

#

### 4. Faça uma consulta que selecione o nome, o salário e o departamento dos funcionários que não são gerentes, ordenando pelo Código do Departamento.

~~~sql
SELECT f.depto, f.nome, f.dt_nasc
FROM funcionario f 
WHERE f.codigo NOT IN (
	SELECT gerente
	FROM departamento d
	WHERE f.codigo = d.gerente
	)
ORDER BY f.depto
~~~

#

### 5. Responda as perguntas: **(A FAZER)**
- a) Explique os problemas de termos valores nulos nos dados.

- b) Explique o funcionamento do Right e do Left Join.

- c) Explique o funcionamento do Full Outer Join e como pode ser feito no MySQL ou MariaDB que não tem mais o comando Full.


#

### 6. Faça uma consulta que selecione o nome, o salário dos funcionários e a descrição do departamento, mesmo que eles não tenham departamentos associados.
~~~sql
SELECT d.descricao, f.nome, f.salario
FROM departamento d RIGHT JOIN funcionario f
ON f.depto = d.codigo
~~~

# 

### 10. Faça uma consulta que selecione o nome do projeto, o nome do departamento do projeto, o nome do funcionário responsável pelo projeto, o nome do departamento do funcionário responsável.

~~~sql
SELECT p.descricao AS "Projeto", d.descricao AS "Departamento", f.nome AS "Responsável", (
	SELECT dep.descricao AS "Depto. Func. Res"
	FROM departamento dep
	WHERE p.responsavel = f.codigo AND f.depto = dep.codigo
)
FROM projeto p, departamento d, funcionario f
WHERE p.depto = d.codigo AND p.responsavel = f.codigo 
~~~

#

### 11. Faça uma consulta que selecione o nome do projeto, o nome do departamento do projeto, o nome do funcionário responsável pelo projeto, o nome do departamento do funcionário responsável, mas apenas os projetos que o responsável é de outro departamento.

~~~sql
SELECT p.descricao AS "Projeto", d.descricao AS "Departamento", f.nome AS "Responsável", (
	SELECT dep.descricao AS "Depto. Func. Res"
	FROM departamento dep
	WHERE p.responsavel = f.codigo AND f.depto = dep.codigo
)
FROM projeto p, departamento d, funcionario f
WHERE p.depto = d.codigo AND p.responsavel = f.codigo AND f.depto != d.codigo
~~~

#

### 12. Faça uma consulta que selecione o nome do projeto, data de início e fim do projeto, a descrição da atividade do projeto, data de início e fim da atividade. **(NÃO TEM atividade_projeto)**



#

### 17. Faça uma consulta que selecione o código e descrição do projeto cujo gerente do departamento que ele faz parte ganhe mais que os outros gerentes de departamento.

~~~sql
SELECT p.codigo AS "Código", p.descricao AS "Projeto"
FROM projeto p, departamento d
WHERE p.depto IN (
	SELECT d.codigo AS "Departamento"
	FROM funcionario f, departamento d
	WHERE f.salario = (
		SELECT MAX(f.salario)
		FROM departamento d, funcionario f
		WHERE d.gerente = f.codigo
	) AND d.gerente = f.codigo
) AND p.depto = d.codigo
~~~

#

### 18. Faça uma consulta que selecione o nome dos funcionários com maior salário de seu departamento, mesmo que não tenha departamento associado, e o nome dos respectivos departamentos. Exiba em ordem crescente de acordo com o salário.
**(NÃO TERMINOU)**
~~~sql
SELECT f.nome, f.salario
FROM funcionario f LEFT JOIN departamento d
ON f.salario IN (
	SELECT MAX(f.salario)
	FROM departamento d RIGHT JOIN funcionario f
	ON f.depto IN (
		SELECT d.codigo
		FROM departamento d
	) AND d.codigo = f.depto
	GROUP BY d.codigo
) AND f.depto = d.codigo OR f.depto = null
~~~