# 📘 Guia de Processamento de Mensalidades e Consultas

Este documento serve como guia oficial para a utilização dos notebooks de processamento de dados do plano de saúde. O fluxo foi otimizado para garantir integridade dos dados, separando a etapa de geração da etapa de validação.

---

## 📂 Estrutura do Projeto

O projeto está organizado nas seguintes pastas para facilitar a manutenção e execução:

- **`01_Processamento/`**: Contém o código principal para gerar a planilha atualizada.
- **`02_Validacao/`**: Contém scripts para conferência de dados, busca de erros e discrepâncias.
- **`03_Outros/`**: Arquivos legados ou auxiliares que não fazem parte do fluxo principal mensal.
- **`Documentacao/`**: Manuais e guias (este arquivo).

---

## 🚀 Passo a Passo da Execução Mensal

Siga a ordem abaixo para garantir o processamento correto.


### Passo 1: Gerar a Nova Planilha (Mensalidades)
**Arquivo:** `01_Processamento/1_Gerar_Nova_Planilha.ipynb`

1. **Prepare os Arquivos:** Certifique-se de ter em mãos:
   - A planilha base do mês (ex: `AGO 2025`) com a aba correspondente
   - O relatório de mensalidades da operadora (formato `.xls` ou `.xlsx`)

2. **Configure os Caminhos:** 
   - Abra o notebook e localize a célula **1.1 Carregamento dos Dados**
   - Substitua as strings vazias `''` pelos caminhos completos dos arquivos:
     ```python
     # Use '../' para referenciar arquivos na pasta raiz
     dados = pd.read_excel('../dados.xlsx', skiprows=1, sheet_name='AGO 2025')
     mensalidades = pd.read_excel('../mensalidades.xlsx', index_col=0)
     ```

3. **Ajuste o Nome da Aba (se necessário):**
   - Se o mês mudou, atualize o parâmetro `sheet_name` (ex: `'SET 2025'`, `'OUT 2025'`)

4. **Execute:** Rode todas as células sequencialmente.

5. **Resultado:** Um arquivo chamado `dados_atualizados.xlsx` será gerado na pasta raiz.

---

### Passo 2: Processar Consultas
**Arquivo:** `01_Processamento/2_Processar_Consultas.ipynb`

1. **Objetivo:** Adicionar os valores de coparticipação/consultas à planilha gerada no passo anterior.

2. **Pré-requisito:** Ter o arquivo `dados_atualizados.xlsx` na pasta raiz (resultado do Passo 1).

3. **Configure:**
   - Na célula **2.1**, insira o caminho do relatório de Slip Fatura (Consultas).
   ```python
   caminho_consultas = '../Relatorio_Slip_Fatura.xls'
   ```

4. **Execute:** O script extrairá os nomes e valores do relatório complexo e atualizará a coluna `CONSULTA`.

5. **Resultado:** Um novo arquivo `dados_com_consultas.xlsx` será gerado na pasta raiz.

---

### Passo 3: Validar os Dados Gerados
**Arquivos:** 
- `02_Validacao/1_Validacao_Pos_Geracao.ipynb` (Foco em Mensalidades)
- `02_Validacao/3_Validacao_Consultas.ipynb` (Foco em Consultas)

1. **Validação de Mensalidades:**
   - Execute `1_Validacao_Pos_Geracao.ipynb` lendo o arquivo `dados_atualizados.xlsx` ou `dados_com_consultas.xlsx`.
   - Verifica nulos e duplicatas de mensalidades.

2. **Validação de Consultas:**
   - Execute `3_Validacao_Consultas.ipynb`.
   - Lê automaticamente `dados_com_consultas.xlsx`.
   - **Crítico:** Verifica se há nomes duplicados com valor de consulta (risco de cobrança dupla).
   - Compara totais e lista os maiores valores.

---

### Passo 4: Verificação Cruzada (Opcional/Específico)
**Arquivo:** `02_Validacao/1_Validacao_Pos_Geracao.ipynb`

1. **Objetivo:** Verificar se o arquivo gerado no Passo 1 está consistente e completo.

2. **Configuração:**
   - Na célula **2.1**, configure o caminho da planilha de mensalidades original (mesmo do Passo 1)
   - O notebook carrega automaticamente o `dados_atualizados.xlsx` gerado anteriormente

3. **Execução:** Rode todas as células sequencialmente logo após gerar a planilha.

4. **Validações Realizadas:**
   
   **2.2 - Mensalidades Nulas:**
   - Conta quantas pessoas têm nome mas não têm valor de mensalidade
   - Lista os nomes afetados
   - **Ação:** Se houver muitos casos, verifique se os nomes estão escritos exatamente igual nas duas planilhas
   
   **2.3 - Nomes Não Encontrados (Mensalidades → Dados):**
   - Identifica pessoas que estão sendo cobradas pela operadora mas não estão na nossa base
   - **Ação:** Pode indicar novos beneficiários que precisam ser cadastrados
   
   **2.4 - Nomes Sobrando (Dados → Mensalidades):**
   - Identifica pessoas na nossa base que não aparecem na cobrança
   - **Ação:** Pode indicar pessoas que saíram do plano ou erros de digitação
   
   **2.5 - Verificação de Duplicatas:**
   - Verifica nomes duplicados na planilha de dados
   - Verifica nomes duplicados na planilha de mensalidades (usando data de nascimento como filtro)
   - **Ação:** Se houver duplicatas, investigue se são pessoas diferentes com mesmo nome ou cadastros duplicados

