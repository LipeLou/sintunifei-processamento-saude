# 📋 Especificação Técnica do Sistema

Este documento detalha a estrutura técnica, formatos de dados e lógica de processamento dos notebooks.

## 🔧 Requisitos Técnicos

### Dependências Python

```python
pandas >= 1.3.0
openpyxl >= 3.0.0  # Para arquivos .xlsx
xlrd >= 2.0.0      # Para arquivos .xls (legado)
```

### Instalação

```bash
pip install pandas openpyxl xlrd
```

## 📊 Estrutura de Dados

### Planilha Base (Principal)

**Formato:** Excel (`.xlsx`)

**Estrutura esperada:**
- **Aba:** Nome do mês (ex: `AGO 2025`, `SET 2025`)
- **Cabeçalho:** Primeira linha (por isso `skiprows=1`)
- **Colunas obrigatórias:**
  - `NOME`: Nome completo do beneficiário (string)
  - `MENSALIDADE`: Valor da mensalidade (float, será atualizado)

**Características:**
- As últimas 35 linhas são removidas automaticamente (rodapés/índices)
- Pode conter linhas com `NOME` vazio (NaN) - essas são ignoradas nas validações

### Planilha de Mensalidades (Operadora)

**Formato:** Excel (`.xls` ou `.xlsx`)

**Estrutura esperada:**
- **Colunas obrigatórias:**
  - `Nome`: Nome completo do beneficiário (string) - **deve corresponder exatamente ao `NOME` da planilha base**
  - `Valor`: Valor da mensalidade (float)
- **Colunas opcionais:**
  - `Data Nasc.`: Data de nascimento (usada para filtrar duplicatas nas validações)

**Características:**
- Usa `index_col=0` na leitura (primeira coluna como índice)
- Pode conter linhas com `Nome` vazio (NaN) - essas são ignoradas

### Arquivo Gerado (`dados_atualizados.xlsx`)

**Formato:** Excel (`.xlsx`)

**Estrutura:**
- Mesma estrutura da planilha base
- Coluna `MENSALIDADE` atualizada com valores da planilha de mensalidades
- Sem índice ao exportar (`index=False`)

## 🔄 Lógica de Processamento

### Notebook 1: Geração da Planilha

**Algoritmo:**

```
1. Carregar planilha base
   - Ler Excel com skiprows=1
   - Selecionar aba específica (ex: 'AGO 2025')
   - Remover últimas 35 linhas

2. Carregar planilha de mensalidades
   - Ler Excel com index_col=0

3. Loop de atualização:
   Para cada linha em mensalidades:
     nome = mensalidades['Nome']
     valor = mensalidades['Valor']
     
     Encontrar linha em dados onde dados['NOME'] == nome
     Atualizar dados['MENSALIDADE'] = valor

4. Exportar dados atualizados
   - Salvar como 'dados_atualizados.xlsx'
   - Sem índice
```

**Complexidade:** O(n × m) onde n = linhas em mensalidades, m = linhas em dados

**Observações:**
- A correspondência de nomes é **case-sensitive** e **exata**
- Se um nome não for encontrado, a mensalidade permanece inalterada (ou nula)
- Múltiplas correspondências atualizam todas as ocorrências

### Notebook 2: Validação Pós-Geração

**Validações realizadas:**

1. **Mensalidades Nulas:**
   - Filtra linhas onde `NOME` não é NaN
   - Conta quantas têm `MENSALIDADE` nula
   - Lista os nomes afetados

2. **Nomes Não Encontrados (Mensalidades → Dados):**
   - Compara conjunto de nomes únicos
   - Identifica nomes em mensalidades que não estão em dados
   - Indica possíveis novos beneficiários

3. **Nomes Sobrando (Dados → Mensalidades):**
   - Compara conjunto de nomes únicos
   - Identifica nomes em dados que não estão em mensalidades
   - Indica possíveis saídas do plano

4. **Duplicatas em Dados:**
   - Filtra linhas com `NOME` não nulo
   - Identifica duplicatas usando `duplicated(keep=False)`
   - Mostra todas as ocorrências

