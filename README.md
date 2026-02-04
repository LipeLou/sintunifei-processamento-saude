# 📊 Sistema de Processamento de Planilhas de Plano de Saúde

[![Python](https://img.shields.io/badge/Python-3.8%2B-3776AB?logo=python&logoColor=white)](https://www.python.org/)
[![Pandas](https://img.shields.io/badge/Pandas-1.3%2B-150458?logo=pandas&logoColor=white)](https://pandas.pydata.org/)
[![Licença: MIT](https://img.shields.io/badge/Licen%C3%A7a-MIT-green.svg)](LICENSE)

Sistema profissional para processamento mensal de planilhas de mensalidades e consultas de plano de saúde, com fluxo consistente e validações de integridade, desenvolvido para o SINTUNIFEI.

## 🎯 Objetivo

Automatizar a atualização mensal das planilhas com:
- Atualização de valores de mensalidades
- Consolidação de consultas/coparticipação
- Validações de integridade e consistência
- Relatórios de discrepâncias e conferência

## ✨ Principais funcionalidades

- Geração de planilha mensal atualizada (`dados_atualizados.xlsx`)
- Consolidação de consultas em planilha final (`dados_com_consultas.xlsx`)
- Validação de nulos, duplicatas e divergências
- Comparação de entradas/saídas entre meses

## 📁 Estrutura do projeto

```
.
├── 01_Processamento/                # Notebooks principais de processamento
│   ├── 1_Gerar_Nova_Planilha.ipynb   # Mensalidades
│   └── 2_Processar_Consultas.ipynb   # Consultas/Coparticipação
├── 02_Validacao/                     # Notebooks de validação e conferência
│   ├── 1_Validacao_Pos_Geracao.ipynb # Validação de mensalidades
│   ├── 1.1_Validacao_Entra_Sai.ipynb # Entradas/saídas entre meses
│   └── 2_Validacao_Consultas.ipynb   # Validação de consultas
├── Documentacao/                     # Documentação completa
│   ├── Guia_de_Uso.md
│   └── Indice.md
├── requirements.txt
└── README.md
```

## ✅ Requisitos

- Python 3.8+
- Jupyter Notebook/Lab
- Dependências em `requirements.txt`

## 🔧 Instalação

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## 🚀 Fluxo de trabalho mensal (rápido)

1. **Gerar mensalidades**  
   Execute `01_Processamento/1_Gerar_Nova_Planilha.ipynb`  
   Resultado: `dados_atualizados.xlsx`

2. **Validar mensalidades**  
   Execute `02_Validacao/1_Validacao_Pos_Geracao.ipynb`

3. **Processar consultas**  
   Execute `01_Processamento/2_Processar_Consultas.ipynb`  
   Resultado: `dados_com_consultas.xlsx`

4. **Validar consultas**  
   Execute `02_Validacao/2_Validacao_Consultas.ipynb`

5. **Comparar entrada/saida**  
   Execute `02_Validacao/1.1_Validacao_Entra_Sai.ipynb`

## 📖 Documentação

- **Guia de uso:** `Documentacao/Guia_de_Uso.md`
- **Índice:** `Documentacao/Indice.md`

## 🔒 Dados sensíveis

As planilhas contêm dados pessoais. Mantenha backups e evite publicar arquivos de dados no repositório.

## 🤝 Contribuição

Contribuições são bem-vindas. Abra uma issue descrevendo a proposta antes de enviar PR.

## 📄 Licença

Este projeto está licenciado sob a licença MIT. Veja `LICENSE`.

---

**Desenvolvido para o SINTUNIFEI** | Sistema de Processamento de Planilhas de Plano de Saúde

