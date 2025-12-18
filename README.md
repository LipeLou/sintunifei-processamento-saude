# 📊 Sistema de Processamento de Planilhas de Plano de Saúde

Sistema organizado e documentado para processamento mensal de planilhas de mensalidades e consultas do plano de saúde, desenvolvido para o SINTUNIFEI.

## 🎯 Objetivo

Este projeto automatiza a atualização mensal das planilhas de mensalidades do plano de saúde, realizando:
- Atualização automática de valores de mensalidades
- Validação de integridade dos dados
- Identificação de discrepâncias entre planilhas
- Verificação cruzada com relatórios da operadora

## 📁 Estrutura do Projeto

```
criaçao-planilha/
├── 01_Processamento/          # Scripts principais de processamento
│   ├── 1_Gerar_Nova_Planilha.ipynb  # Mensalidades
│   └── 2_Processar_Consultas.ipynb  # Consultas/Coparticipação
├── 02_Validacao/               # Scripts de validação e verificação
│   ├── 1_Validacao_Pos_Geracao.ipynb   # Validação Mensalidades
│   ├── 2_Verificacao_Cruzada_Operadora.ipynb # DIRF vs Operadora
│   └── 3_Validacao_Consultas.ipynb     # Validação Consultas
├── 03_Outros/                  # Arquivos legados e auxiliares
│   ├── consultas.ipynb (original)
│   └── ... (outros notebooks)
├── Documentacao/               # Documentação completa
│   └── Guia_de_Uso.md
└── README.md                   # Este arquivo
```

## 🚀 Início Rápido

### Fluxo de Trabalho Mensal

1. **Mensalidades** (`01_Processamento/1_Gerar_Nova_Planilha.ipynb`)
   - Gera `dados_atualizados.xlsx` com mensalidades atualizadas.

2. **Consultas** (`01_Processamento/2_Processar_Consultas.ipynb`)
   - Lê o arquivo anterior e adiciona as consultas.
   - Gera `dados_com_consultas.xlsx` (Final).

3. **Validar Dados** (`02_Validacao/`)
   - Execute `1_Validacao_Pos_Geracao.ipynb` e `3_Validacao_Consultas.ipynb`.
   - Garante que não há duplicatas ou erros.

## 📖 Documentação Completa

Para instruções detalhadas, consulte o **[Guia de Uso](Documentacao/Guia_de_Uso.md)**.

## ⚠️ Importante

- **Sempre verifique os caminhos dos arquivos** antes de executar os notebooks
- **Mantenha backups** das planilhas originais antes do processamento
- **Execute as validações** após cada geração de planilha
- Os arquivos originais estão preservados em `03_Outros/` para referência

## 🔧 Manutenção

Os notebooks foram organizados de forma modular:
- **Processamento**: Lógica principal de atualização (não altere a menos que necessário)
- **Validação**: Verificações que podem ser expandidas conforme necessário
- **Outros**: Arquivos históricos e scripts auxiliares

## 📝 Notas

- Os notebooks foram testados e estão funcionais
- A lógica original foi preservada integralmente
- A organização facilita a manutenção e apresentação do projeto

---

**Desenvolvido para SINTUNIFEI** | Sistema de Processamento de Planilhas de Plano de Saúde

