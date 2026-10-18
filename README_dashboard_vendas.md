# Dashboard de Vendas (Excel)

Dashboard de vendas feito inteiramente no Excel. Ele transforma uma base de pedidos em indicadores e gráficos que respondem a filtros: quanto foi vendido, quanto sobrou de lucro, quais categorias, regiões e produtos mais contribuem e como a receita evolui ao longo do tempo.

![Visão geral do dashboard](images/dashboard.png)

> **Sobre os dados:** a base incluída neste repositório é **fictícia**, gerada só para demonstrar o funcionamento (900 pedidos de janeiro de 2024 a setembro de 2026, de uma loja de games imaginária). Nenhum valor representa vendas reais. A seção [Usando outra base](#usando-outra-base) explica como trocar pelos seus dados.

---

## O que o dashboard responde

| Pergunta | Onde aparece |
|---|---|
| Quanto foi vendido? | Cartão **Receita total** |
| Quanto sobrou de lucro e qual a margem? | Cartões **Lucro total** e **Margem de lucro** |
| Quantas unidades e quantos pedidos? | Cartões **Unidades vendidas** e **Pedidos** |
| Qual o valor médio de cada pedido? | Cartão **Ticket médio** |
| Quais categorias vendem mais? | Gráfico **Receita por categoria** |
| Como as vendas se dividem entre loja física e online? | Gráfico **Participação por canal** |
| Como a receita evolui mês a mês? | Gráfico **Evolução mensal da receita** |
| Quais regiões vendem mais? | Gráfico **Receita por região** |
| Quais são os produtos campeões? | Gráfico **Top 5 produtos por receita** |

## Filtros

No topo do dashboard há quatro filtros em lista suspensa: **Ano**, **Região**, **Categoria** e **Canal**. Cada um tem a opção "Todos/Todas". Ao mudar qualquer filtro, os cartões e os gráficos se atualizam.

Exceção: o gráfico de evolução mensal respeita Região, Categoria e Canal, mas mostra sempre todos os anos, porque a linha do tempo completa é o objetivo dele.

---

## Estrutura da planilha

| Aba | Conteúdo |
|---|---|
| **Dashboard** | Tela final: banner, filtros, seis indicadores e cinco gráficos. |
| **Base** | Os dados brutos, uma linha por pedido. |
| **Apoio** | Listas dos filtros, critérios calculados e tabelas que alimentam os gráficos. |

### Colunas da aba Base

| Coluna | Descrição |
|---|---|
| ID do pedido | Identificador do pedido |
| Data | Data da venda |
| Produto / Categoria | O que foi vendido (12 produtos em 4 categorias: Console, Acessório, Jogo, Assinatura) |
| Região | Norte, Nordeste, Centro-Oeste, Sudeste ou Sul |
| Canal | Loja física ou Online |
| Quantidade | Unidades no pedido |
| Preço unitário / Custo unitário | Valores em reais |
| **Receita** | Calculada: quantidade × preço unitário |
| **Lucro** | Calculado: receita − quantidade × custo unitário |

Convenção visual: **texto azul** são dados digitados, **células cinzas** são fórmulas e **células amarelas** são os filtros do dashboard.

---

## Como foi feito

O dashboard usa **fórmulas, intervalos nomeados e gráficos nativos do Excel**, sem macros.

1. **Base organizada:** uma linha por pedido, com Receita e Lucro calculados por fórmula.
2. **Intervalos nomeados** deixam as fórmulas legíveis, como `b_receita`, `b_data`, `b_regiao`, `b_categoria`, `b_canal`. Os filtros também têm nome: `f_ano`, `f_regiao`, `f_categoria`, `f_canal`.
3. **Filtros com validação de dados:** listas suspensas alimentadas pela aba Apoio.
4. **Critérios dinâmicos:** a aba Apoio converte a escolha do filtro em critério. Quando o filtro está em "Todos", o critério vira o curinga `*` (qualquer texto), e o ano vira um intervalo de datas (de 01/01 a 31/12 do ano escolhido, ou um período amplo para "Todos").
5. **Indicadores com `SUMIFS` e `COUNTIFS`**, todos usando os mesmos critérios. Exemplo:

   ```
   =SUMIFS(b_receita, b_data, ">="&c_ini, b_data, "<="&c_fim,
           b_regiao, c_regiao, b_categoria, c_cat, b_canal, c_canal)
   ```
6. **Top 5 com `LARGE`, `INDEX` e `MATCH`:** uma chave de desempate evita que produtos com a mesma receita se repitam no ranking.
7. **Evolução mensal:** uma tabela de 33 meses (jan/24 a set/26) calculada com `EDATE` e `SUMIFS`.
8. **Gráficos** de colunas, barras, linha e pizza ligados às tabelas da aba Apoio, por isso mudam junto com os filtros.
9. **Acabamento:** banner, linhas de grade ocultas, cartões de indicadores e página configurada para impressão em uma folha.

---

## Como reproduzir

1. Baixe `dashboard_vendas.xlsx` e abra no Excel.
2. Vá à aba **Dashboard** e teste os filtros.
3. Para ver como cada número é calculado, abra a aba **Apoio** e clique nas células cinzas. Para ver os nomes, use *Fórmulas > Gerenciador de Nomes*.

Se quiser refazer do zero:

1. Crie a aba **Base** com as colunas acima e nomeie cada coluna (`b_receita`, `b_data`...).
2. Crie a aba **Apoio** com as listas dos filtros e as tabelas por categoria, região, canal, produto e mês, todas com `SUMIFS`.
3. Na aba **Dashboard**, crie os filtros com *Dados > Validação de Dados > Lista*, os cartões com as fórmulas de `SUMIFS` e os gráficos ligados à aba Apoio.

---

## Usando outra base

Para usar sua própria base de vendas (por exemplo, a base do desafio):

1. Cole os dados na aba **Base**, a partir da linha 2, com as mesmas colunas e na mesma ordem. Se sua base tiver outras colunas, ajuste os intervalos nomeados.
2. Copie as fórmulas de **Receita** e **Lucro** para todas as linhas novas, se a sua base não as trouxer prontas.
3. Na aba **Apoio**, atualize as listas de **Anos, Regiões, Categorias e Canais** e a lista de **Produtos** (coluna A, a partir da linha 20).
4. Os intervalos nomeados cobrem até a linha 3001 da Base. Passou disso, estenda-os no Gerenciador de Nomes.
5. A tabela mensal tem 33 linhas a partir do primeiro mês da base. Se o seu período for maior, copie a última linha para baixo e estenda o gráfico.

---

## Limitações

- Os filtros são listas suspensas, não segmentações de dados (*slicers*), e os números vêm de fórmulas, não de tabelas dinâmicas. A vantagem é que tudo é rastreável fórmula a fórmula.
- O ranking Top 5 mostra produtos com receita zero quando o filtro deixa menos de cinco produtos disponíveis.
- Não há comparação com períodos anteriores nem metas.

## Ideias para evoluir

- Trocar os filtros por **segmentações de dados** e as tabelas de apoio por **tabelas dinâmicas**.
- Cartões com variação contra o ano anterior.
- Meta de vendas por região com indicador de atingimento.
- Ocultar a barra de fórmulas, os títulos e as guias na versão final.

---

## Estrutura do repositório

```
dashboard-vendas-excel/
├── README.md
├── dashboard_vendas.xlsx
└── images/
    └── dashboard.png
```
