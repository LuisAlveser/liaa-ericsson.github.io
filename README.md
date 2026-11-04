# 🛡️ Sistema de Detecção de Intrusão em Rede (NIDS) com Machine Learning — CIC-IDS-2018

Este repositório contém a pipeline completa de tratamento de dados, deduplicação temporal, treinamento e avaliação comparativa de modelos de Machine Learning (Regressão Logística vs. Random Forest) aplicados ao dataset de segurança cibernética **CSE-CIC-IDS2018**.

---

## 📌 1. Contextualização do Dataset

O dataset **CSE-CIC-IDS2018** simula uma infraestrutura corporativa real composta por:
* **Infraestrutura:** 5 departamentos, 420 máquinas e 30 servidores alvos, além de 50 máquinas atacantes[cite: 1].
* **Captura:** Tráfego bruto capturado e processado via **CIC FlowMeter-V3**, gerando 80+ características estatísticas bidirecionais (*Biflow*) por fluxo[cite: 1].
* **Cenários de Ataque Incluídos:** Força Bruta (FTP/SSH/Web/XSS), Heartbleed, Botnet, DoS (Hulk, SlowHTTPTest, GoldenEye, Slowloris), DDoS (LOIC-HTTP, LOIC-UDP, HOIC), Infiltração Interna e SQL Injection[cite: 1, 11].

---

## 🧹 2. Engenharia de Dados & Pré-processamento

Dada a dimensão original do dataset (~16,1 milhões de registros)[cite: 10, 16], foram implementadas otimizações rigorosas para execução sem estouro de memória RAM (ex: Google Colab com restrição de 12.7 GB)[cite: 16, 20]:

1. **Otimização de Tipos de Dados:** Conversão de `float64` para `float32`[cite: 10].
2. **Tratamento de Nulos e Infinitos:** Limpeza confirmada via DuckDB (0 instâncias com `NaN`/`Inf` no dataset final)[cite: 10, 29, 30].
3. **Seleção de Atributos:**
   * Exclusão de 8 colunas constantes que geravam ruído[cite: 10].
   * Remoção de identificadores diretos para evitar *overfitting* (`Flow ID`, `Src IP`, `Src Port`, `Dst IP`, `Dst Port`)[cite: 10, 30].
   * Manutenção da porta de origem/destino e metadados temporais durante a partição[cite: 10, 16, 30].
4. **Deduplicação Global & Temporal:** Remoção de duplicatas exatas considerando a ordem cronológica via `Timestamp`[cite: 20, 28, 29].
5. **Persistência Eficiente:** Exportação dos subconjuntos finais em formato `.parquet` gerenciados via **DuckDB**[cite: 10, 16, 24, 29].