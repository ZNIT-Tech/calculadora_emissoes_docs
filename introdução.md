# Introdução

## O que é esta API?

A Calculadora de Emissões é uma API REST desenvolvida em Python para cálculo de emissões de gases de efeito estufa (GEE) conforme o **GHG Protocol**.

## Escopos de Emissão

### 🔥 Escopo 1 - Emissões Diretas
- Combustão estacionária (caldeiras, fornos)
- Combustão móvel (veículos da empresa)
- Processos industriais
- Vazamentos (refrigerantes, etc.)

### ⚡ Escopo 2 - Emissões Indiretas por Energia
- Consumo de eletricidade
- Consumo de vapor
- Consumo de calor

### 🔄 Escopo 3 - Outras Emissões Indiretas
- Matérias-primas
- Transporte de insumos
- Viagens de negócios
- Resíduos

## Tecnologias Utilizadas

- **Python** 3.11+
- **FastAPI** (ou Flask) - Framework web
- **Pydantic** - Validação de dados
- **SQLAlchemy** - ORM (se usar banco)
- **Docker** - Containerização
- **Pytest** - Testes