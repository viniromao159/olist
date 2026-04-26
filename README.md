![FIAP](https://www.fiap.com.br/wp-content/themes/fiap2016/images/sharing/fiap.png)

# Análise de Desempenho Logístico no E-commerce Brasileiro - Olist

### Tech Challenge - Fase 1 | Pós-Tech Data Analytics

Análise exploratória de dados do e-commerce brasileiro utilizando o dataset público da Olist, com foco em eficiência logística, satisfação do cliente e impacto regional no desempenho de vendas.

---

## Sumário

- [Introdução](#introdução)
- [Contexto](#contexto)
- [Problema](#problema)
- [Objetivos](#objetivos)
- [Preparação dos Dados](#preparação-dos-dados)
- [Análises Realizadas](#análises-realizadas)
- [Principais Insights](#principais-insights)
- [Recomendações Estratégicas](#recomendações-estratégicas)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Como Executar](#como-executar)
- [Resultados](#resultados)
- [Conclusão](#conclusão)
- [Autor](#autor)
- [Licença](#licença)

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

### Objetivos Específicos

1. **Mapear a distribuição regional** de vendas, sellers e clientes
2. **Analisar a eficiência logística** por região (SLA, taxa de atraso, custo de frete)
3. **Identificar correlações** entre performance logística e satisfação do cliente
4. **Realizar análise de sentimento** em avaliações de clientes (NLP)
5. **Avaliar impacto do atraso** na taxa de retenção e recompra
6. **Propor recomendações estratégicas** para otimização operacional

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

### Schema das Tabelas Principais

#### Orders (Pedidos)
```
order_id                    : Identificador único do pedido
customer_id                 : Identificador do cliente
order_status                : Status do pedido
order_purchase_timestamp    : Data/hora da compra
order_approved_at           : Data/hora da aprovação
order_delivered_carrier_date: Data/hora de postagem
order_delivered_customer_date: Data/hora de entrega
order_estimated_delivery_date: Data estimada de entrega
```

#### Order Items (Itens do Pedido)
```
order_id          : Identificador do pedido
order_item_id     : Número sequencial do item
product_id        : Identificador do produto
seller_id         : Identificador do vendedor
shipping_limit_date: Data limite para envio
price             : Preço do item
freight_value     : Valor do frete
```

#### Reviews (Avaliações)
```
review_id            : Identificador único da avaliação
order_id             : Identificador do pedido
review_score         : Nota de 1 a 5 estrelas
review_comment_title : Título do comentário
review_comment_message: Texto do comentário
review_creation_date : Data de criação da avaliação
review_answer_timestamp: Data de resposta
```

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

### 1. **Programa de Incentivo a Sellers Locais**

**Objetivo**: Reduzir SLA e custo de frete via sellers regionais

**Implementação**:
- Onboarding facilitado com taxas reduzidas (6 meses) para sellers Norte/Nordeste
- Badge "Entrega Local" destacando sellers da mesma região
- Ranqueamento com peso de 15-20% para sellers locais

**Impacto Esperado**:
- Redução de 30-40% no SLA médio Norte/Nordeste
- Diminuição de 25% nos custos de frete
- Aumento de 0,5 estrelas na avaliação média
- Crescimento de 40-60% no GMV dessas regiões (18 meses)

### 2. **Sistema de Frete Inteligente e Dinâmico**

**Objetivo**: Otimizar custos e percepção de valor do frete

**Implementação**:
- ML model para precificação dinâmica (distância, peso, histórico, sazonalidade)
- Programa "Frete Progressivo": subsídio parcial em fretes >R$ 30 (Norte/Nordeste)
- Opção "Frete Econômico": +5-7 dias com 40-50% desconto

**Impacto Esperado**:
- Redução de 15-20% no custo médio de frete
- Aumento de 8-12% na taxa de conversão
- Redução de 30% em menções negativas sobre frete
- ROI de 180% em 12 meses

### 3. **SLA Segmentado por Região**

**Objetivo**: Alinhar expectativas com realidade logística

**Implementação**:
- Substituir prazo único por prazos regionalizados + margem de 15%
- Comunicação proativa: notificações em tempo real (separação, postagem, trânsito)
- Sistema "Entrega Garantida": sellers podem optar por prazos agressivos com penalidade

**Impacto Esperado**:
- Redução de 50% na taxa de atrasos
- Aumento de 0,8 estrelas (Norte/Nordeste)
- Redução de 40% em tickets de suporte
- Melhoria de 25% na taxa de recompra

### 4. **Programa de Excelência em Sellers**

**Objetivo**: Reconhecer e incentivar sellers com melhor performance

**Implementação**:
- Sistema de tiers: Bronze, Prata, Ouro, Platinum
- Critérios: taxa de atraso <5%, avaliação >4.5, processamento <24h, cancelamento <1%
- Benefícios Platinum: destaque visual, redução de 30% nas taxas, prioridade
- "Fulfillment by Olist": armazenagem e logística para sellers premium

**Impacto Esperado**:
- Aumento de 35% em sellers Ouro/Platinum (12 meses)
- Redução de 20% no churn de sellers de alta performance
- Melhoria de 0,3 estrelas na média geral
- Aumento de 18% na conversão

### 5. **Programa Anti-Churn Logístico**

**Objetivo**: Recuperar clientes que sofreram atraso

**Implementação**:
- Automação de cupom 15-20% OFF para clientes com atraso
- Email marketing segmentado explicando melhorias
- Follow-up proativo 30 dias após o atraso

**Impacto Esperado**:
- Redução de churn de 100% → 40% (primeira compra atrasada)
- Custo de retenção < custo de aquisição (ROI positivo)

---

## Tecnologias Utilizadas

### Linguagem
- **Python 3.10+**

### Bibliotecas de Análise de Dados
```python
pandas==2.0.3          # Manipulação de dados
numpy==1.24.3          # Operações numéricas
scipy==1.11.1          # Estatística avançada
```

### Visualização
```python
matplotlib==3.7.2      # Gráficos base
seaborn==0.12.2        # Visualizações estatísticas
plotly==5.15.0         # Gráficos interativos (opcional)
```

### Análise Geoespacial
```python
geopandas==0.13.2      # Análise geográfica
shapely==2.0.1         # Geometrias
folium==0.14.0         # Mapas interativos (opcional)
```

### Processamento de Linguagem Natural
```python
nltk==3.8.1            # Processamento de texto
unidecode==1.3.6       # Normalização de caracteres
wordcloud==1.9.2       # Nuvem de palavras (opcional)
```

### Machine Learning
```python
scikit-learn==1.3.0    # Preprocessamento e análise
```

### Jupyter
```python
jupyter==1.0.0         # Ambiente de notebook
ipykernel==6.25.0      # Kernel Python
```

### Ambiente
```python
python-dotenv==1.0.0   # Variáveis de ambiente
```

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

## Conclusão

### Validação da Hipótese

**CONFIRMADA**: A análise confirma que o crescimento do e-commerce brasileiro através da Olist apresenta forte concentração regional (Sudeste: 50%+ receita), resultado direto de vantagens logísticas (prazos 54% mais rápidos, fretes 43% mais baratos).

### Principais Achados

1. **Logística é Destino**
   - Atrasos causam queda de 35% na satisfação
   - 100% de churn quando primeira compra atrasa
   - 67% das reclamações são sobre logística

2. **Desigualdade Regional**
   - Norte: 22 dias (SLA) vs Sudeste: 8 dias
   - Norte: R$ 41 (frete) vs Sudeste: R$ 20
   - Nordeste: maior taxa de atraso (12,8%)

3. **Oportunidade Massiva**
   - Norte + Nordeste + Centro-Oeste = 64% território + 46% população
   - Mas apenas 25% da receita
   - Gap de 40-60% de crescimento potencial

### Mensagem Final

> **"A democratização do e-commerce no Brasil passa necessariamente pela resolução dos desafios logísticos. Norte, Nordeste e Centro-Oeste representam 64% do território nacional e 46% da população, mas apenas 25% da receita. Este gap não é apenas um problema — é a maior oportunidade de crescimento para a Olist nos próximos anos."**

**O momento de agir é agora. Os dados não mentem: logística é destino.** 

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