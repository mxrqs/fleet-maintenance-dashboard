# Medidas DAX

As medidas abaixo foram utilizadas no dashboard de manutenção de frota.

> Observação: os nomes das tabelas e campos foram adaptados para fins de portfólio. Os dados do projeto são fictícios.

## Custo Total

```DAX
Custo Total =
SUM('Servicos'[Valor])


```DAX
% DO TOTAL = 
DIVIDE(
    SUM('Tabela_Os'[_total]),
    CALCULATE(
        SUM(Tabela_Os[_total]),
        ALL('Tabela_OS')
    )
)

```DAX
Carros Ativos em Contratos = 
VAR ContratoSelecionado =
    VALUES(Tabela_Os[Contrato])

RETURN
CALCULATE(
    DISTINCTCOUNT(Tabela_Mobilizacoes[PLACA]),

    TREATAS(
        ContratoSelecionado,
        Tabela_Mobilizacoes[CONTRATO]
    ),

    FILTER(
        ALL(Tabela_Mobilizacoes),
        ISBLANK(Tabela_Mobilizacoes[DESMOBILIZAÇÃO])
    )
)

```DAX
CUSTO MÉDIO P/VEICULO = 
DIVIDE(
    SUM('Tabela_OS'[_total]),
    [Carros Ativos em Contratos]
)

```DAX
Custo Mês Anterior = 
CALCULATE(
    [Custo Total],
    PREVIOUSMONTH('Calendario'[Date])
)

```DAX
Serviços Finalizados = 
CALCULATE(
    COUNTROWS('Tabela_Os'),
    'Tabela_Os'[encerrada] = TRUE()
)

```DAX
Serviços Pendentes = 
CALCULATE(
    COUNTROWS('Tabela_Os'),
    'Tabela_Os'[encerrada] = FALSE()
)

```DAX
Total de Serviços = 
COUNTROWS(Tabela_Os)


```DAX
Variação % Mes Passado = IF( [Custo Mês Anterior] = 0, 
	BLANK(), DIVIDE( [Custo Total] - [Custo Mês Anterior], 
	 [Custo Mês Anterior] ) )

```DAX
Veículos Atendidos = 
DISTINCTCOUNT(Tabela_Os[placa])


```DAX
Contrato = 
VAR VeiculoAtual = Tabela_Os[matricula]
VAR DataServico  = Tabela_Os[dt_ent]

VAR MobilizacaoValida =
FILTER (
    Tabela_Mobilizacoes,
    Tabela_Mobilizacoes[MATRÍCULA] = VeiculoAtual
        && Tabela_Mobilizacoes[MOBILIZAÇÃO] <= DataServico
        && (
            ISBLANK ( Tabela_Mobilizacoes[DESMOBILIZAÇÃO] )
            || Tabela_Mobilizacoes[DESMOBILIZAÇÃO] >= DataServico
        )
)

RETURN
COALESCE (
    MAXX ( MobilizacaoValida, Tabela_Mobilizacoes[CONTRATO] ),
    "SEM CONTRATO NO PERIODO"
)