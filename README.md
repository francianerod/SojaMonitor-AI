# SojaMonitor AI: Inteligência Climática para a Soja

Monitoramento contínuo da estiagem da cultura da soja a partir de dados
meteorológicos diários.
 
A solução classifica, ao longo do ciclo produtivo, quando a falta de chuva
passa a comprometer a lavoura de soja — usando um indicador calibrado pela
exigência hídrica da cultura, e não por convenções meteorológicas genéricas.

Um diferencial para esse repositório é a possibilidade de previsão da estiagem
da soja para 3 dias a frente que será estudado e analisado.
 
**Autora:** Franciane Rodrigues — Doutora em Administração (ESAN/UFMS)
 
---
 
## Origem
 
O núcleo da solução vem da tese de doutorado defendida em 2025:
 
> RODRIGUES, F. *Classificação da estiagem na sojicultura com machine
> learning: uma abordagem aplicada à região de Dourados/MS.* 2025. 90 f.
> Tese (Doutorado em Administração) — Escola de Administração e Negócios,
> Universidade Federal de Mato Grosso do Sul, Campo Grande, MS.
 
Os notebooks originais da pesquisa estão em repositório próprio:
[Doutorado-ESAN-UFMS](https://github.com/francianerod/Doutorado-ESAN-UFMS)
 
Este repositório contém o desenvolvimento posterior, voltado à aplicação
operacional do conceito.
 
---
 
## O indicador
 
**Estiagem da cultura da soja:** precipitação acumulada inferior a **20,6 mm
em janelas móveis de cinco dias**, durante o ciclo produtivo (setembro a
março) em Dourados/MS.
 
O limiar foi calibrado sobre 44 anos de série diária da estação da Embrapa
Agropecuária Oeste, em Dourados/MS — 16.281 registros, com apenas quatro dias
sem medição. O critério unifica os conceitos de estiagem e veranico em uma
única métrica operacional.
 
---
 
## Conteúdo
 
```
sojamonitor-ai/
├── cpao_oficial_dados_1979_2023.csv     série diária, Dourados/MS
├── 01_validacao_temporal.py             desempenho em safras futuras
├── 02_valor_do_modelo.py                modelo vs. regra de cálculo
└── painel_sojamonitorAI.html              protótipo do painel da safra

```
 
---
 
## `01_validacao_temporal.py`
 
Mede o desempenho do classificador em safras que não participaram do
treinamento.
 
O modelo é treinado com dados até 31/08/2021 e avaliado em duas safras
posteriores, escolhidas por contraste climático: 2021/22 (desempenho ruim)
e 2022/23 (desempenho bom). Não há sobreposição temporal entre treino e
validação.
 
Reporta acurácia, acurácia balanceada, AUC, precisão, sensibilidade, F1 e
matriz de confusão, sempre em comparação com o baseline da classe
majoritária. Roda dois conjuntos de variáveis — o adotado na tese e um
reduzido, sem as variáveis derivadas do critério de rotulagem — como teste
de sensibilidade.
 
**Resultado:** AUC de 0,85 (safra 2021/22) e 0,87 (safra 2022/23) com o
conjunto completo; entre 0,67 e 0,74 com o conjunto reduzido, acima do acaso
em todos os classificadores.
 
---
 
## `02_valor_do_modelo.py`
 
Testa em que situações o modelo supera o simples cálculo do indicador.
 
Com a janela de cinco dias completa, a classificação é aritmética: soma-se a
precipitação e compara-se com 20,6 mm. O modelo só se justifica onde essa
soma não pode ser feita. Cada situação é comparada a uma alternativa sem
modelo.
 
| Situação | Alternativa sem modelo | Estiagens não avisadas |
|---|---|---|
| Aviso com 3 dias de antecedência | repetir a condição atual | 43 → 6 (safra boa) |
| Janela com 2 de 5 dias sem medição | extrapolação proporcional | 11 → 0 (safra ruim) |
 
Também deriva o limiar local a partir de qualquer série, mostrando que o
valor responde ao regime de chuva: 20,7 mm na série completa, entre 19,6 mm
e 22,2 mm conforme a década analisada. Isso fundamenta a recalibração como
etapa da extensão a outras regiões.
 
**Limite declarado:** o horizonte de antecipação foi testado de 1 a 14 dias.
Até três dias o modelo supera a alternativa sem modelo; a partir de cinco, o
desempenho cai ao nível do acaso, porque a janela alvo passa a depender de
chuva ainda não ocorrida.
 
---
 
## Como executar
 
```bash
pip install pandas numpy scikit-learn
# opcionais, usados automaticamente quando disponíveis:
pip install xgboost imbalanced-learn
 
python validacao/01_validacao_temporal.py
python validacao/02_valor_do_modelo.py
```
 
Ajuste a constante `CAMINHO` no topo de cada script conforme a localização
do arquivo de dados. As saídas de referência estão em `resultados/`.
 
Os scripts têm semente fixa e são determinísticos: a mesma base produz os
mesmos números.
 
---
 
## Estágio atual
 
| Componente | Situação |
|---|---|
| Conceito de estiagem da cultura da soja | definido e publicado em tese |
| Limiar de 20,6 mm em janelas de cinco dias | calibrado sobre 44 anos |
| Modelo de classificação | treinado e validado |
| Validação em safras contrastantes | concluída (separação temporal) |
| Painel | protótipo demonstrativo |
| Ingestão automatizada e API | a desenvolver |
| Teste com usuários | a realizar |
 
---
 
## Tecnologias
 
Python 3 · pandas · NumPy · scikit-learn · XGBoost · imbalanced-learn
 
## Dados
 
Estação meteorológica da Embrapa Agropecuária Oeste, Dourados/MS
(1979–2023). Dado público.
