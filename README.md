# Conferência de Guias PIS/COFINS

Aplicação para conferir guias PIS/COFINS em PDF contra a planilha `Apuração-Pis-Cofins.xlsx`.

# O que o programa faz

1. Lê todos os PDFs da pasta selecionada, inclusive em subpastas.
2. Extrai de cada guia:
   - Razão social;
   - CNPJ;
   - Código de receita;
   - Período de apuração;
   - Valor da guia.
3. Lê a aba `Base Guias` da planilha `Apuração-Pis-Cofins.xlsx`.
4. Cruza os dados usando o campo `Nome Arquivo`.
5. Compara o período de apuração do PDF com o período da planilha.
6. Compara o valor do PDF com o valor da planilha.
7. Gera o arquivo `resultado_conferencia.xlsx` na pasta onde está a planilha de entrada.

# Regras de resultado | Situação | Status Final |
> PDF localizado, período igual e valor igual | `ok` |
> PDF localizado, mas período diferente | `revisar` — Período de apuração divergente |
> PDF localizado e valor diferente | `revisar` — Valor de guia incorreto |
> Linha da planilha sem PDF correspondente | `revisar` — Apenas planilha |
> PDF sem linha correspondente na planilha | `revisar` — Apenas Guia |

A conferência considera as informações corretas somente quando o PDF é localizado, o período de apuração é igual ao período da planilha e a diferença entre os valores é exatamente `0,00`.

# Planilha de entrada

O arquivo deve conter a aba `Base Guias` e as colunas:

- `Razao Social` ou `Razão Social`;
- `CNPJ`;
- `Valor Guia`;
- `Codigo` ou `Código`;
- `Periodo` ou `Período`;
- `Nome Arquivo`.

# Como o cruzamento é feito

O programa compara o conteúdo da coluna `Nome Arquivo` com o nome dos PDFs.

Exemplo:
PDF: 4 - COFINS - ADM PATRIMONIO - 07.2026.pdf
Nome utilizado no cruzamento: COFINS - ADM PATRIMONIO
O número inicial, o período final, a extensão do arquivo, os acentos e as diferenças entre letras maiúsculas e minúsculas são desconsiderados.

# Normalizações aplicadas

- Razão social: remove acentos, espaços duplicados e diferenças entre maiúsculas/minúsculas;
- CNPJ: comparado somente pelos dígitos;
- Código: utiliza somente a parte antes do hífen e completa com zeros à esquerda até quatro dígitos;
- Período: normalizado internamente para `AAAA-MM`;
- Valores: convertidos e arredondados para duas casas decimais;
- Nome do arquivo: remove o código inicial, o período final e os acentos.

# Conferência do período

O período é extraído do PDF quando aparece em formatos como:
janeiro/2026
janeiro-2026
07/2026
2026/07
Depois, o período do PDF e o período da planilha são convertidos para o padrão interno `AAAA-MM`. Por exemplo:

julho/2026 = 2026-07
07/2026 = 2026-07

Se os períodos forem diferentes, o registro será marcado como `revisar`.

# Arquivo de saída

O programa cria:
resultado_conferencia.xlsx
A planilha de resultado mantém as colunas originais e acrescenta:

- `Valor PDF`;
- `Diferença`;
- `Status Final`;
- `Situação`.

As células da coluna `Status Final` recebem a seguinte formatação:

- `ok`: preenchimento verde;
- `revisar`: preenchimento vermelho.
