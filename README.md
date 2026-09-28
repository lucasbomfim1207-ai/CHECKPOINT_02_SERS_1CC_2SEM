# APIs de Energia Renovável e Aprendizado de Máquina  

##  Integrantes

| Nome completo | RM |
|---|---|
| Eduardo Barcelos De Carvalho Braziliano | 573274 |
| Julia Johanson Peniche Dias Da Silva | 572220 |
| Lucas Bomfim Leite | 570420 |

## Objetivo

Este projeto utiliza dados de duas APIs públicas para aplicar técnicas de aprendizado de máquina em duas tarefas relacionadas à geração de energia renovável.

As tarefas desenvolvidas são:

1. **Classificação de fontes de geração da ANEEL:** classificar um empreendimento como **Solar, Eólica ou Hidráulica** utilizando sua potência outorgada e localização.
2. **Regressão de radiação solar:** estimar a **radiação solar horizontal média (W/m²)** em Petrolina (PE) a partir de condições meteorológicas e da hora do dia.

Em cada tarefa são treinados e comparados três algoritmos diferentes de aprendizado de máquina.

---

## Tarefa 1 — Classificação de fontes de geração

### Objetivo

Verificar se é possível classificar a fonte de geração de um empreendimento como **Solar, Eólica ou Hidráulica** utilizando apenas:

* Potência outorgada, em kW;
* Latitude;
* Longitude.

A variável alvo é `fonte`.

As categorias utilizadas são:

* `UFV` → **Solar**
* `EOL` → **Eólica**
* `UHE`, `PCH` e `CGH` → **Hidráulica**

Informações como nome do empreendimento, código CEG e descrição da fonte não são utilizadas como entradas, pois poderiam revelar diretamente a classe.

### Origem dos dados

Os dados são obtidos do **SIGA — Sistema de Informações de Geração da ANEEL**, através da API pública CKAN/DataStore.

Fonte:

* ANEEL — SIGA: https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel
* Recurso utilizado: https://dadosabertos.aneel.gov.br/dataset/siga-sistema-de-informacoes-de-geracao-da-aneel/resource/11ec447d-698d-4ab8-977f-b424d5deee6a

A consulta é pública e não exige token ou chave de API.

### Período dos dados

A consulta utiliza os registros disponíveis no recurso SIGA no momento da execução do notebook. Não é aplicado um intervalo de datas específico na consulta.

Foram consultadas as categorias `UFV`, `EOL`, `UHE`, `PCH` e `CGH`, com limite de 1.200 registros por categoria.

### Variáveis

| Variável      | Descrição                                   |
| ------------- | ------------------------------------------- |
| `potencia_kw` | Potência outorgada do empreendimento, em kW |
| `latitude`    | Latitude aproximada do empreendimento       |
| `longitude`   | Longitude aproximada do empreendimento      |
| `fonte`       | Classe: Solar, Eólica ou Hidráulica         |

### Modelos

Foram utilizados três classificadores diferentes, treinados com a mesma divisão estratificada entre treino e teste.

Os modelos, métricas e matriz de confusão utilizados devem ser apresentados no notebook.

### Conclusão da Tarefa 1

A classificação busca avaliar até que ponto **potência e localização** são suficientes para distinguir as três fontes de geração.

A análise deve considerar Accuracy, Precision, Recall e F1, utilizando a mesma divisão de treino/teste para os três modelos. Também deve ser observada a matriz de confusão para identificar quais classes apresentam maior confusão.

Mesmo que um modelo apresente bons resultados, potência e localização são informações limitadas para uma aplicação real. Características específicas do empreendimento e da fonte de geração podem ser necessárias para uma classificação mais confiável.

**Resultados dos modelos:** devem ser preenchidos após a execução do notebook com as métricas obtidas para os três classificadores.

---

## Tarefa 2 — Regressão de radiação solar

### Objetivo

Estimar a **radiação solar horizontal média**, em W/m², em Petrolina (PE), utilizando condições meteorológicas e a hora local.

As entradas utilizadas são:

* Temperatura;
* Umidade relativa;
* Cobertura de nuvens;
* Velocidade do vento;
* Hora do dia.

A variável alvo é `radiacao_w_m2`.

### Origem dos dados

Os dados meteorológicos são obtidos através da API histórica do **Open-Meteo**.

Fonte:

https://open-meteo.com/en/docs/historical-weather-api

