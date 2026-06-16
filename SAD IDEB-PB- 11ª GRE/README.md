# 📊 Data Mart IDEB & Censo Escolar - Análise de Desempenho da 11° GRE da Paraíba

Este projeto consiste no desenvolvimento de um **Data Mart (Data Warehouse)** focado na análise dos microdados do IDEB (Índice de Desenvolvimento da Educação Básica) e do Censo Escolar do INEP. A solução engloba todo o pipeline de dados: desde a extração dos arquivos brutos (CSV), passando pelo processo de **ETL (Pentaho Spoon)**, modelagem e armazenamento em banco de dados relacional **(PostgreSQL)**, até a disponibilização para análise OLAP no **Power BI**.

---

## 🏗️ Arquitetura do Projeto

O projeto segue a arquitetura clássica de Business Intelligence, utilizando uma abordagem de modelagem dimensional em **Star Schema (Esquema Estrela)** para otimizar a performance de consultas analíticas.

### Ferramentas Utilizadas
* **ETL:** Pentaho Data Integration (PDI / Spoon) versão 9.4
* **Banco de Dados:** PostgreSQL 
* **Visualização & OLAP:** Power BI Desktop

---

## 📐 Modelagem Dimensional

O modelo é composto por uma **Tabela Fato** central cercada por **4 Tabelas de Dimensão**, garantindo a integridade dos dados por meio de uma **Chave Primária Composta** na tabela fato (sem a necessidade de IDs artificiais de auto-incremento).

### Tabelas de Dimensão (`dim`)
* `dim_tempo`: Armazena o histórico dos anos avaliados.
* `dim_local`: Contém a hierarquia geográfica (Estado, Região, Cidade, Bairro) e o cadastro das Escolas (Código e Nome).
* `dim_ensino`: Segmentação por etapas da Educação Básica (Ensino Médio, Fundamental Inicial, Fundamental Final).
* `dim_administrativa`: Classificação da dependência administrativa da instituição (Estadual, Municipal, Federal, Privada).

### Tabela Fato (`fato`)
* `fato_desempenho`: Centraliza as métricas e chaves estrangeiras (`fk_tempo`, `fk_local`, `fk_ensino`, `fk_administrativa`).

---

## ⚙️ Processo de ETL (Pentaho)

O pipeline de dados foi desenhado para processar múltiplos anos de microdados (mais de 1 milhão de registros brutos combinados), realizando as seguintes limpezas:
1. **Unificação de Arquivos:** Consolidação de diferentes anos usando steps de *Append Streams*.
2. **Tratamento de Dados Ausentes (Nulos):** Remoção de strings de escape e hífens (`-`) oficiais do INEP, convertendo-os em valores `NULL` reais no banco de dados para não distorcer os cálculos estatísticos.
3. **Remoção de Duplicados:** Utilização de steps de ordenação (*Sort Rows*) e registros únicos (*Unique Rows*) para garantir que a dimensão local possua apenas escolas únicas.

