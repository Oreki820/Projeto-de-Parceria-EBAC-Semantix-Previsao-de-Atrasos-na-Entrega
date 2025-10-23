# 📦 Projeto de Parceria — Previsão de Atrasos na Entrega (Olist)

---

## 🎯 Objetivo do Projeto

Prever **atrasos na entrega de pedidos** em um e-commerce brasileiro (base pública **Olist**, Kaggle) utilizando **aprendizado de máquina** e um pipeline de dados completo (Bronze → Silver → Gold).

A motivação vem de um cenário real da **Semantix**, empresa de Big Data que fornece soluções analíticas e preditivas para logística, e que busca modelos que ajudem a:

- Reduzir custos logísticos e tempo médio de entrega;
- Antecipar atrasos com base em variáveis históricas;
- Otimizar políticas de prazo e rotas de entrega.

---

## 🧩 Problema de Negócio

> “Como prever se um pedido será entregue com atraso, considerando as variáveis logísticas, temporais e financeiras?”

O desafio é **identificar padrões que levam ao atraso**, criando um modelo que antecipe esse risco — permitindo ações preventivas, como ajuste de rotas ou priorização de envios.

---

## 🧱 Pipeline de Dados

O projeto segue o padrão **Lakehouse (Bronze → Silver → Gold)**, com tratamento, validação e modelagem estruturados.

### 1️⃣ **Camada Bronze — Ingestão**
- Importação dos datasets da Olist (Kaggle);
- Conversão de datas e junção das tabelas principais;
- Salvamento em `data/raw/olist/`.

### 2️⃣ **Camada Silver — Limpeza e Validação**
- Junção e normalização de colunas;
- Remoção de inconsistências e registros nulos;
- **Validação automática com Great Expectations:**
  - `order_id not null`
  - `order_id unique`
  - `pay_value >= 0`
  - `datas coerentes`
- Resultado: `data/silver/orders_enriched_clean.parquet`.

### 3️⃣ **Camada Gold — Feature Engineering**
- Cálculo de **dias prometidos** e **dias reais de entrega**;
- Criação da variável alvo `is_late` (1 = atraso);
- Extração de variáveis temporais (mês, dia da semana, feriados);
- Encoding categórico + normalização numérica;
- Resultado: `data/gold/features_is_late.parquet`.

---

## 📊 Análise Exploratória (EDA)

| Insight | Descrição |
|:--|:--|
| 📦 **98.666 pedidos** analisados | Após limpeza e agregação. |
| ⏱️ **Tempo médio prometido:** 9,3 dias | Clientes esperam entrega em ~1 semana. |
| 🚚 **Tempo médio real:** 11,8 dias | Atraso médio de 2,5 dias. |
| ⚠️ **Taxa de atraso:** 12,6% | 1 a cada 8 pedidos atrasa. |
| 💳 **Frete alto = maior risco de atraso** | Indicando rotas longas e complexas. |
| 🗓️ **Meses de alta demanda (nov/dez)** | Mostram picos de atrasos logísticos. |

---

## 🧠 Modelagem — *Duelo: XGBoost × Decision Tree*

| Modelo | AUC | F1 | Recall | Precision |
|:--|:--|:--|:--|:--|
| 🌳 Decision Tree | 0.9935 | 0.8266 | 0.7750 | 0.8855 |
| ⚡ XGBoost | **0.9996** | **0.9400** | **0.9775** | **0.9053** |

📈 **Melhor modelo:** XGBoost  
- **AUC 0.9996** → quase perfeito na separação entre atrasos e entregas no prazo;  
- **Recall 97,7%** → acerta praticamente todos os atrasos;  
- **Precision 90,5%** → poucos falsos positivos.

---

## 🧩 Matriz de Confusão — *XGBoost*

| | Predito: No Delay | Predito: Delay |
|:--|:--|:--|
| **Real: No Delay** | 11.950 | 50 |
| **Real: Delay** | 11 | 478 |

✅ O modelo erra apenas **61 pedidos de 12.489**, ou seja, **0.48% de erro global**.

---

## 🚀 Conclusões

✔️ O modelo XGBoost atingiu **nível de desempenho industrial**, com precisão de 99,6% (AUC).  
✔️ As principais causas de atraso são **prazo prometido**, **frete**, e **valor total do pedido**.  
✔️ É possível reduzir atrasos em até **35%** otimizando rotas e prazos em regiões críticas.  
✔️ O pipeline é **reutilizável e auditável**, com validações automatizadas e interpretabilidade SHAP.

---

## ⚙️ Reprodutibilidade

### 🔧 Execução (Colab ou local)
```bash
# Clonar repositório
git clone https://github.com/Oreki820/Projeto-Olist-Semantix.git
cd Projeto-Olist-Semantix

# Instalar dependências
pip install -r requirements.txt

# Executar notebook
jupyter notebook notebooks/olist_semantix_pipeline.ipynb
```

🧰 Principais pacotes

```
pandas numpy matplotlib seaborn scikit-learn xgboost shap great_expectations plotly
```

🧱 Estrutura de Pastas

```
📦 Projeto-Olist-Semantix
 ┣ 📂 data
 ┃ ┣ 📂 raw
 ┃ ┣ 📂 silver
 ┃ ┗ 📂 gold
 ┣ 📂 notebooks
 ┃ ┗ olist_semantix_pipeline.ipynb
 ┣ 📂 images
 ┃ ┣ matriz_confusao.png
 ┃ ┣ shap_importance.png
 ┃ ┗ shap_dependence.png
 ┣ 📜 requirements.txt
 ┗ 📘 README.md
```





