![FIAP](https://www.fiap.com.br/wp-content/themes/fiap2016/images/sharing/fiap.png)

# Análise de Desempenho Logístico no E-commerce Brasileiro - Olist

### Tech Challenge - Fase 1 | Pós-Tech Data Analytics

Análise exploratória de dados do e-commerce brasileiro utilizando o dataset público da Olist, com foco em eficiência logística, satisfação do cliente e impacto regional no desempenho de vendas.


---

## Introdução

A Olist é uma plataforma brasileira de e-commerce que conecta pequenos e médios comerciantes aos maiores marketplaces do país. Fundada em 2015, a empresa democratizou o acesso ao comércio eletrônico, permitindo que lojistas vendam seus produtos em múltiplos canais com uma única integração.

Este projeto tem como objetivo analisar dados disponibilizados pela Olist referentes ao período de **setembro de 2016 a agosto de 2018**, investigando a relação entre distribuição geográfica, eficiência logística e satisfação do cliente no contexto do e-commerce brasileiro.

---

## Contexto

### Crescimento do E-commerce no Brasil

- **Mundial**: Crescimento de 24% na década de 2010, atingindo US$ 2,9 trilhões em vendas
- **América Latina**: Crescimento aproximado de 17% no mesmo período
- **Brasil**: Evolução de R$ 6 bilhões entre 2015 e 2017 (fonte: EBIT, 2019)

### Expansão da Olist

Durante o período analisado, a Olist demonstrou crescimento consistente:
- **Receita total**: R$ 13,5 milhões
- **Crescimento médio mensal**: 15% no volume de pedidos (2017-2018)
- **Ticket médio estável**: ~R$ 160
- **Pico de vendas**: Novembro de 2017 (Black Friday)

### Concentração Regional

- **Sudeste**: 64,6% da receita total
  - São Paulo: 41% da receita nacional
- **Sul**: 14,5%
- **Nordeste**: 11,8%
- **Norte**: 2,6% (maior oportunidade de crescimento)
- **Centro-Oeste**: 6,5%

---

## Problema

### Desafios Logísticos no Brasil

Dada a vasta extensão territorial brasileira, o e-commerce enfrenta desafios significativos:

1. **Disparidades de Frete**
   - Sudeste: ~R$ 20 (média)
   - Norte: ~R$ 41 (média) - **105% mais caro**

2. **Variação de Prazos (SLA)**
   - Sudeste: 8 dias (média)
   - Norte: 22 dias (média) - **175% mais lento**
   - Nordeste: 19,5 dias (média)

3. **Impacto na Satisfação**
   - 51% dos abandonos de carrinho relacionados ao frete (NSTECH, 2024)
   - 58% dos consumidores não retornam após experiência negativa (CX Trends, 2024)
   - 28% das insatisfações causadas por atrasos na entrega

### Pergunta Norteadora

> **"Regiões mais afastadas dos centros de distribuição tendem a apresentar avaliações inferiores devido a problemas logísticos?"**

### Hipótese

O crescimento de vendas é concentrado na região Sudeste do Brasil, e regiões mais distantes dos centros de distribuição tendem a apresentar menor volume de compras e avaliações inferiores devido a problemas logísticos, incluindo prazos de entrega mais longos e custos de frete elevados.

---

## Objetivos

### Objetivo Geral

Analisar o desempenho logístico do e-commerce brasileiro através do estudo de caso da plataforma Olist, investigando a relação entre distribuição geográfica, eficiência operacional e satisfação do cliente.

---

## Preparação dos Dados

### Fonte dos Dados

**Dataset**: [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

**Período**: Setembro de 2016 - Agosto de 2018  
**Formato**: CSV  
**Total de tabelas**: 8

### Estrutura dos Dados

| Tabela | Descrição | Registros |
|--------|-----------|-----------|
| `olist_orders_dataset` | Informações dos pedidos | ~100k |
| `olist_order_items_dataset` | Itens de cada pedido | ~112k |
| `olist_order_payments_dataset` | Dados de pagamento | ~103k |
| `olist_order_reviews_dataset` | Avaliações dos clientes | ~99k |
| `olist_customers_dataset` | Informações dos clientes | ~99k |
| `olist_sellers_dataset` | Dados dos vendedores | ~3k |
| `olist_products_dataset` | Catálogo de produtos | ~32k |
| `olist_geolocation_dataset` | Dados geográficos | ~1M |

### Tratamento de Dados

#### 1. Limpeza
- Remoção de valores nulos em colunas críticas
- Tratamento de datas inconsistentes
- Normalização de textos (reviews)

#### 2. Feature Engineering
```python
# Cálculo de SLA (Service Level Agreement)
df['sla_days'] = (df['order_delivered_customer_date'] - 
                  df['order_purchase_timestamp']).dt.days

# Status de atraso
df['status_sla'] = df.apply(
    lambda x: 'Atrasado' if x['order_delivered_customer_date'] > 
              x['order_estimated_delivery_date'] else 'No Prazo', axis=1
)

# Diferença de dias (atraso)
df['delay_days'] = (df['order_delivered_customer_date'] - 
                    df['order_estimated_delivery_date']).dt.days
```

#### 3. Enriquecimento Geográfico
- Mapeamento de CEPs para estados e regiões
- Cálculo de distâncias entre seller e customer
- Integração com shapefiles do Brasil (GeoPandas)

---

## Análises Realizadas

### 1. **Crescimento do Negócio**

**Análises**:
- Evolução temporal de receita e pedidos
- Cálculo de ticket médio (AoV)
- Identificação de sazonalidade

**Descobertas**:
- Crescimento médio mensal de 15%
- Ticket médio estável em R$ 160
- Pico em novembro/2017 (Black Friday)

### 2. **Expansão Regional**

**Análises**:
- Distribuição de receita por região/estado
- Mapeamento geográfico de vendas (GeoPandas)
- Análise de concentração de sellers

**Descobertas**:
- Sudeste: 64,6% da receita
- 83,7% dos sellers concentrados no Sudeste
- Norte: apenas 2,6% da receita (oportunidade!)

**Visualização**:
```python
import geopandas as gpd
import matplotlib.pyplot as plt

# Carregar shapefile do Brasil
brasil = gpd.read_file('brasil_estados.shp')

# Merge com dados de receita
brasil_vendas = brasil.merge(receita_estado, on='sigla_uf')

# Plot
fig, ax = plt.subplots(1, 1, figsize=(12, 10))
brasil_vendas.plot(column='receita', cmap='Blues', 
                   legend=True, ax=ax)
plt.title('Receita por Estado (R$ milhões)')
plt.show()
```

### 3. **Eficiência Logística**

**Análises**:
- SLA médio e mediana por região
- Taxa de atraso por estado
- Correlação entre distância e prazo
- Análise de custo de frete

**Descobertas**:
- Norte: 22 dias (SLA) vs Sudeste: 8 dias
- Nordeste: maior taxa de atraso (12,8%)
- Frete Norte: 105% mais caro que Sudeste

**Código de Análise**:
```python
# Análise de SLA por região
sla_regiao = df.groupby('customer_region').agg({
    'sla_days': ['mean', 'median', 'std'],
    'delay_days': 'mean',
    'order_id': 'count'
}).round(2)

# Taxa de atraso
taxa_atraso = df.groupby('customer_region')['status_sla'].apply(
    lambda x: (x == 'Atrasado').sum() / len(x) * 100
).round(2)
```

### 4. **Satisfação do Cliente**

**Análises**:
- Distribuição de review scores
- Correlação entre atraso e avaliação
- Impacto do frete na satisfação
- Análise por região

**Descobertas**:
- Nota média geral: 4,08
- Pedidos atrasados: 2,8 (média) vs No prazo: 4,2
- **Queda de 35% na satisfação** quando há atraso
- Norte/Nordeste: avaliações inferiores

**Visualização**:
```python
import seaborn as sns

# Comparação de reviews: atrasado vs no prazo
fig, ax = plt.subplots(1, 2, figsize=(14, 5))

# Gráfico 1: Distribuição geral
sns.histplot(df['review_score'], bins=5, kde=False, ax=ax[0])
ax[0].set_title('Distribuição de Review Score')

# Gráfico 2: Por status de SLA
sns.boxplot(data=df, x='status_sla', y='review_score', ax=ax[1])
ax[1].set_title('Review Score: Atrasado vs No Prazo')

plt.tight_layout()
plt.show()
```

### 5. **Análise de Sentimento (NLP)**

**Técnicas**:
- Tokenização de texto com NLTK
- Remoção de stopwords (português)
- Normalização com unidecode
- Análise de frequência de palavras
- Wordcloud de termos positivos/negativos

**Descobertas**:
- Termos logísticos: 40% das palavras frequentes (todas reviews)
- Termos logísticos: 67% em reviews negativas (1-2 estrelas)
- Palavras-chave negativas: "prazo", "atraso", "demora", "frete"
- Palavras-chave positivas: "entrega", "produto", "recomendo", "qualidade"

**Código NLP**:
```python
import nltk
from nltk.corpus import stopwords
from unidecode import unidecode
import re
from collections import Counter

# Download de recursos
nltk.download('stopwords')
stop_words = set(stopwords.words('portuguese'))

# Função de limpeza
def limpar_texto(texto):
    if pd.isna(texto):
        return ""
    # Lowercase e normalização
    texto = unidecode(texto.lower())
    # Remove pontuação
    texto = re.sub(r'[^\w\s]', '', texto)
    # Tokenização e remoção de stopwords
    palavras = [p for p in texto.split() if p not in stop_words and len(p) > 2]
    return palavras

# Aplicar em reviews negativas (1-2 estrelas)
reviews_negativas = df[df['review_score'] <= 2]['review_comment_message']
palavras_negativas = []

for review in reviews_negativas:
    palavras_negativas.extend(limpar_texto(review))

# Contar frequência
freq_negativas = Counter(palavras_negativas).most_common(15)

# Resultados
# Top palavras negativas: prazo, atraso, entrega, demora, frete...
```

### 6. **Taxa de Recompra e Retenção**

**Análises**:
- Análise de coorte de retenção
- Coorte específico: clientes com primeira compra atrasada
- Correlação entre experiência inicial e recompra

**Descobertas**:
- Taxa de recompra geral: **2,9%** (muito baixa!)
- Primeira compra atrasada: **100% de churn**
- Primeira compra antecipada: 6,2% recompram
- Primeira compra no prazo: 3,8% recompram

**Análise de Coorte**:
```python
# Identificar primeira compra de cada cliente
primeira_compra = df.sort_values('order_purchase_timestamp').groupby('customer_id').first()

# Contar recompras
total_compras = df.groupby('customer_id').size()
recompra = (total_compras > 1).sum()

taxa_recompra = recompra / len(total_compras) * 100
print(f"Taxa de recompra: {taxa_recompra:.2f}%")

# Coorte por status da primeira compra
primeira_atrasada = primeira_compra[primeira_compra['status_sla'] == 'Atrasado']
clientes_atrasados = primeira_atrasada.index

recompra_atrasados = total_compras[total_compras.index.isin(clientes_atrasados)]
taxa_recompra_atrasados = (recompra_atrasados > 1).sum() / len(recompra_atrasados) * 100

print(f"Taxa recompra (primeira atrasada): {taxa_recompra_atrasados:.2f}%")
```

---

## Principais Insights

### 1. **Concentração Regional é Crítica**
- **83,7% dos sellers** no Sudeste
- **64,6% da receita** no Sudeste
- **Oportunidade**: Norte/Nordeste/Centro-Oeste = 64% território + 46% população = apenas 25% receita

### 2. **Logística Define Satisfação**
- Atraso reduz nota de **4,2 → 2,8** (queda de 35%)
- Norte: SLA de 22 dias vs Sudeste 8 dias
- Nordeste: maior taxa de atraso (12,8%)


### 3. **Frete é Barreira de Conversão**
- 51% dos abandonos de carrinho por frete alto
- Norte: R$ 41 vs Sudeste: R$ 20 (105% mais caro)

### 4. **Primeira Impressão é Definitiva**
- Taxa de recompra geral: **2,9%** (péssima!)
- Primeira compra atrasada: **100% de churn**

### 5. **Cliente Não É Neutro**
- Distribuição bimodal: 57,8% dão nota 5 OU 11,5% dão nota 1
- Notas intermediárias (2-3): apenas 11,4%
- Quando insatisfeito, cliente dá nota mínima (protesto)

### 6. **NLP Revela Dor do Cliente**
- 67% das palavras em reviews negativas são sobre logística
- Top palavras negativas: "prazo", "atraso", "demora", "frete"
- Top palavras positivas: "entrega", "recomendo", "qualidade"

---

## Recomendações Estratégicas

### 1. SLA

- Revisar os prazos prometidos para as regiões Norte e Nordeste aumentando a margem de segurança em 20% para transformar pedidos atrasados em entregas no prazo, atendo a expectativa do cliente e elevando a nota média sem alterar a operação física.
- Automatizar o disparo de cupons de desconto exclusivos para clientes que sofreram atraso para quebrar a tendência de 100% de churn, incentivando uma segunda chance à plataforma e gerar recomendações pelo pós-compra.
- Permitir subsídios de frete (Frete Grátis ou Reduzido) como ferramenta de conversão para compensar prazos de entrega mais longos em regiões de difícil acesso.

### 2. Sellers

- Criar incentivos ou campanhas para a entrada de novos sellers em outras regiões, principalmente Norte e Nordeste, reduzindo a distância entre o estoque e o consumidor final.
- Implementar um sistema de pontuação para sellers baseado em performance logística e review score. Exemplo: A, B, C, D e E.
   - A/B: Prioridade em algoritmos de busca e parcerias de frete.
   - C/D/E: Acesso obrigatório a trilhas de treinamento focada em logística e proteção do produto.
- Implementar um indicador de confiança para sellers baseada no sistema de pontuação, reduzindo a ansiedade e insegurança do comprador.

---

## Como Executar

### Pré-requisitos

- Python 3.10 ou superior
- pip (gerenciador de pacotes Python)
- Jupyter Notebook (opcional)

### 1. Clone o Repositório

```bash
git clone https://github.com/viniromao159/olist-ecommerce-analysis.git
cd olist-ecommerce-analysis
```

### 2. Crie um Ambiente Virtual (Recomendado)

```bash
# Criar ambiente virtual
python -m venv venv

# Ativar ambiente virtual
# Windows
venv\Scripts\activate

# Linux/Mac
source venv/bin/activate
```

### 3. Instale as Dependências

```bash
pip install -r requirements.txt
```

### 4. Download do Dataset

1. Acesse: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce
2. Faça o download do dataset
3. Extraia os arquivos CSV para a pasta `data/raw/`

### 5. Download dos Shapefiles (Opcional - para mapas)

```bash
# Download do shapefile do Brasil
# Fonte: IBGE
# Link: https://geoftp.ibge.gov.br/organizacao_do_territorio/malhas_territoriais/

# Extrair para data/geo/
```

### 6. Execute o Notebook Principal

```bash
# Iniciar Jupyter Notebook
jupyter notebook

# Abrir: notebooks/Tech_Challenge_Final_V2.ipynb
```

### 7. Ou Execute Scripts Python

```bash
# Exemplo: rodar análise completa
python src/main.py
```

---

## Autor

**Vinicius Lopes Romão**

## Referências

### Dados
- **Olist**. Brazilian E-Commerce Public Dataset. Kaggle, 2018. Disponível em: https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce

### Estudos e Relatórios
- **EBIT | Nielsen**. Webshoppers 39ª edição. São Paulo, 2019.
- **NSTECH & Frete Rápido**. Panorama da Gestão Logística no E-commerce Brasileiro, 2024.
- **CX Trends**. Customer Experience Trends Report, 2024.
- **IBGE**. Censo Demográfico 2022. Disponível em: https://censo2022.ibge.gov.br/

### Técnicas e Metodologias
- **GeoPandas Documentation**. Disponível em: https://geopandas.org/
- **NLTK Documentation**. Natural Language Toolkit. Disponível em: https://www.nltk.org/
- **Python for Data Analysis** (Wes McKinney, 2022)
- **Storytelling with Data** (Cole Nussbaumer Knaflic, 2015)

---

## 📄 Licença

Este projeto está licenciado sob a **Licença MIT** - veja o arquivo [LICENSE](LICENSE) para detalhes.

```
MIT License

Copyright (c) 2026 Vinicius Lopes Romão

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---