A consulta é pública e não exige cadastro ou chave de API.

### Período e localização

Os dados utilizados correspondem ao período de:

**1º de abril de 2025 a 30 de junho de 2025**

Localização aproximada:

* Latitude: `-9.39`
* Longitude: `-40.50`
* Local: **Petrolina, Pernambuco**
* Fuso horário: `America/Recife`

Foram mantidos apenas os registros entre **7h e 17h**, evitando as horas noturnas.

Os dados históricos do Open-Meteo são estimativas baseadas em modelos/reanálise e não representam necessariamente medições realizadas por um sensor específico.

### Variáveis

| Variável        | Descrição                              | Unidade |
| --------------- | -------------------------------------- | ------- |
| `data_hora`     | Data e hora local do registro          | —       |
| `temperatura_c` | Temperatura do ar a 2 m                | °C      |
| `umidade_pct`   | Umidade relativa a 2 m                 | %       |
| `nuvens_pct`    | Cobertura total de nuvens              | %       |
| `vento_kmh`     | Velocidade do vento a 10 m             | km/h    |
| `hora`          | Hora local                             | h       |
| `radiacao_w_m2` | Radiação solar global horizontal média | W/m²    |

### Divisão dos dados

Os registros são mantidos em ordem cronológica.

Aproximadamente:

* **80% iniciais:** treinamento;
* **20% finais:** teste.

Os dados não são embaralhados, preservando a característica temporal da série.

### Modelos

Foram utilizados três modelos de regressão:

* Linear Regression;
* Random Forest Regressor;
* Gradient Boosting.

Os modelos são comparados utilizando:

* **MAE** — erro absoluto médio;
* **MSE** — erro quadrático médio;
* **R²** — coeficiente de determinação.

Também é apresentado um gráfico comparando os valores reais e previstos.

### Conclusão da Tarefa 2

A hora do dia possui papel importante na estimativa da radiação, pois a quantidade de radiação solar varia significativamente ao longo do dia. As demais variáveis meteorológicas ajudam a representar condições que podem influenciar a radiação observada.

Modelos capazes de representar relações não lineares, como Random Forest e Gradient Boosting, podem representar melhor a relação entre as variáveis meteorológicas, a hora e a radiação do que uma regressão linear simples.

Os resultados finais devem ser analisados a partir dos valores de MAE, MSE e R² obtidos no notebook.

É importante destacar que **radiação solar em W/m² não representa diretamente a energia produzida por um sistema fotovoltaico**. A produção de energia também depende de fatores como área dos painéis, eficiência dos módulos, inclinação, temperatura e perdas do sistema.

**Resultados dos modelos:** devem ser preenchidos após a execução do notebook com as métricas obtidas para os três regressores.

---

## Como executar o projeto

### 1. Requisitos

É necessário ter:

* Python 3;
* Jupyter Notebook ou JupyterLab;
* Conexão com a internet.

As bibliotecas utilizadas incluem:

```bash
pip install pandas numpy matplotlib scikit-learn
```

### 2. Executar o notebook

Abra o arquivo:

```text
Aula_APIs_Energia_Renovavel_ML(1).ipynb
```

Execute as células **na ordem**, começando pela primeira.

O notebook realiza as consultas às APIs, gera os arquivos CSV e executa as análises e modelos de aprendizado de máquina.

### 3. Arquivos gerados

Durante a execução são gerados:

```text
aneel_classificacao_orange.csv
meteo_regressao_orange.csv
```

O primeiro contém os dados utilizados na classificação das fontes de geração.

O segundo contém os dados meteorológicos utilizados na regressão da radiação solar.  

---

## Conclusão geral

As duas tarefas demonstram aplicações diferentes de aprendizado de máquina em dados relacionados à energia renovável.

Na **classificação**, o objetivo é identificar a fonte de geração de um empreendimento a partir de potência e localização, utilizando três classes: Solar, Eólica e Hidráulica.

Na **regressão**, o objetivo é estimar a radiação solar horizontal em Petrolina a partir de variáveis meteorológicas e da hora do dia.

Os experimentos permitem comparar diferentes algoritmos e observar como a escolha do modelo influencia o desempenho. A análise também evidencia as limitações dos dados: classificar uma fonte de geração apenas por potência e localização possui limitações, enquanto prever radiação solar não equivale diretamente a prever a quantidade de energia que um sistema fotovoltaico irá produzir.
