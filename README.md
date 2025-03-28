# Classificaçao de Pulsares: Random Forest Classifier
Trabalho prático II - Sistemas de Apoio à Decisão

- Kassio Rodrigues Ferreira - 19.1.8139
- Mateus Costa 20.1.8025

---

Arquivos úteis:

- [**Notebook - Código**](./TP2-SAD-Classificação-Pulsar-Relatório.pdf)
- [**Relatório**](./TP2-SAD-Classificação-Pulsar-Relatório.pdf)
- [**Apresentação - Slides**](./TP2-SAD-Classificação-Pulsar-Relatório.pdf)
- **Dataset:** [https://archive.ics.uci.edu/dataset/372/htru2](https://archive.ics.uci.edu/dataset/372/htru2)

# Dataset HTRU2 (Pulsar Detection)

## 1. Introdução

Este projeto tem como objetivo a aplicação de técnicas de mineração de dados para resolver um problema de classificação binária: distinguir entre **pulsars reais** e **falsos positivos** em sinais de rádio captados por telescópios.

A base de dados utilizada é a **HTRU2 (High Time Resolution Universe Survey)**, disponível no repositório da UCI Machine Learning. A tarefa consiste em prever, a partir de estatísticas extraídas dos sinais, se uma amostra representa ou não um pulsar.

- Link para o dataset: https://archive.ics.uci.edu/dataset/372/htru2

### 1.1 Sobre o Dataset HTRU2

O conjunto de dados **HTRU2** foi criado a partir de sinais de rádio captados por telescópios, com o objetivo de identificar **pulsars** — estrelas de nêutrons que emitem feixes de radiação de forma periódica, como um farol no espaço.

Detectar esses sinais é desafiador, pois a maioria dos registros captados é composta por **ruídos ou interferências**, vindos de transmissões humanas ou outros fenômenos cósmicos. O grande desafio é diferenciar os sinais legítimos (pulsars reais) dos **falsos positivos**.

Para isso, cada amostra do dataset é descrita por estatísticas extraídas de duas representações do sinal:

- **Perfil Integrado**: mostra como o sinal varia ao longo do tempo.
- **Curva DM-SNR**: mostra como o sinal se comporta ao variar o valor da dispersão (um fenômeno causado pela presença de partículas entre a estrela e a Terra).

De cada uma dessas curvas, foram extraídas quatro medidas estatísticas que ajudam a caracterizar o sinal:

- **Média**: valor médio da curva; sinais de pulsars tendem a ter médias mais elevadas em função da presença de picos regulares.
- **Desvio padrão**: mostra o quanto o sinal oscila em torno da média; pulsares costumam apresentar maior variação por conta da repetição de pulsos intensos.
- **Curtose**: detecta picos acentuados; sinais de pulsars geralmente exibem curtose alta devido aos pulsos bem definidos.
- **Assimetria**: avalia se o sinal é balanceado ou inclinado para um dos lados; em muitos casos, pulsares apresentam assimetria positiva ou negativa dependendo do formato do pulso captado.


## 2. Características da Base de Dados

- **Total de amostras:** 17.898
- **Classes:**
  - `0`: Não-pulsar (16.259 amostras)
  - `1`: Pulsar (1.639 amostras)

- **Atributos:** 8 estatísticas descritivas dos sinais baseadas em duas curvas extraídas do sinal original: **curva integrada** e **curva DM-SNR**.

Para cada tipo de curva, foram extraídas 4 estatísticas, e os atributos foram renomeados para português visando facilitar a análise:

- **Perfil Integrado**
  - Média, Desvio Padrão, Curtose, Assimetria

- **Curva DM-SNR**
  - Média, Desvio Padrão, Curtose, Assimetria

## 3. Etapas de Pré-processamento

- **Análise exploratória de dados:** distribuição das variáveis e matriz de correlação entre variáveis.

- **Remoção de colunas altamente correlacionadas:** `assimetria_perfil_integrado` e `assimetria_curva_dm_snr` foram removidas por apresentarem correlação superior a 90% com outras variáveis.

- **Verificação de valores ausentes:** nenhuma ocorrência de valores nulos foi detectada.

- **Normalização dos dados:** os dados foram padronizados com `StandardScaler` (z-score), garantindo média zero e desvio padrão 1 para cada atributo.

- **Balanceamento das classes:** como a classe 0 representava mais de 90% das amostras, aplicou-se **undersampling com NearMiss (versão 1)**. Resultado:
  - 1.639 amostras de cada classe
  - Total: 3.278 amostras

## 4. Treinamento

- Foi utilizado o algoritmo **Random Forest Classifier**, com validação cruzada `StratifiedKFold` (5 folds) e otimização de hiperparâmetros via `GridSearchCV`.
- O modelo foi treinado e avaliado em cada fold de forma independente.

## 5. Resultados

### Principais métricas (média entre os folds):

| Métrica      | Valor Médio |
|--------------|-------------|
| Acurácia     | ~95,28%     |
| Precisão     | ~95,39%     |
| Recall       | ~95,24%     |
| F1-Score     | ~95,24%     |

### Importância das Features (top 3):

1. `curtose_perfil_integrado`
2. `media_perfil_integrado`
3. `curtose_curva_dm_snr`

## Referências

R. Lyon. "HTRU2," UCI Machine Learning Repository, 2015. [Online]. Available: https://doi.org/10.24432/C5DK6R.

Lyon, R.J., Stappers, B.W., Cooper, S., Brooke, J.M., & Knowles, J.D. (2016). Fifty years of pulsar candidate selection: from simple filters to a new principled real-time classification approach. Monthly Notices of the Royal Astronomical Society, 459, 1104-1123.
