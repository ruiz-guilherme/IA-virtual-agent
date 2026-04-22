# Base de Conhecimento

## Dados Utilizados

| Arquivo                     | Formato | Onde é utilizado no Bot?                                   |
| --------------------------- | ------- | ---------------------------------------------------------- |
| `historico_atendimento.csv` | CSV     | Contextualizar interações anteriores                       |
| `perfil_investidor.json`    | JSON    | Personalizar as explicações baseado no contexto do usuário |
| `produtos_financeiros.json` | JSON    | Explicar os produtos adequados ao perfil do usuário        |
| `transacoes.csv`            | CSV     | Analisar o padrão de gastos do cliente                     |

> [!TIP]
> **Quer um dataset mais robusto?** Você pode utilizar datasets públicos do [Hugging Face](https://huggingface.co/datasets) relacionados a finanças, desde que sejam adequados ao contexto do desafio.

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

Removi a opção de Fundo Multimercado e adicionei o Fundo Imobiliário, pois é um método mais conhecido e utilizado no mundo de investimentos.

---

## Estratégia de Integração

### Como os dados são carregados?

> Descreva como seu agente acessa a base de conhecimento.

Injetando os dados diretamente no prompt (Copiar e Colar) ou carregar os arquivos via código.

Exemplo:

```python
import pandas as pd
import json

#CSVs
historico = pd.read_csv('data/historico_atendimento.csv')
transacoes = pd.read_CSV('data/transacoes.csv')

#JSONs
with open('data/perfil_investidor.json', 'r', encoding='utf-8') as f:
    perfil = json.load(f)

with open ('data/produtos_financeiros.json', 'r', encoding='utf-8') as f:
    produtos = json.load(f)
```

### Como os dados são usados no prompt?

> Os dados vão no system prompt? São consultados dinamicamente?

```text
DADOS DO CLIENTE E PERFIL (data/perfil_investidor.json):
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

TRANSACOES DO CLIENTE (data/transacoes.csv):
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

HISTÓRICO DE ATENDIMENTO (data/historico_atendimento.csv):
data,canal,tema,resumo,resolvido
2025-09-15,chat,CDB,Cliente perguntou sobre rentabilidade e prazos,sim
2025-09-22,telefone,Problema no app,Erro ao visualizar extrato foi corrigido,sim
2025-10-01,chat,Tesouro Selic,Cliente pediu explicação sobre o funcionamento do Tesouro Direto,sim
2025-10-12,chat,Metas financeiras,Cliente acompanhou o progresso da reserva de emergência,sim
2025-10-25,email,Atualização cadastral,Cliente atualizou e-mail e telefone,sim

PRODUTOS DISPONIVEIS (data/produtos_financeiros.json):
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
    "nome": "Fundo Imobiliario",
    "categoria": "fundo",
    "risco": "medio",
    "rentabilidade": "6% a 12% ano",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil moderado que busca diversificação e Renda Recorrente"
  },
  {
    "nome": "Fundo de Ações",
    "categoria": "fundo",
    "risco": "alto",
    "rentabilidade": "Variável",
    "aporte_minimo": 100.00,
    "indicado_para": "Perfil arrojado com foco no longo prazo"
  }
]
```

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

```
DADOS DO CLIENTE:
- Nome: João Silva
- Perfil: Moderado
- Reserva Atual: R$ 10.000 (Meta: R$ 15.000)
- Objetivo: Construir reserva de Emergência

RESUMO DE GASTOS:
- Moradia: R$ 1.380
- Alimentação: R$ 650
- Transporte: R$ 280
- Saúde: R$ 130
- Lazer: R$ 250

PRODUTOS DISPONÍVEIS PARA EXPLICAR:
- Tesouro Selic (risco baixo)
- CDB Liquidez Diária (risco baixo)
- LCI/LCa (risco baixo)
- Fundo Imobiliário (risco médio)
- Fundo de ações (risco alto)

```
