# 📘 Guia de Processamento de Mensalidades e Consultas

Este documento serve como guia oficial para a utilização dos notebooks de processamento de dados do plano de saúde. O fluxo foi otimizado para garantir a integridade dos dados, separando a etapa de geração da etapa de validação.

---

## 📂 Estrutura do Projeto

O projeto está organizado nas seguintes pastas para facilitar a manutenção e execução:

- **`01_Processamento/`**: Contém o código principal para gerar a planilha atualizada.
- **`02_Validacao/`**: Contém scripts para conferência de dados, busca de erros e discrepâncias.
- **`Documentacao/`**: Manuais e guias (este arquivo).

---

## 🚀 Passo a Passo da Execução Mensal

Siga a ordem abaixo para garantir o processamento correto.


### Passo 1: Gerar a Nova Planilha (Mensalidades)
**Arquivo:** `01_Processamento/1_Gerar_Nova_Planilha.ipynb`

1. **Prepare os arquivos:** Certifique-se de ter em mãos:
   - A planilha base do mês (ex: `AGO 2025`) com a aba correspondente
   - O relatório de mensalidades da operadora (formato `.xls` ou `.xlsx`)

2. **Configure os caminhos:** 
   - Abra o notebook e localize a célula **1.1 Carregamento dos Dados** em `01_Processamento/1_Gerar_Nova_Planilha.ipynb`.
   - Preencha as variáveis vazias `''` com os caminhos corretos:
     ```python
     caminho_dados = '' # Caminho para a planilha de dados (../Data/dados.xlsx)
     caminho_mensalidades = '' # Caminho para a planilha de mensalidades (../Data/mensalidades.xlsx)
     mes = '' # Mês Atual (Ex: 'JAN 2025')
     ```

3. **Execute:** Rode todas as células sequencialmente.

4. **Resultado:** Um arquivo chamado `dados_atualizados.xlsx` será gerado na pasta raiz.

---

### Passo 2: Validar Mensalidades
**Arquivo:** `02_Validacao/1_Validacao_Pos_Geracao.ipynb`

1. **Validação de Mensalidades:**
   - Execute `1_Validacao_Pos_Geracao.ipynb` lendo o arquivo `dados_atualizados.xlsx`.

2. **Resultado:**
- Validações geradas
- Planilhas geradas:
   - **novos_beneficiarios_familia_nova.xlsx**
   - **novos_beneficiarios_familia_existente.xlsx**

3. **Validações Realizadas:**
   
   **2.2 - Mensalidades Nulas:**
   - Conta quantas pessoas têm nome mas não têm valor de mensalidade
   - Lista os nomes afetados
   
   **2.3 - Nomes Não Encontrados (Mensalidades → Dados):**
   - Identifica pessoas que estão sendo cobradas pela operadora mas não estão na nossa base
   - **Ação:** Pode indicar novos beneficiários que precisam ser cadastrados
   
   **2.4 - Nomes Sobrando (Dados → Mensalidades):**
   - Identifica pessoas na nossa base que não aparecem na cobrança
   - **Ação:** Pode indicar pessoas que saíram do plano ou erros de digitação
   
   **2.5 - Verificação de Duplicatas:**
   - Verifica nomes duplicados na planilha de dados
   - Verifica nomes duplicados na planilha de mensalidades (usando data de nascimento como filtro)
   - **Ação:** Se houver duplicatas, investigue se são pessoas diferentes com o mesmo nome, se há cadastros duplicados ou se é valor retroativo.

- Adicione os novos beneficiários localizados nas planilhas geradas (**novos_beneficiarios_...**.xlsx).

4. **Executar novamente**
   - Após realizar as verificações e adicionar os novos beneficiários aos dados, execute todas as células novamente.

### Passo 3: Processar Consultas
**Arquivo:** `01_Processamento/2_Processar_Consultas.ipynb`

1. **Objetivo:** Adicionar os valores de coparticipação/consultas à planilha gerada no passo anterior.

2. **Pré-requisito:** Já ter finalizado o processo completo das mensalidades.

3. **Configure:**
   - Na célula **2.1**, insira o caminho do relatório de Slip Fatura (Consultas).
   ```python
   caminho_consultas = '../Relatorio_Slip_Fatura.xls'
   ```

4. **Execute:** O script extrairá os nomes e valores do relatório complexo e atualizará a coluna `CONSULTA`.

5. **Resultado:** Um novo arquivo `dados_com_consultas.xlsx` será gerado na pasta raiz.

---

### Passo 4: Validar Consultas
**Arquivo:** `02_Validacao/2_Validacao_Consultas.ipynb`

1. **Validação de Consultas:**
   - Execute `2_Validacao_Consultas.ipynb`.
   - Lê automaticamente `dados_com_consultas.xlsx`.
   - **Crítico:** Verifica se há nomes duplicados com valor de consulta (risco de cobrança dupla).
   - Compara totais e lista os maiores valores.

---

