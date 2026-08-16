# 🌱 SojaMonitor AI — Inteligência Climática para a Soja

**Monitoramento e antecipação de estiagens na cultura da soja a partir de dados meteorológicos diários e machine learning.**

O **SojaMonitor AI** é uma solução de inteligência climática voltada à identificação de condições de estiagem ao longo do ciclo produtivo da soja. O projeto utiliza um indicador calibrado a partir da exigência hídrica da cultura, evitando depender exclusivamente de convenções meteorológicas genéricas.

Para Dourados/MS, considera-se uma condição de estiagem quando a precipitação acumulada é inferior a **20,6 mm em uma janela móvel de cinco dias**, durante o ciclo produtivo da soja.

Além do monitoramento da condição observada, o projeto investiga situações em que o **machine learning pode agregar valor além do cálculo direto do indicador**, especialmente:

* **antecipação da estiagem em até três dias**;
* **classificação quando há falhas na medição meteorológica**;
* **recalibração do indicador para futura expansão regional**.

Os experimentos iniciais apresentam evidências de viabilidade para a antecipação em três dias, com redução expressiva dos eventos de estiagem não detectados em comparação com estratégias simples sem modelo.

**Autora:** Franciane Rodrigues — Cientista de Dados e Doutora em Administração (ESAN/UFMS)

---

## 🎓 Origem

O núcleo científico do SojaMonitor AI tem origem na tese de doutorado defendida em 2025:

> RODRIGUES, F. *Classificação da estiagem na sojicultura com machine learning: uma abordagem aplicada à região de Dourados/MS.* 2025. 90 f. Tese (Doutorado em Administração) — Escola de Administração e Negócios, Universidade Federal de Mato Grosso do Sul, Campo Grande, MS.

Os notebooks originais da pesquisa estão disponíveis no repositório:

