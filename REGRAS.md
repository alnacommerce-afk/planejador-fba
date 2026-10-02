# Planejador de FBA: regras de leitura e reposição

Planilha: `PLANEJADOR DE FBA.xlsx` (aba Planilha1). Os 3 arquivos de entrada ficam em `arquivos enviados/envioN_AAAA-MM-DD_HH-MM/`.

## Filtro principal
- Só entram na análise produtos cujo **SKU termina com `_FBA`**. São os que a empresa repõe no FBA.
- SKUs sem `_FBA` no final (ex.: `TOA_COLORE_1`, ofertas FBM/logística do vendedor) são ignorados.

## Arquivos de entrada
| Arquivo | Alimenta |
|---|---|
| Inventário do programa FBA - Logística da Amazon | A (ASIN), B a G (idade do inventário), H (disponível) |
| Gerenciar todo o inventário | I (vendas 30 dias), L (ASIN), M (SKU) |
| Relatório de curva ABC da assessoria (por SKU) | N (curva); J busca a curva pelo ASIN via L:N |

- Vendas 30 dias em "--" significa **0 vendas** (confirmado pelo usuário).
- A curva ABC vem por SKU, então o SKU (coluna M) é a ponte até o ASIN.
- **Lembrete mensal:** no início de cada mês, cobrar do usuário a curva ABC do mês anterior.

## Regra de reposição (enviar o ASIN ao FBA)
Todas as condições juntas:
1. Estoque antigo zerado: colunas **C a G** (61+ dias) com 0. A coluna B (0-60) NÃO conta como antigo.
2. Disponível (H) **+ a caminho** (em preparação, enviado, em recebimento) menor que 5.
3. Vendas em 30 dias (I) maiores que o disponível.

- Estoque "em preparação" conta como já programado: não repor de novo, mas **avisar sempre que há produtos a caminho do FBA**.
- Quantidade: sempre múltiplo de 5, arredondando para cima: vendas 30d arredondadas ao próximo múltiplo de 5, menos o disponível.
- Em toda resposta, informar **ASIN e SKU juntos**.
- Curva: usar a da assessoria (por SKU). Colunas L (ASIN), M (SKU) e N (curva) listam todos os `_FBA`; N é atualizada quando a assessoria manda a curva nova.
- Fórmula da coluna J deve apontar para a própria linha: `=IFERROR(VLOOKUP(A{n},L:N,3,0),"")`.

## Onde fica a planilha
- Versão principal: `arquivos enviados/PLANEJADOR DE FBA.xlsx` (neste repositório).
- A cópia em `G:\Meu Drive\DRIVE - COMPUTADOR\PLANEJADOR DE FBA.xlsx` deve ser **sempre sobrescrita** com a principal a cada atualização.
- Se o Excel estiver com o arquivo aberto (existe `~$PLANEJADOR DE FBA.xlsx`), pedir para fechar antes de salvar.
