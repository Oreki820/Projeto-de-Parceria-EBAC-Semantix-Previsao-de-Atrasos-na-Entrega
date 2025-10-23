# 🪐 Projeto de Parceria EBAC + Semantix  
## 📦 Previsão de Atrasos de Entrega — Base Olist (Kaggle)

---

### 👨‍💻 Autor  
**Lucas Gabriel Ferreira Gomes**  
📍 Santana do Livramento — RS, Brasil  
📧 lucasgabrielferreiragomes10@gmail.com  
📅 Ano: 2025  
🎓 Curso: Profissão: Cientista de Dados — EBAC  

---

## 🎯 1. Introdução e Contexto do Projeto

Este projeto foi desenvolvido como parte da **Parceria EBAC + Semantix** e tem como objetivo aplicar de forma prática as técnicas de **Ciência de Dados, Estatística, Machine Learning e Storytelling Analítico** aprendidas ao longo do curso.

### 💡 Problemática

Atrasos de entrega são um problema real e frequente em empresas de e-commerce, afetando custos, logística e satisfação dos clientes.  
Com base nisso, o desafio proposto foi:

> **“Como prever, com base em dados históricos, se um pedido será entregue com atraso?”**

O projeto utiliza a base pública **Olist Brazilian E-Commerce** (Kaggle), contendo milhares de pedidos de um marketplace real brasileiro.

---

## 📥 2. Coleta e Origem dos Dados

