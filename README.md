# monitoramente-e-drift

Roteiro 4 — Monitoramento de um detector de phishing em produção
Cibersegurança Aplicada com IA · Insper 2026-2

Dupla: **Antonio Almeida** e **Luka Figueiredo**

## Conteúdo

| Arquivo | Descrição |
|---|---|
| `roteiro4.ipynb` | Notebook com o código, as saídas e o comentário de cada item (a–g) |
| `relatorio_roteiro4.pdf` | Relatório curto (3 páginas) com as respostas de cada item |
| `relatorio_roteiro4.tex` | Fonte LaTeX do relatório |
| `fig_relatorio.png` | Figura usada no relatório |
| `dados/` | Pacote `R5_monitoramento_drift.zip` do roteiro (modelo, referência, produção, ouro e LEIA-ME) |

## Resumo do diagnóstico

O modelo (Random Forest, limiar 0,30) não mudou entre as semanas 9 e 40, mas o mundo em volta dele sim:

1. **Drift de dados (semanas 13–16):** tráfego legítimo novo (domínios mais novos, links divergentes, pedidos de login) virou falso positivo; quarentena em ~19%.
2. **Envenenamento pelo feedback (desde a semana 18):** 8 caixas (0,27%) concentram 36,5% das liberações, e 99,7% do que liberam é phishing — ataque gradual ("sapo na panela").
3. **Quebra de pipeline (semanas 22–25):** `idade_dominio_dias = 0` em 50% dos e-mails; ~4.400 quarentenas a mais. Não é drift.
4. **Drift de conceito (desde a semana 30):** fraude de pagamento com cara de e-mail legítimo; o recall cai de ~0,95 para ~0,5 sem nenhuma feature com PSI alto.

Recomendações: retreino por gatilho com rótulo verificado (S2), sem semanas de pipeline quebrado; **não** ligar o retreino automático com `rotulo_pipeline`; portão de âncora fixa no conjunto de ouro; e o plano de monitoramento com 8 sinais do item g).

## Como reproduzir

```bash
python -m venv .venv
.venv\Scripts\activate          # Windows  (Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace roteiro4.ipynb
```

O modelo foi gravado com o scikit-learn 1.9.1; use essa versão para que o score recalculado bata com a coluna `score`.