[Doutorado-ESAN-UFMS](https://github.com/francianerod/Doutorado-ESAN-UFMS)

Este repositório reúne o desenvolvimento posterior da pesquisa, com foco na transformação do conceito científico em uma solução com potencial de aplicação operacional.

---

## 🌧️ O indicador de estiagem

No contexto atual do projeto, a **estiagem da cultura da soja** é definida como:

> **Precipitação acumulada inferior a 20,6 mm em uma janela móvel de cinco dias**, durante o ciclo produtivo da soja, entre setembro e março, em Dourados/MS.

O limiar foi calibrado a partir de **44 anos de dados meteorológicos diários** da estação da Embrapa Agropecuária Oeste, em Dourados/MS.

A série histórica compreende:

* período de **1979 a 2023**;
* **16.281 registros diários**;
* apenas **quatro dias sem medição**.

O critério busca representar a disponibilidade hídrica relevante para a cultura e unifica, em uma única métrica operacional, situações tradicionalmente descritas como estiagem e veranico.

### Escopo atual do limiar

O valor de **20,6 mm foi calibrado para Dourados/MS e não deve ser considerado um valor universal**.

A aplicação do SojaMonitor AI em outras localidades exige uma etapa de **recalibração com séries meteorológicas locais**, seguida de validação do indicador e do modelo para cada nova região.

---

## 🤖 Por que usar machine learning?

Quando os cinco dias da janela já foram completamente observados, a classificação da estiagem é determinística:

1. soma-se a precipitação dos cinco dias;
2. compara-se o resultado com o limiar de 20,6 mm.

Nesse cenário, **machine learning não é necessário**.

O modelo passa a agregar valor quando a informação necessária para realizar esse cálculo ainda não está completamente disponível.

O SojaMonitor AI concentra o uso de machine learning principalmente em dois cenários:

### 1. Antecipação da estiagem

Prever hoje se a janela será classificada como estiagem **três dias à frente**, quando parte da precipitação necessária para o cálculo ainda não foi observada.

### 2. Janela meteorológica incompleta

Classificar a condição de estiagem mesmo quando existem **dias sem medição dentro da janela de cinco dias**, situação possível em estações meteorológicas sujeitas a falhas ou interrupções.

Assim, o objetivo do modelo não é substituir um cálculo que já pode ser realizado diretamente, mas **atuar quando a informação necessária para esse cálculo ainda não existe ou está incompleta**.

---

## 📊 Principais resultados

### Validação temporal

O modelo foi treinado com dados até **31/08/2021** e avaliado em duas safras posteriores, sem sobreposição temporal entre treinamento e validação:

* **2021/22**
* **2022/23**

Com o conjunto completo de variáveis, foram obtidos:

| Safra   |       AUC |
| ------- | --------: |
| 2021/22 | **0,848** |
| 2022/23 | **0,872** |

Os resultados indicam boa capacidade de discriminação entre dias com e sem estiagem em períodos que não participaram do treinamento.

Um segundo experimento retirou variáveis derivadas do próprio critério de rotulagem e o ano-calendário, reduzindo possíveis atalhos informacionais.

Nesse cenário mais restritivo, a AUC ficou entre **0,674 e 0,735**.

A redução de desempenho é registrada de forma intencional, pois delimita o que o modelo atual consegue entregar e fornece evidências para orientar sua evolução.

---

## ⏱️ Antecipação de três dias

A tarefa consiste em antecipar se a janela móvel será classificada como estiagem **três dias à frente**, quando parte da precipitação futura ainda não foi observada.

Como referência sem modelo, utiliza-se a estratégia de **persistência**, que assume que a condição observada hoje permanecerá nos dias seguintes.

Com um corte de decisão de **0,35**, configurado para priorizar maior sensibilidade, foram obtidos os seguintes resultados:

| Safra   |          Sem modelo | SojaMonitor AI | Redução |
| ------- | ------------------: | -------------: | ------: |
| 2021/22 | 26 alertas perdidos |          **7** | **73%** |
| 2022/23 | 43 alertas perdidos |          **6** | **86%** |

Na safra 2022/23, por exemplo:

* acurácia: **58,0% → 74,5%** com corte 0,50;
* AUC: **0,580 → 0,788**;
* sensibilidade: **0,59 → 0,82**;
* alertas perdidos: **43 → 19**.

Ao reduzir o corte para **0,35**, priorizando a sensibilidade, os alertas perdidos caem para apenas **6**, com sensibilidade de **0,94**.

Os resultados fornecem **evidência de viabilidade para a antecipação da estiagem em três dias**, embora novas safras e regiões ainda devam ser utilizadas na evolução e validação do modelo.

---

## 📡 Classificação com falhas de medição

O segundo cenário simula uma janela em que **dois dos cinco dias estão sem medição**.

Sem todos os valores observados, a soma da precipitação não pode ser realizada diretamente.

Como referência sem modelo, utiliza-se a **extrapolação proporcional dos dias disponíveis**.

Com o corte de decisão de **0,35**:

| Safra   |          Sem modelo | SojaMonitor AI |  Redução |
| ------- | ------------------: | -------------: | -------: |
| 2021/22 | 11 alertas perdidos |          **0** | **100%** |
| 2022/23 | 11 alertas perdidos |          **2** |  **82%** |

Na safra 2021/22, a sensibilidade alcançou **1,00**, sem nenhum evento de estiagem perdido.

Na safra 2022/23, a sensibilidade chegou a **0,98**, com apenas **2 eventos não detectados**.

---

## 🚨 Estratégia de calibração

O erro operacional prioritário do SojaMonitor AI é o **alerta de estiagem não emitido**, pois a ausência de aviso reduz a possibilidade de adoção antecipada de medidas de manejo e pode aumentar o risco de perdas produtivas.

Por essa razão, foi avaliado um corte de decisão de **0,35**, configurado para privilegiar deliberadamente a sensibilidade.

Considerando as duas tarefas avaliadas, essa configuração reduziu os eventos não detectados entre **73% e 100%** em relação às respectivas estratégias sem modelo.

Esse ganho ocorre ao custo de um aumento de alarmes falsos em algumas situações, caracterizando um **trade-off operacional intencional**.

O sistema pode, portanto, ser calibrado conforme o custo associado a:

* deixar de detectar uma possível estiagem;
* emitir um alerta que posteriormente não se confirme.

No estágio atual, a configuração avaliada prioriza a **detecção de eventos potencialmente críticos**.

---

## 🌎 Recalibração e expansão regional

O procedimento de calibração também foi reaplicado a diferentes períodos da série histórica de Dourados/MS.

Para os **44 anos completos**, o limiar recalculado foi de:

**20,7 mm**

O resultado é praticamente idêntico aos **20,6 mm** obtidos na tese, reforçando a reprodutibilidade do procedimento.

Quando a calibração foi realizada separadamente por década, entretanto, os limiares variaram entre:

**19,6 mm e 22,2 mm**

Esse resultado indica que o valor numérico do limiar é sensível às características climáticas do período analisado.

Portanto:

> **O procedimento de calibração é reprodutível, mas o valor de 20,6 mm não deve ser tratado como universal.**

A expansão do SojaMonitor AI para novas localidades deverá incluir:

1. obtenção de séries meteorológicas históricas locais;
2. recalibração do indicador;
3. validação do novo limiar;
4. treinamento ou ajuste do modelo;
5. validação em safras futuras da região.

A transferibilidade geográfica do procedimento ainda deverá ser avaliada empiricamente.

---

## 🔬 Horizonte de antecipação

O horizonte de antecipação foi testado de **1 a 14 dias**.

Os experimentos indicaram que o modelo agrega valor principalmente nos horizontes mais curtos.

Até **três dias**, o desempenho supera a estratégia de referência utilizada no experimento.

A partir de aproximadamente **cinco dias**, o desempenho se aproxima do acaso, pois uma parcela crescente da janela alvo passa a depender de precipitação ainda não ocorrida.

Por esse motivo, o horizonte inicial adotado pelo SojaMonitor AI é de:

> **até três dias de antecedência.**

---

## 📁 Conteúdo do repositório

```text
sojamonitor-ai/
│
├── cpao_oficial_dados_1979_2023.csv
│   └── série meteorológica diária de Dourados/MS
│
├── validacao/
│   ├── 01_validacao_temporal.ipynb
│   └── 02_valor_do_modelo.ipynb
│
└── index.html
    └── protótipo do painel da safra
│
└──  Evidencias_Desenvolvimento_SojaMonitor_AI.pdf
```

---

## 🧪 `01_validacao_temporal.py`

Avalia o desempenho do classificador em safras que não participaram do treinamento.

O modelo é treinado com dados até **31/08/2021** e avaliado nas safras **2021/22** e **2022/23**.

Não há sobreposição temporal entre treinamento e validação.

O experimento reporta:

* acurácia;
* acurácia balanceada;
* AUC;
* precisão;
* sensibilidade;
* F1-score;
* matriz de confusão.

Os resultados são comparados ao baseline da classe majoritária.

Também são avaliados dois conjuntos de variáveis:

1. conjunto completo utilizado no desenvolvimento original;
2. conjunto reduzido, sem variáveis derivadas do critério de rotulagem e sem o ano-calendário.

Essa segunda configuração funciona como teste de sensibilidade e ajuda a avaliar quanto do desempenho permanece sem possíveis atalhos informacionais.

---

## 🧪 `02_valor_do_modelo.py`

Avalia **em quais situações o modelo agrega valor em relação a alternativas simples sem machine learning**.

Os principais experimentos são:

| Situação                | Referência sem modelo     |                     2021/22 |    2022/23 |
| ----------------------- | ------------------------- | --------------------------: | ---------: |
| Antecipação de 3 dias   | Persistência              | **26 → 7** alertas perdidos | **43 → 6** |
| 2 de 5 dias sem medição | Extrapolação proporcional |                  **11 → 0** | **11 → 2** |

*Resultados do modelo com corte de decisão de 0,35, configurado para priorizar sensibilidade.*

O script também avalia:

* diferentes horizontes de antecipação;
* diferentes limiares de decisão;
* comportamento dos falsos positivos e falsos negativos;
* recalibração do indicador ao longo da série histórica.

---

## ▶️ Como executar

Instale as dependências principais:

```bash
pip install pandas numpy scikit-learn
```

Dependências opcionais:

```bash
pip install xgboost imbalanced-learn
```

Execute os experimentos:

```bash
python validacao/01_validacao_temporal.py
python validacao/02_valor_do_modelo.py
```

Ajuste a constante `CAMINHO` no início de cada script conforme a localização do arquivo de dados.

As saídas de referência estão disponíveis em:

```text
resultados/
```

Os experimentos utilizam semente fixa, favorecendo a reprodutibilidade: a mesma base e configuração produzem os mesmos resultados.

---

## 🚧 Estágio atual

| Componente                              | Situação                                              |
| --------------------------------------- | ----------------------------------------------------- |
| Conceito de estiagem da cultura da soja | ✅ Definido e publicado em tese                        |
| Limiar de 20,6 mm                       | ✅ Calibrado sobre 44 anos                             |
| Modelo de classificação                 | ✅ Treinado                                            |
| Validação temporal                      | ✅ Realizada em duas safras posteriores ao treinamento |
| Antecipação de 3 dias                   | ✅ Evidência inicial de viabilidade                    |
| Tratamento de janelas incompletas       | ✅ Avaliado experimentalmente                          |
| Recalibração temporal do indicador      | ✅ Avaliada                                            |
| Mockup do Painel                        | ✅ Protótipo demonstrativo                             |
| Recalibração para outras regiões        | 🔄 A desenvolver                                      |
| Painel                                  | 🔄 A desenvolver                                      |
| Ingestão automatizada de dados          | 🔄 A desenvolver                                      |
| API                                     | 🔄 A desenvolver                                      |
| Validação em novas regiões              | 🔄 A realizar                                         |
| Testes com usuários                     | 🔄 A realizar                                         |

---

## 🛠️ Tecnologias

* Python 3
* pandas
* NumPy
* scikit-learn
* XGBoost
* imbalanced-learn

---

## 📚 Dados

Os experimentos utilizam dados meteorológicos da:

**Embrapa Agropecuária Oeste — Dourados/MS**

Período utilizado:

**1979–2023**

A série meteorológica utilizada no projeto é composta por dados diários e constitui a base para a calibração e avaliação inicial do indicador de estiagem.

---

## ⚠️ Limitações atuais

O SojaMonitor AI encontra-se em estágio de desenvolvimento e validação experimental.

Os resultados apresentados neste repositório:

* foram obtidos a partir da série meteorológica de **Dourados/MS**;
* não demonstram, por si só, validade para outras regiões produtoras;
* exigem validação adicional em novas safras e localidades;
* não substituem recomendações agronômicas, meteorológicas ou decisões técnicas de manejo.

O objetivo atual é avaliar a viabilidade técnica da abordagem e evoluir o modelo para posterior aplicação em diferentes contextos produtivos.

---

## 🎯 Visão do projeto

O SojaMonitor AI busca evoluir de um indicador científico de estiagem para uma solução de **inteligência climática aplicada à sojicultura**, capaz de combinar:

**dados meteorológicos → monitoramento → antecipação → alerta → apoio à decisão**

O princípio que orienta o desenvolvimento é simples:

> **Quando a condição já pode ser calculada diretamente, utiliza-se a regra. Quando a informação ainda não existe ou está incompleta, o machine learning passa a agregar valor.**