- **Fonte:** [Olist Brazilian E-Commerce (Kaggle)](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Tipo:** Dados públicos  
- **Arquivos utilizados:**
  - `olist_orders_dataset.csv`
  - `olist_order_items_dataset.csv`
  - `olist_order_payments_dataset.csv`
  - `olist_customers_dataset.csv`
  - `olist_products_dataset.csv`
  - `product_category_name_translation.csv`

Os dados foram organizados segundo o modelo de arquitetura em camadas:

data/

├── raw/ → dados brutos

├── silver/ → dados tratados e validados

└── gold/ → features finais para modelagem


> 🧱 *A coleta foi feita via Kaggle API, seguida de transformações e verificações com Great Expectations.*

---

## 🧹 3. Preparação e Limpeza dos Dados (Camada Silver)

Nesta etapa foram realizadas:
- Correção de nulos e duplicatas;
- Mesclagem de tabelas (`orders`, `items`, `payments`, `customers`, `products`);
- Criação da variável-alvo `is_late` (1 = pedido atrasado);
- Filtragem de cancelamentos e outliers;
- Validação automática com **Great Expectations**.

💾 *Arquivo resultante:* `orders_enriched_clean.parquet`

---

## 💎 4. Engenharia de Atributos e Feature Store (Camada Gold)

Com base nos dados limpos, foram criadas novas variáveis (features) para enriquecer o modelo:

| Tipo | Variável | Descrição |
|------|-----------|-----------|
| Temporal | `promised_days`, `delivered_days`, `month`, `dow` | Prazo prometido, real e sazonalidade |
| Logística | `freight_value`, `n_items`, `distance_estimate` | Fatores de custo e complexidade de entrega |
| Financeira | `pay_value`, `installments`, `payment_type` | Valor e forma de pagamento |
| Categórica | `main_category` | Tipo principal de produto |
| Target | `is_late` | Indicador binário de atraso |

🎯 *Objetivo:* construir uma base preditiva robusta para identificar padrões logísticos e financeiros associados aos atrasos.

---

## ⚙️ 5. Modelagem e Avaliação

### 🔹 Modelos Utilizados
| Modelo | Tipo | Características |
|:--|:--|:--|
| 🌳 **Decision Tree** | Base | Interpretação simples, usada como benchmark |
| ⚡ **XGBoost** | Avançado | Ensemble com regularização e tuning fino |

### 🔹 Estratégia de Treinamento
- Divisão temporal (evita *data leakage*):
  - **Treino:** até 2017-06  
  - **Validação:** 2017-07 a 2017-09  
  - **Teste:** após 2017-09
- Pipelines com `ColumnTransformer`, `SimpleImputer` e `OneHotEncoder`.

### 🔹 Métricas de Avaliação

| Modelo | AUC | F1 | Recall | Precision |
|:--|--:|--:|--:|--:|
| Decision Tree | 0.9935 | 0.8266 | 0.7750 | 0.8855 |
| **XGBoost** | **0.9996** | **0.9400** | **0.9775** | **0.9053** |

> 🏆 **Melhor desempenho:** XGBoost — recall de **97,7%** e precisão de **90,5%**, indicando alta capacidade de identificar atrasos reais.

---

## 📈 6. Visualização de Dados (EDA & Explicabilidade)

As visualizações foram desenvolvidas tanto em **Plotly** (notebook) quanto em **Looker Studio (Dashboard)**.

### 🔸 Gráficos da EDA
1. **% de atrasos por mês** → revela picos sazonais (Black Friday, Natal).  
2. **% de atrasos por estado** → gargalos logísticos no Norte e Nordeste.  
3. **Dias prometidos × atraso** → prazos curtos (≤ 5 dias) = alto risco.  
4. **Categoria × atraso** → móveis e eletrônicos atrasam mais.  
5. **Valor pago × atraso** → pedidos caros tendem a atrasar.

### 🔸 Explicabilidade (Permutation Importance)
- `promised_days` e `delivered_days` são as variáveis mais relevantes;  
- `freight_value` e `total_price` influenciam secundariamente;  
- `installments` indica relação entre crédito e risco.

---

## 📊 7. Dashboard Interativo — Looker Studio

Explore o painel com indicadores e filtros dinâmicos:

🔗 **Acesso direto:**  
[📊 Visualizar Dashboard](https://lookerstudio.google.com/reporting/ef01fad6-5056-457a-b2cb-561cc0af28e2/page/BuhcF)

---

## 🧠 8. Principais Insights

| Dimensão | Padrão Observado | Interpretação Estratégica |
|-----------|------------------|----------------------------|
| **Tempo (Sazonalidade)** | Atrasos concentrados em meses de alta demanda | Planejar capacidade e ajustar prazos |
| **Geografia** | Norte e Nordeste mais suscetíveis | Reforçar hubs e rotas locais |
| **Prazo (SLA)** | Entregas prometidas em ≤ 5 dias atrasam mais | Calibrar prazos realistas |
| **Categoria** | Produtos grandes e frágeis = mais atrasos | Otimizar transporte e embalagem |
| **Financeiro** | Pedidos caros e parcelados = maior risco | Monitorar pedidos premium |

---

## 🧾 9. Conclusões Gerais

O modelo XGBoost demonstrou alta eficiência e precisão, permitindo prever atrasos com antecedência e apoiar decisões operacionais.  
O uso de dados possibilita:

- Reduzir custos de entrega e retrabalho;
- Melhorar a experiência do cliente;
- Otimizar políticas de SLA e rotas logísticas;
- Aplicar previsões automáticas em dashboards corporativos.

> 📈 *Conclusão:* Dados, quando bem estruturados, transformam a logística em uma operação previsível e eficiente.

---

## ⚖️ 10. Licença e Autorização

> **Autorização LGPD e Cessão de Uso — Projeto de Parceria Semantix**
>
> Eu, **Lucas Gabriel Ferreira Gomes**, portador do CPF **041.526.820.60**,  
> autorizo a cessão do meu projeto em favor da **Semantix**, bem como a divulgação do meu nome como autor responsável pelo projeto,  
> para fins de exibição e portfólio profissional.  
> 
> Declaro também que este trabalho foi desenvolvido de forma independente, sem vínculo empregatício ou remuneração,  
> e autorizo o uso dos meus dados de contato para eventual comunicação com a empresa.

> 📜 **Licença do Repositório:** MIT License  
> Uso livre para fins educacionais, desde que mantidos os créditos originais.  
> Dataset público: *Olist Brazilian E-Commerce (Kaggle)*.  
> Direitos de marca pertencem à Olist e Semantix.

---

## 🙏 11. Agradecimentos

Agradecimentos à **EBAC** e à **Semantix** pela oportunidade de aprendizado e desenvolvimento profissional,  
e à comunidade de cientistas de dados que compartilha conhecimento diariamente.

> 🪞 *"Prometer menos, planejar melhor e entregar no prazo — essa é a ciência dos dados aplicada à logística."*

---

### 🧩 Contatos

- 📧 **E-mail:** lucasgabrielferreiragomes10@gmail.com  
- 💼 **LinkedIn:** (www.linkedin.com/in/lucas-gabriel-dados)  
- 💻 **GitHub:** (https://github.com/Oreki820)
---


