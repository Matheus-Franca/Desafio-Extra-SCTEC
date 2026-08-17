# Documentação do Projeto: Previsão de Cancelamento de Reservas Hoteleiras

## 1. Identificação do Projeto e do Autor
* **Projeto:** Desafio Extra C2 - Introdução à Inteligência Artificial (IP 20h A)
* **Objetivo:** Realizar a Análise Exploratória de Dados (AED) e desenvolver um modelo preditivo para identificar padrões de cancelamento de reservas utilizando a base de dados "Hotel Booking Demand".
* **Autor:** Matheus Henrique França
* **Data:** agosto/2026

---

## 2. Instruções de Execução (Google Colab)
Para a reprodução deste código no Google Colab, você precisará adicionar a base de dados `hotel_bookings.csv` na pasta `sample_data` do seu ambiente. Para fazer isso, siga os passos abaixo:
1. Abra o arquivo ipynb no **Google Colab**.
2. No canto superior direito da tela, clique no botão **"Conectar"** (ou simplesmente execute a primeira célula de código). Isso é necessário para que o Colab crie o ambiente virtual e aloque a sua máquina.
3. Após conectar, no menu lateral esquerdo, clique no ícone de **Pasta** (Arquivos).
4. Localize a pasta padrão chamada `sample_data` e clique nela para expandir.
5. Clique no ícone de upload (uma folha com uma seta para cima) ou simplesmente **arraste e solte** o arquivo `hotel_bookings.csv` do seu computador para dentro da pasta `sample_data`.
6. Aguarde o círculo de carregamento finalizar indicando que o upload foi concluído antes de executar as células.

---

## 3. Descrição das Etapas de Desenvolvimento
O projeto foi estruturado de forma lógica e sequencial, simulando um ambiente real de Ciência de Dados:
1. **Configuração e Importação:** Carregamento do conjunto de dados `hotel_bookings.csv` utilizando a biblioteca `pandas` e importação de bibliotecas gráficas (`matplotlib`, `seaborn`) e de Machine Learning (`scikit-learn`).
2. **Inspeção Inicial:** Uso de métodos exploratórios para entender as dimensões do dataset, os tipos de variáveis e a presença de dados nulos.
3. **Limpeza e Tratamento:** Preenchimento de dados faltantes, remoção de colunas com excesso de nulos, ajuste de tipos de dados e tratamento de *outliers* irreais.
4. **Análise Exploratória de Dados (AED):** Geração de visualizações gráficas padronizadas (utilizando paleta de cores verde/vermelho para clareza) para responder a questões de negócio (sazonalidade, impacto do *lead time*, análise geográfica, etc).
5. **Engenharia de Recursos (Feature Engineering):** Criação de novas variáveis (como o total de hóspedes) e remoção de colunas que causariam vazamento de dados (*data leakage*). Em seguida, os dados categóricos foram codificados e divididos em treino e teste.
6. **Modelagem Preditiva:** Treinamento de dois modelos de classificação distintos (Random Forest e Regressão Logística) para fins de comparação.
7. **Avaliação do Modelo:** Geração de métricas de desempenho (Acurácia, Recall, AUC-ROC) e da Curva ROC para determinar o melhor modelo preditivo.

---

## 4. Principais Decisões Tomadas Durante o Tratamento dos Dados
Para garantir a qualidade dos dados alimentados no modelo, as seguintes decisões técnicas foram aplicadas:
* **Remoção de Nulos Críticos:** A coluna `company` foi completamente excluída, pois apresentava mais de 90% de valores nulos, o que não agregaria valor estatístico.
* **Imputação de Dados:** Valores nulos na coluna `agent` foram preenchidos com `0` (assumindo que a reserva foi feita sem agente). Valores nulos em `country` foram preenchidos com a *tag* `'Unknown'`, e em `children` preenchidos com `0`.
* **Tratamento de Outliers:** Foi identificado visualmente (via Boxplot) e estatisticamente um erro de sistema na coluna `adr` (Tarifa Diária Média), onde uma reserva possuía o valor irreal de $5.400. Essa linha foi removida para evitar distorções na média e no treinamento do modelo.
* **Limpeza Lógica:** Linhas onde a soma de adultos, crianças e bebês resultava em zero foram removidas, por se tratarem de erros de inserção do sistema do hotel.
* **Prevenção de *Data Leakage*:** As colunas `reservation_status` e `reservation_status_date` foram removidas antes do treinamento, pois revelam o desfecho exato da reserva, o que enviesaria o modelo preditivo a obter 100% de acerto de forma ilusória.

---

## 5. Principais Insights Obtidos a Partir da Análise Exploratória
Durante a etapa de AED, utilizando agrupamentos e visualizações, foram observados os seguintes comportamentos:
* **Visão Geral:** O `City Hotel` recebe um volume absoluto muito maior de reservas do que o `Resort Hotel`, mas também sofre com uma taxa de cancelamento proporcionalmente mais agressiva.
* **Impacto da Antecedência (Lead Time):** O gráfico de densidade comprovou que reservas feitas com poucos dias de antecedência têm baixíssima probabilidade de cancelamento. Em contrapartida, quanto maior o *lead time* (reservas feitas com meses de antecedência), maior é o risco de cancelamento.
* **Sazonalidade:** Os meses de pico (Julho e Agosto, verão europeu) concentram os maiores volumes de reservas, mas também disparam o volume absoluto de cancelamentos.
* **Influência do Depósito:** Surpreendentemente, reservas categorizadas como "Non Refund" (Não Reembolsável) apresentam uma grande quantidade de cancelamentos na base. O tipo de depósito provou ser um indicativo muito forte do desfecho da reserva.
* **Análise Geográfica:** Portugal (PRT) lidera com ampla margem tanto em número de clientes totais quanto em volume de cancelamentos, seguido por Grã-Bretanha, França e Espanha.

---

## 6. Análise dos Resultados do Modelo Preditivo
Para o desafio de prever se um cliente cancelará a reserva (`is_canceled`), foram treinados dois modelos supervisionados de classificação: **Random Forest Classifier** e **Regressão Logística**.

Para a avaliação, o foco principal foi o **Recall (Revocação)** e o **AUC-ROC**, visto que no setor hoteleiro, não prever um cancelamento (falso negativo) gera perda de receita irrecuperável. 

**Resultados Alcançados:**
* **Regressão Logística:** Apresentou uma Acurácia de ~81.42% e um Recall de ~66.07%, com AUC-ROC de 0.8952. Trata-se de um modelo decente, porém conservador, deixando passar cerca de 34% dos cancelamentos reais.
* **Random Forest:** Apresentou uma Acurácia de **~89.17%**, Recall altíssimo de **~80.83%** e um AUC-ROC quase perfeito de **0.9584**.

**Conclusão:**
O modelo **Random Forest** foi o grande vencedor e é o modelo recomendado para implantação. Por ser um modelo baseado em árvores de decisão, ele conseguiu capturar excelentes relações não-lineares entre as variáveis complexas dos hóspedes. Ele detectou previamente 81% de todos os cancelamentos reais na base de testes, o que permitiria ao hotel implementar políticas de *overbooking* dinâmico com grande segurança, maximizando sua receita.
