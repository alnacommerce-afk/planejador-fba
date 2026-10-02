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
- A cada atualização: salvar a planilha, sobrescrever a cópia do Drive, fazer commit e **push** para o GitHub (sempre).

## Curva ABC análise do Claude (coluna O) e Curva da Assessoria (coluna N)
- Cabeçalhos em linha 2: L=ASIN, M=sku, N=Curva da Assessoria, O=Curva ABC análise do Claude. Dados a partir da linha 3.
- Critério da coluna O: unidades vendidas nos últimos 30 dias (coluna I) dos SKUs `_FBA`, em ordem decrescente, com percentual acumulado.
  - **A**: item que começa antes de 80% do acumulado. **B**: começa antes de 95%. **C**: o restante e quem vendeu 0.
  - Empate em unidades: desempata pelas unidades de 60 dias da curva da assessoria.
- Base usada: unidades (como a assessoria faz), não R$, porque o campo "Vendas" em R$ do Seller Central não bate com unidades x preço.
- Formatação da planilha (definida pelo usuário): linha 2 com filtro em todas as colunas (A2:O2) e cabeçalhos centralizados. Preservar ao atualizar (editar o arquivo existente, nunca recriar).

## Fluxo de envio de arquivos
- Quando o usuário avisar "vou enviar arquivos novos": criar `arquivos enviados/envioN_AAAA-MM-DD_HH-MM` (N = próximo número) e mandar o caminho completo no chat para ele soltar os arquivos lá.
- Ele avisa "enviei"; aí examinar os arquivos com zoom, confirmar a legibilidade e só então extrair.
- Arquivos anexados no chat chegam reduzidos (imagens são recomprimidas): usar sempre os arquivos colocados direto na pasta.