### Passo 5: Comparação de Entrada/Saída
**Arquivo:** `02_Validacao/1.1_Validacao_Entra_Sai.ipynb`

Este notebook verifica a evolução da planilha comparando o mês atual com o mês anterior.

1. **Configuração:**
   - Na célula de "Carregamento", defina o `caminho_mes_anterior`.
   - Pode ser um arquivo `dados_atualizados.xlsx` do mês passado ou a planilha DIRF antiga.

2. **O que ele faz:**
   - Identifica **Novos Entrantes:** Pessoas que não existiam ou não tinham mensalidade e agora têm.
   - Identifica **Saídas:** Pessoas que tinham mensalidade e agora não têm (ou sumiram da planilha).
   - Gera relatórios Excel automáticos: `relatorio_entradas.xlsx` e `relatorio_saidas.xlsx` na pasta raiz.

3. **Para que serve:**
   - Ao final de cada planilha, consta uma tabela com os nomes dos beneficiários que entraram e saíram (de um mês para outro). Este notebook analisa e cria essas tabelas.
   
---

## ⚠️ Pontos de Atenção

### Configuração de Arquivos

- **Caminhos dos arquivos:** Como os arquivos mudam mensalmente, sempre verifique se o nome/caminho no código corresponde ao arquivo salvo no computador.
  - Use caminhos absolutos ou relativos ao diretório do notebook
  - Exemplo de caminho relativo: `'../dados/planilha.xlsx'`
  - Exemplo de caminho absoluto: `'C:/Users/Usuario/Documentos/planilha.xlsx'` (Windows) ou `'/home/usuario/dados/planilha.xlsx'` (Linux)

- **Abas do Excel:** Se o nome da aba mudar (ex: de `AGO 2025` para `SET 2025`), ajuste o parâmetro `sheet_name` no Passo 1.

- **Formato dos arquivos:** 
  - A planilha base geralmente é `.xlsx`
  - O relatório da operadora pode ser `.xls` ou `.xlsx`
  - Se usar `.xls`, pode ser necessário instalar `xlrd`: `pip install xlrd`

### Estrutura Esperada das Planilhas

**Planilha base:**
- Deve ter uma coluna chamada `NOME` (exatamente assim, maiúsculas)
- Deve ter uma coluna chamada `MENSALIDADE`
- A primeira linha pode ser cabeçalho (por isso `skiprows=1`)

**Planilha de mensalidades (Operadora):**
- Deve ter uma coluna chamada `Nome` (com N maiúsculo)
- Deve ter uma coluna chamada `Valor`
- Pode ter uma coluna `Data Nasc.` (usada nas validações)

### Erros Comuns e Soluções

| Erro | Causa Provável | Solução |
|------|----------------|---------|
| `FileNotFoundError` | Arquivo não encontrado | Verifique o caminho, nome do arquivo e extensão (`.xls` vs `.xlsx`) |
| `KeyError: 'Nome'` | Coluna não encontrada | Verifique se a coluna se chama exatamente `Nome` (ou `NOME` na planilha base) |
| `KeyError: 'MENSALIDADE'` | Coluna não encontrada | Verifique se a coluna existe na planilha base |
| `EmptyDataError` | Planilha vazia ou formato incorreto | Verifique se o arquivo não está corrompido |
| Valores não atualizados | Nomes não correspondem exatamente | Verifique espaços extras, acentos ou diferenças de maiúsculas/minúsculas |

---

## 📊 Exemplo de Fluxo Completo

```
1. Receber planilhas da operadora (mensalidades + consultas)
   ↓
2. Executar: 01_Processamento/1_Gerar_Nova_Planilha.ipynb
   → Lê planilhas originais
   → Gera dados_atualizados.xlsx (apenas mensalidades)
   ↓
3. Executar: 02_Validacao/1_Validacao_Pos_Geracao.ipynb
   → Valida mensalidades e identifica inconsistências
   ↓
4. Executar: 01_Processamento/2_Processar_Consultas.ipynb
   → Lê dados_atualizados.xlsx + Slip Fatura
   → Gera dados_com_consultas.xlsx (completo)
   ↓
5. Executar: 02_Validacao/2_Validacao_Consultas.ipynb
   → Valida consultas e valores duplicados
   ↓
6. Executar: 02_Validacao/1.1_Validacao_Entra_Sai.ipynb
   → Compara entradas e saídas entre meses
   ↓
7. Corrigir problemas identificados (se houver)
   ↓
8. Planilha final pronta para uso
```

## 🔍 Dicas de Troubleshooting

### Nomes não estão sendo atualizados

1. **Verifique correspondência exata:** Os nomes devem ser idênticos (incluindo espaços e acentos)
   ```python
   # Exemplo de problema:
   # Planilha base: "JOSÉ DA SILVA"
   # Mensalidades: "JOSE DA SILVA"  # Sem acento - não vai corresponder!
   ```

2. **Use a validação:** O Passo 2 mostrará quais nomes não foram encontrados

3. **Verifique manualmente:** Compare algumas linhas diretamente nas planilhas