# AVALIAÇÃO 1 DE SQL SERVER

Respostas da avaliação de sql server do professor Leandro


##  1 Liste todos os funcionarios.

Resposta:
`SELECT * FROM t_funcionarios;`

##  2 Liste todos os funcionarios que fazem parte do departamento 'CONSTRUÇÃO'

Resposta:
`SELECT 
	* 
	FROM [t_funcionarios]
	JOIN [t_departamento] 
	ON 
	[t_departamento].[n_idfuncionario_responsavel] = 
	[t_funcionarios].n_idfuncionario
	WHERE [c_nomedepto] = 'CONSTRUCAO'`


 ##  3 Liste todos os departamentos liderados por 'ANTONIO JOSE'.

 Resposta:
	`SELECT*
	FROM [t_departamento]
	JOIN
	[t_funcionarios] ON [t_departamento].[n_idfuncionario_responsavel] = 
  [t_funcionarios].[n_idfuncionario]
	WHERE [t_funcionarios].[c_nomefuncionario] = 'ANTONIO JOSE';`


 ##  4 Liste todos os projetos com os custos previstos.

 Resposta:
	`SELECT p.[n_idprojeto] AS 'ID PROJETO', p.[c_descr_projeto] AS 'DESCRICAO DO   
PROJETO',
       SUM(p.[n_custo]) AS 'Custo previsto'
	FROM [t_projeto] p
	LEFT JOIN [t_acompanhamento] a ON p.[n_idprojeto] = a.[idprojeto]
	GROUP BY p.[n_idprojeto], p.[c_descr_projeto];`


##  5 Liste todos os projetos finalizados (data fim != null ).

 Resposta:
	`SELECT *
	FROM t_projeto
	WHERE d_fim IS NOT NULL;`

##  6 Liste todos os projetos com os custos previstos (com somatorio).

 Resposta:
	`SELECT SUM([t_projeto].[n_custo]) AS 'Custo Total Previsto'FROM [t_projeto]
	LEFT JOIN [t_acompanhamento] ON [n_idprojeto] = [idprojeto];`





##  7 liste todos os projetos listando os custos de acordo com os gastos de acompanhamento do projeto (com somatorio).

 Resposta:
	`SELECT SUM([t_acompanhamento].[n_custo]) AS 'Custo Total do acompanhamento'FROM [t_projeto]
	LEFT JOIN [t_acompanhamento] ON [n_idprojeto] = [idprojeto];
	`

# Bloco anonimo
 - O CODIGO A SEGUIR DEVE ESTA DENTRO DA ESTRUTURA: `BEGIN<CODIGO A BAIXO>END`
##  A) Verificar se existem datas de acompanhamento fora das datas do projeto.

  Resposta:
  `
    IF EXISTS (
        SELECT 1
        FROM t_acompanhamento a
        LEFT JOIN t_projeto p ON a.idprojeto = p.n_idprojeto
        WHERE a.d_data < p.d_inicio OR a.d_data > COALESCE(p.d_fim, a.d_data)
    )
    BEGIN
        PRINT 'Datas de acompanhamento fora das datas dos projetos.';
    END;
  `
  
##  B) Verificar se existem projetos que tiveram custos do acompanhamento acima do custo previsto

  Resposta:
  `
      IF EXISTS (
        SELECT 1
        FROM t_projeto p
        INNER JOIN (
            SELECT idprojeto, SUM(n_custo) as total_custo_acompanhamento
            FROM t_acompanhamento
            GROUP BY idprojeto
        ) a ON p.n_idprojeto = a.idprojeto
        WHERE a.total_custo_acompanhamento > p.n_custo
    )
    BEGIN
        PRINT 'Projetos com custos de acompanhamento acima do custo previsto.';
    END;`
## C) Verificar se existem projetos que atrasaram 

Resposta:
  ` IF EXISTS (
        SELECT 1
        FROM t_projeto
        WHERE d_fim < d_previsao_termino
    )
    BEGIN
        PRINT 'Existe projetos que atrasou.';
    END;`
## D) Verificar se existem diferenças entre o custo do projeto e os gastos dos acompanhamentos - mostrando a diferenca (o que ainda tem em caixa para o projeto ou o que ultrapassou o valor esperado)

Resposta:
  `  SELECT p.n_idprojeto, p.c_descr_projeto, p.n_custo as custo_previsto,
           ISNULL(a.total_custo_acompanhamento, 0) as custo_acompanhamento,
           p.n_custo - ISNULL(a.total_custo_acompanhamento, 0) as diferenca
    FROM t_projeto p
    LEFT JOIN (
        SELECT idprojeto, SUM(n_custo) as total_custo_acompanhamento
        FROM t_acompanhamento
        GROUP BY idprojeto
    ) a ON p.n_idprojeto = a.idprojeto;`
    
    
    
    
