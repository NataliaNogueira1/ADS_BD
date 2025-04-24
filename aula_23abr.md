# Data Query Language

## DQL

```sql

SELECT [campos]
FROM [tabelas]
JOIN [tabelas] ON [relacionamentos]
WHERE [criterios]
GROUP BY [campos]
HAVING [criterios]
ORDER BY [campos]

```

## Exercícios
### Consultas Básicas

1. Listar todos os clientes pessoa física com seus nomes e CPFs.

```sql
    SELECT nome, cpf FROM pessoa_fisica ORDER BY nome;
```

2. Exibir o nome fantasia e CNPJ de todas pessoas jurídicas cadastradas.

```sql
    SELECT nome_fantasia, cnpj FROM pessoa_juridica ORDER BY nome_fantasia;
```

3. Listar todos os emails cadastrados no sistema.

```sql
    SELECT email FROM email;
```

4. Exibir todos os endereços do tipo "Residencial", mostrando logradouro, número e cidade.

```sql
    SELECT logradouro, numero, cidade FROM endereco WHERE tipo='Residencial';
```

5. Listar todos os números de telefone do tipo "Movel".

```sql
    SELECT numero FROM telefone WHERE tipo='Movel';
```

### Consultas Intermediárias

6. Listar o nome e CPF de todas as pessoas físicas ativas.

```sql
    SELECT nome, cpf FROM pessoa_fisica INNER JOIN cliente ON pessoa_fisica.id = cliente.id WHERE ativo = true;
```

7. Listar o nome fantasia e a quantidade de telefones cadastrados por pessoa jurídica.

```sql
    SELECT pj.nome_fantasia, COUNT(t.numero) AS quantidade
    FROM pessoa_juridica pj 
    INNER JOIN cliente c ON pj.id = c.id
    LEFT JOIN telefone t ON t.cliente_id = c.id
    GROUP BY pj.nome_fantasia;
```

8. Exibir o nome (PF ou PJ), o tipo de cliente e a cidade de todos os clientes que possuem endereço.

```sql
    SELECT DISTINCT 
	COALESCE(pessoa_fisica.nome, pessoa_juridica.nome_fantasia) AS nome, 
	CASE
		WHEN pessoa_fisica.id IS NOT NULL THEN 'Pessoa física'
		WHEN pessoa_juridica.id IS NOT NULL THEN 'Pessoa Juridíca'
	END AS tipo_cliente,
	endereco.cidade
    FROM cliente
    LEFT JOIN pessoa_fisica ON cliente.id = pessoa_fisica.id
    LEFT JOIN pessoa_juridica ON cliente.id = pessoa_juridica.id
    INNER JOIN endereco ON cliente.id = endereco.cliente_id;
```

9. Exibir o nome e email de todas as pessoas físicas que possuem mais de um email cadastrado.

```sql
    SELECT nome, email
    FROM pessoa_fisica
	INNER JOIN cliente ON cliente.id = pessoa_fisica.id
	INNER JOIN email ON email.cliente_id = cliente.id;
```

10. Listar todos os clientes (PF e PJ) que não possuem nenhum telefone cadastrado.

```sql
    SELECT
	COALESCE(pessoa_fisica.nome, pessoa_juridica.nome_fantasia) AS nome, 
	CASE
		WHEN pessoa_fisica.id IS NOT NULL THEN 'Pessoa física'
		WHEN pessoa_juridica.id IS NOT NULL THEN 'Pessoa Juridíca'
	END AS tipo_cliente
	FROM cliente
    LEFT JOIN pessoa_fisica ON cliente.id = pessoa_fisica.id
    LEFT JOIN pessoa_juridica ON cliente.id = pessoa_juridica.id
    LEFT JOIN telefone ON cliente.id = telefone.cliente_id
	WHERE telefone.id IS NULL;
```