5. **Duplicatas em Mensalidades:**
   - Filtra linhas com `Data Nasc.` não nulo
   - Conta ocorrências de cada nome
   - Mostra nomes que aparecem mais de uma vez

### Notebook 3: Verificação Cruzada DIRF

**Algoritmo:**

```
1. Carregar planilha DIRF
   - Ler Excel com skiprows=2, index_col=0
   - Selecionar aba específica (ex: 'MAR 2025')

2. Carregar relatório demonstrativo
   - Ler Excel com index_col=0

3. Comparação:
   Para cada linha em mensalidades:
     nome = mensalidades['Nome']
     Se nome não está em dados['NOME']:
       Imprimir nome
```

**Uso:** Validação específica antes de enviar dados para a DIRF

## ⚙️ Parâmetros Configuráveis

### Notebook 1

| Parâmetro | Localização | Descrição | Exemplo |
|-----------|-------------|-----------|---------|
| `skiprows` | `pd.read_excel()` | Linhas a pular no início | `1` (cabeçalho) |
| `sheet_name` | `pd.read_excel()` | Nome da aba | `'AGO 2025'` |
| `index_col` | `pd.read_excel()` | Coluna como índice | `0` (mensalidades) |
| Linhas removidas | `dados.iloc[:-35]` | Últimas linhas a remover | `35` |

### Notebook 2

| Parâmetro | Localização | Descrição |
|-----------|-------------|-----------|
| Arquivo gerado | `pd.read_excel('dados_atualizados.xlsx')` | Arquivo do Passo 1 |
| Filtro Data Nasc. | `dropna(subset=['Data Nasc.'])` | Usado para duplicatas |

### Notebook 3

| Parâmetro | Localização | Descrição | Exemplo |
|-----------|-------------|-----------|---------|
| `skiprows` | `pd.read_excel()` | Linhas a pular | `2` |
| `sheet_name` | `pd.read_excel()` | Nome da aba DIRF | `'MAR 2025'` |
| `index_col` | `pd.read_excel()` | Coluna como índice | `0` |

## 🐛 Tratamento de Erros

### Erros Esperados e Comportamento

1. **Nome não encontrado:**
   - **Comportamento:** Mensalidade permanece inalterada
   - **Detecção:** Validação 2.3 e 2.4

2. **Nome duplicado:**
   - **Comportamento:** Todas as ocorrências são atualizadas
   - **Detecção:** Validação 2.5

3. **Valor NaN em Nome:**
   - **Comportamento:** Linha ignorada no processamento
   - **Detecção:** Filtros com `dropna(subset=['NOME'])`

4. **Valor NaN em Mensalidade:**
   - **Comportamento:** Mantido como NaN
   - **Detecção:** Validação 2.2

## 📈 Performance

### Tempo Estimado de Processamento

- **Notebook 1:** ~2-5 segundos para 1000 registros
- **Notebook 2:** ~3-7 segundos para 1000 registros
- **Notebook 3:** ~1-3 segundos para 1000 registros

### Otimizações Possíveis

- Usar `pd.merge()` ao invés de loop para grandes volumes
- Usar `set()` para comparações de nomes (mais rápido que listas)
- Processar em chunks para arquivos muito grandes (>10.000 linhas)

## 🔒 Considerações de Segurança

- **Dados Sensíveis:** As planilhas contêm informações pessoais (nomes, valores)
- **Backup:** Sempre mantenha backups das planilhas originais
- **Validação:** Sempre execute as validações antes de usar os dados finais
- **Auditoria:** Os notebooks preservam a lógica original para rastreabilidade

## 📝 Notas de Manutenção

- **Alterações na estrutura:** Se as colunas mudarem de nome, atualize todos os notebooks
- **Novos meses:** Apenas atualize o `sheet_name` - não é necessário modificar código
- **Novas validações:** Adicione novas células no Notebook 2, mantendo a numeração sequencial

---

**Última atualização:** Organização inicial do projeto  
**Versão:** 1.0