### Validação Extra: Comparação de Entrada/Saída
**Arquivo:** `02_Validacao/1.1_Validacao_Entra_Sai.ipynb`

Este notebook verifica a evolução da planilha comparando o mês atual com o mês anterior.

1. **Configuração:**
   - Na célula de "Carregamento", defina o `caminho_mes_anterior`.
   - Pode ser um arquivo `dados_atualizados.xlsx` do mês passado ou a planilha DIRF antiga.

2. **O que ele faz:**
   - Identifica **Novos Entrantes:** Pessoas que não existiam ou não tinham mensalidade e agora têm.
   - Identifica **Saídas:** Pessoas que tinham mensalidade e agora não têm (ou sumiram da planilha).
   - Gera relatórios Excel automáticos: `relatorio_entradas.xlsx` e `relatorio_saidas.xlsx` na pasta raiz.

---

### Passo 3: Verificação Cruzada (Opcional/Específico)
**Arquivo:** `02_Validacao/2_Verificacao_Cruzada_Operadora.ipynb` (antigo `dirf2`)

1. **Objetivo:** Comparar a base de dados preparada para DIRF com relatórios específicos de repasse da operadora.

2. **Quando usar:** 
   - Quando precisar validar dados específicos para a DIRF
   - Quando houver necessidade de comparar com relatórios financeiros detalhados
   - Antes de enviar dados para a DIRF

3. **Configuração:** 
   - Na célula **3.1**, atualize os caminhos dos arquivos:
     ```python
     dados = pd.read_excel('caminho/para/DADOS_PARA_A_DIRF.xlsx', skiprows=2, index_col=0, sheet_name="MAR 2025")
     mensalidades = pd.read_excel('caminho/para/Relatorio_Demonstrativo.xls', index_col=0)
     ```
   - Ajuste o `sheet_name` conforme o mês necessário
   - **Atenção:** O código original tinha caminhos do Google Colab (`/content/`), sempre ajuste para caminhos locais

4. **Execução:** Execute todas as células para listar nomes presentes no relatório financeiro que não constam na base DIRF.

5. **Interpretação dos Resultados:**
   - Se aparecerem nomes, significa que há pessoas no relatório da operadora que não estão na nossa planilha DIRF
   - **Ação:** Verifique se essas pessoas precisam ser adicionadas ou se há erro de digitação

---

## ⚠️ Pontos de Atenção

### Configuração de Arquivos

- **Caminhos dos Arquivos:** Como os arquivos mudam mensalmente, sempre verifique se o nome/caminho no código corresponde ao arquivo salvo no computador.
  - Use caminhos absolutos ou relativos ao diretório do notebook
  - Exemplo de caminho relativo: `'../dados/planilha.xlsx'`
  - Exemplo de caminho absoluto: `'C:/Users/Usuario/Documentos/planilha.xlsx'` (Windows) ou `'/home/usuario/dados/planilha.xlsx'` (Linux)

- **Abas do Excel:** Se o nome da aba mudar (ex: de `AGO 2025` para `SET 2025`), ajuste o parâmetro `sheet_name` no Passo 1.

- **Formato dos Arquivos:** 
  - A planilha base geralmente é `.xlsx`
  - O relatório da operadora pode ser `.xls` ou `.xlsx`
  - Se usar `.xls`, pode ser necessário instalar `xlrd`: `pip install xlrd`

### Estrutura Esperada das Planilhas

**Planilha Base:**
- Deve ter uma coluna chamada `NOME` (exatamente assim, maiúsculas)
- Deve ter uma coluna chamada `MENSALIDADE`
- A primeira linha pode ser cabeçalho (por isso `skiprows=1`)

**Planilha de Mensalidades (Operadora):**
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
3. Executar: 01_Processamento/2_Processar_Consultas.ipynb
   → Lê dados_atualizados.xlsx + Slip Fatura
   → Gera dados_com_consultas.xlsx (completo)
   ↓
4. Executar Validações:
   → 02_Validacao/1_Validacao_Pos_Geracao.ipynb (Mensalidades)
   → 02_Validacao/1.1_Validacao_Entra_Sai.ipynb (Comparação Mês Anterior)
   → 02_Validacao/3_Validacao_Consultas.ipynb (Consultas)
   ↓
5. Corrigir problemas identificados (se houver)
   ↓
6. Planilha final pronta para uso
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

### Muitas mensalidades nulas

- Pode indicar que muitos nomes não correspondem
- Execute a validação 2.3 para ver quais nomes da operadora não estão na base
- Pode ser necessário padronizar os nomes (remover espaços extras, normalizar acentos)

### Planilha muito grande e lenta

- O processamento pode demorar se houver muitos registros
- Considere processar em lotes se necessário
- Verifique se não há duplicatas desnecessárias

## 📞 Suporte

Para dúvidas sobre a lógica de validação ou manutenção dos scripts:
- Consulte este guia primeiro
- Verifique os comentários nos próprios notebooks
- Consulte o desenvolvedor responsável se necessário

## 📝 Notas Finais

- **Sempre faça backup** das planilhas originais antes de processar
- **Mantenha os arquivos originais** em `03_Outros/` como referência
- **Documente mudanças** se precisar modificar a lógica dos notebooks
- Os notebooks foram testados e estão funcionais - a lógica original foi preservada integralmente

