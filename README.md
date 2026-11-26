# 🚀 Dynamic Pricing Engine — Omnichannel (D2C, 3P & 1P)

Motor de precificação dinâmica orientado a dados projetado para equilibrar **competitividade de mercado** e **proteção de margem líquida** em operações de e-commerce que atuam simultaneamente em **Site Próprio (D2C)**, **Marketplace 3P** e **Sell-in 1P**.

---

## 🎯 Problema de Negócio Resolvido

Operações omnichannel frequentemente sofrem com dois problemas opostos:
1. **Erosão de margem (Guerra de Robôs):** Regras simples do tipo *"cobrir o menor preço"* ignoram diferenças de comissão entre marketplaces (16%+) e site próprio (3,5%), gerando vendas com prejuízo.
2. **Perda de oportunidade:** Ignorar a ruptura de estoque de concorrentes ou a baixa cobertura de estoque próprio faz a empresa vender barato quando poderia capturar margem extra.

---

## ⚙️ Arquitetura de Decisão

O algoritmo processa cada combinação de `SKU + Canal` através de duas camadas sequenciais:

### 1. Guardrails Financeiros (Piso de Rentabilidade)
Calcula o **Preço Mínimo Absoluto** permitido por canal antes de olhar para a concorrência:
$$\text{Preço Piso} = \frac{\text{Custo Unitário} + \text{Frete Médio}}{1 - (\text{Impostos} + \text{Taxa do Canal} + \text{Margem Mínima})}$$

* **Site Próprio (`SITE_PROPRIO`):** Taxa variável de `3,5%` (gateway + antifraude).
* **Marketplace 3P (`MKT_3P`):** Comissão média de `16,0%`.
* **Marketplace 1P (`MKT_1P`):** Margem varejista + verbas de sell-out (`22,0%`).

### 2. Inteligência Competitiva & Estoque (*Aging*)
* **Filtro de Ruptura de Concorrente:** Ignora ofertas de concorrentes sem estoque ativo, evitando reduções desnecessárias de preço.
* **Estratégia por Canal:**
  * `MKT_3P`: Busca ganhar a **Buy Box** (`Menor Concorrente - R$ 0,10`), respeitando o piso.
  * `SITE_PROPRIO`: Aplica **Seguidor Seletivo** (tolera até `+2%` acima do menor preço do mercado, compensado por descontos no PIX ou parcelamento).
  * `MKT_1P`: Alinha paridade de sell-out.
* **Gatilho de Cobertura de Estoque:**
  * `< 10 dias` (Estoque crítico): Incremento de `+4%` para maximizar margem antes da ruptura.
  * `> 90 dias` (Estoque encalhado): Redução de `-4%` para acelerar giro de caixa.

---

## 📊 Resultados da Simulação Operacional

Abaixo estão os resultados reais gerados pelo motor para 6 cenários distintos de negócio:

| SKU | Produto | Canal | Estoque (Dias) | Preço Anterior | Preço Piso | Preço Sugerido | Variação | Decisão do Motor |
| :--- | :--- | :--- | :---: | :---: | :---: | :---: | :---: | :--- |
| **GEL-001** | Geladeira Frost Free 375L | `SITE_PROPRIO` | 45 | R\$ 2.899,90 | R\$ 2.503,27 | **R\$ 2.854,98** | `-1,55%` | Seguidor Seletivo D2C (+2% spread) |
| **GEL-001** | Geladeira Frost Free 375L | `MKT_3P` | 45 | R\$ 2.899,90 | R\$ 3.015,63 | **R\$ 3.015,63** | `+3,99%` | Travado no Piso de Margem (Buy Box 3P) |
| **GEL-001** | Geladeira Frost Free 375L | `MKT_1P` | 45 | R\$ 2.899,90 | R\$ 3.189,66 | **R\$ 3.189,66** | `+9,99%` | Travado no Piso de Margem (Sell-Out 1P) |
| **GEL-004** | Geladeira Multidoor 590L | `SITE_PROPRIO` | 7 | R\$ 6.899,00 | R\$ 5.503,27 | **R\$ 7.089,00** | `+2,75%` | Seguidor Seletivo + Proteção de Ruptura |
| **GEL-008** | Geladeira Cycle Defrost 260L | `MKT_3P` | 35 | R\$ 1.999,00 | R\$ 1.906,25 | **R\$ 2.049,90** | `+2,55%` | Cobertura Buy Box (Ignorou concorrente sem estoque) |
| **GEL-010** | Frigobar Retrô 76L | `SITE_PROPRIO` | 115 | R\$ 1.699,00 | R\$ 1.339,87 | **R\$ 1.631,04** | `-4,00%` | Liquidação de Estoque Encalhado (>90 dias) |

---

## 🛠️ Como Executar

1. Abra o notebook diretamente no **Google Colab** ou clone este repositório:
   ```bash
   git clone https://github.com
   ```
2. Instale as dependências:
   ```bash
   pip install pandas numpy matplotlib
   ```
3. Execute o script principal para visualizar a tabela de decisões e o gráfico comparativo.
Use o código com cuidado.

