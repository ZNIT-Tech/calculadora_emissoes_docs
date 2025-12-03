
# 📊 Calculadora de Emissões API

API REST para cálculo de emissões de gases de efeito estufa (GEE) seguindo o padrão GHG Protocol.

## 🚀 Características

✅ **Cálculo dos 3 Escopos** - Diretas e indiretas  
✅ **Fatores de Emissão Atualizados** - Base IPCC  
✅ **Docker Container** - Fácil deploy  
✅ **Documentação Interativa** - Com Docsify  
✅ **Testes Automatizados** - Pytest  

## 📁 Estrutura do Projeto

```
calculadora_emissoes/
├── docker-compose.yml    # Configuração Docker
├── Dockerfile           # Build da imagem
├── requirements.txt    # Dependências Python
├── src/
│   ├── app/
│   │   ├── main.py     # Aplicação FastAPI/Flask
│   │   ├── core/       # Configurações principais
│   │   ├── escopos/    # Módulos de cálculo
│   │   └── utils/      # Funções auxiliares
│   └── test/           # Testes automatizados
└── docs/               # Documentação (esta)
```

## 🔧 Começando Rápido

### Com Docker (Recomendado):
```bash
docker-compose up -d
```

### Localmente:
```bash
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
# ou .venv\Scripts\activate  # Windows
pip install -r requirements.txt
cd src/app
python main.py
```

A API estará disponível em `http://localhost:8000`

## 📚 Documentação da API

Explore os endpoints disponíveis:

- [📖 Introdução](introducao.md)
- [⚡ Quick Start](quick-start.md)
- [🔌 Endpoints da API](api-endpoints.md)
- [🧪 Testes](testes.md)
- [🐳 Docker & Deploy](docker-deploy.md)
```

**`docs/_sidebar.md`:**
```markdown
- [🏠 Início](/)
- [📖 Introdução](introducao.md)
- [⚡ Quick Start](quick-start.md)

## 🔌 API Reference
- [📡 Endpoints](api-endpoints.md)
  - [Escopo 1](endpoints/escopo1.md)
  - [Escopo 2](endpoints/escopo2.md)
  - [Escopo 3](endpoints/escopo3.md)
- [🔑 Autenticação](autenticacao.md)
- [📊 Modelos de Dados](modelos-dados.md)

## 🛠️ Desenvolvimento
- [🧪 Testes](testes.md)
- [🐳 Docker & Deploy](docker-deploy.md)
- [📦 Estrutura do Código](estrutura-codigo.md)

## ❓ Ajuda
- [❓ FAQ](faq.md)
- [🐛 Reportar Bug](reportar-bug.md)
- [💡 Exemplos](exemplos.md)
```
