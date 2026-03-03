# Base de Conhecimento

## Dados Utilizados

Descreva se usou os arquivos da pasta `data`, por exemplo:

| Arquivo | Formato | Utilização no Agente |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores ou seja, dar continuidade ao atendimentode forma mais eficiente. |
| `perfil_investidor.json` | JSON | Personalizar recomendações sobre as duvidas e necessidade de aprendizados do cliente. |
| `produtos_financeiros.json` | JSON | Conhecer os produtos disponiveis para que eles possam ser ensinados ao cliente.|
| `transacoes.csv` | CSV | Analisar padrão de gastos e investimentos do cliente e usar essa informações de forma didatica. |

> [!TIP]
> **Quer um dataset mais robusto?** Você pode utilizar datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a finanças, desde que sejam adequados ao contexto do desafio.

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

O produto Fundo Imobiliarios (Fii) incluir(expandio) pois e produtos muito utilizado em operações financeiras.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Existe duas possibilidades, injetar os dados diretamente, no pront (crtl+c, crtl+v) ou carregar os arquivos via cóigo, como exemplo abaixo:

```python
import pandas as pd
import json
from pathlib import Path

DATA_DIR = Path("data")

# CSVs
transacoes = pd.read_csv(DATA_DIR / "transacoes.csv")
historico_atendimento = pd.read_csv(DATA_DIR / "historico_atendimento.csv")

# JSONs
with open(DATA_DIR / "perfil_investidor.json", encoding="utf-8") as f:
    perfil_investidor = json.load(f)

with open(DATA_DIR / "produtos_financeiros.json", encoding="utf-8") as f:
    produtos_financeiros = json.load(f)

# Exemplos rápidos de uso
print("Transações:", transacoes.shape)
print("Histórico de atendimento:", historico_atendimento.shape)
print("Perfil investidor (chaves):", list(perfil_investidor.keys()))
print("Qtd produtos financeiros:", len(produtos_financeiros))
```

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Para simplificar, podemos injetar esses dados diretamente no prompt (por exemplo, no system prompt ou em mensagens de contexto), garantindo que o agente tenha o máximo de contexto possível logo na geração da resposta.

```text


DADOS CLIENTES E PERFIL(data/perfil_investidor.json):
{
  "nome": "João Silva",
  "idade": 32,
  "profissao": "Analista de Sistemas",
  "renda_mensal": 5000.00,
  "perfil_investidor": "moderado",
  "objetivo_principal": "Construir reserva de emergência",
  "patrimonio_total": 15000.00,
  "reserva_emergencia_atual": 10000.00,
  "aceita_risco": false,
  "metas": [
    {
      "meta": "Completar reserva de emergência",
      "valor_necessario": 15000.00,
      "prazo": "2026-06"
    },
    {
      "meta": "Entrada do apartamento",
      "valor_necessario": 50000.00,
      "prazo": "2027-12"
    }
  ]
}

TRANSAÇÕES DO CLIENTE(data/transacoes.csv):
data,descricao,categoria,valor,tipo
2025-10-01,Salário,receita,5000.00,entrada
2025-10-02,Aluguel,moradia,1200.00,saida
2025-10-03,Supermercado,alimentacao,450.00,saida
2025-10-05,Netflix,lazer,55.90,saida
2025-10-07,Farmácia,saude,89.00,saida
2025-10-10,Restaurante,alimentacao,120.00,saida
2025-10-12,Uber,transporte,45.00,saida
2025-10-15,Conta de Luz,moradia,180.00,saida
2025-10-20,Academia,saude,99.00,saida
2025-10-25,Combustível,transporte,250.00,saida

HISTPRICO DE ATENDIMENTO(data/historico_atendimento.csv):
data,canal,tema,resumo,resolvido
2025-09-15,chat,CDB,Cliente perguntou sobre rentabilidade e prazos,sim
2025-09-22,telefone,Problema no app,Erro ao visualizar extrato foi corrigido,sim
2025-10-01,chat,Tesouro Selic,Cliente pediu explicação sobre o funcionamento do Tesouro Direto,sim
2025-10-12,chat,Metas financeiras,Cliente acompanhou o progresso da reserva de emergência,sim
2025-10-25,email,Atualização cadastral,Cliente atualizou e-mail e telefone,sim
---

PRODUTOS DISPONIVEIS(data/produtos_financeiros.json):
[
  {
    "nome": "Tesouro Selic",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "100% da Selic",
    "aporte_minimo": 30.00,
    "indicado_para": "Reserva de emergência e iniciantes"
  },
  {
    "nome": "CDB Liquidez Diária",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "102% do CDI",
    "aporte_minimo": 100.00,
    "indicado_para": "Quem busca segurança com rendimento diário"
  },
  {
    "nome": "LCI/LCA",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "95% do CDI",
    "aporte_minimo": 1000.00,
    "indicado_para": "Quem pode esperar 90 dias (isento de IR)"
  },
  {
    "nome": "Fundo Multimercado",
    "categoria": "fundo",
    "risco": "medio",
    "rentabilidade": "CDI + 2%",
    "aporte_minimo": 500.00,
    "indicado_para": "Perfil moderado que busca diversificação"
  },
  {
    "nome": "Fundo de Ações",
    "categoria": "fundo",
    "risco": "alto",
    "rentabilidade": "Variável",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil arrojado com foco no longo prazo"
  },
  {
    "nome": "Fundo Imobiliarios (FII)",
    "categoria": "fundo",
    "risco": "alto",
    "rentabilidade": "DY costuma ficar entre 6% a 10% ao ano",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil moderado ao arrojado com foco no longo prazo e com risco de desvalorização"
  }
]

```
## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

O exemplo abaixo sintetiza as informações mais relevantes dos arquivos originais da base de conhecimento, organizando-as de forma clara e objetiva para serem inseridas no contexto do agente. O objetivo é otimizar o uso de tokens, sem perder os dados essenciais para uma resposta informada e personalizada.

```
Dados do Cliente e Perfil:
- Nome: João Silva
- Perfil: Moderado
- Saldo disponível: R$ 5.000

RESUMO DE GASTO:
- Moradia: R$ 1.300
- Alimentaçao: R$ 590
- Transporte: R$ 295
- Saúde: R$ 189
- Lazer: 155

PRODUTOS DISPONIVEIS:
- Tesouro Selic (Risco Baixo)
- CBD Liquidez Diaria (Risco Baixo)
- LCI/LCA (Rico Baixo)
- Fundo Ações (Risco Alto)
- Fundo Imobiliario (Risco Alto)

Últimas transações:
- 01/11: Supermercado - R$ 450
- 03/11: Streaming - R$ 55
...
```
