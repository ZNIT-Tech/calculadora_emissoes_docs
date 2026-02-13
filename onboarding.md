# **Onboarding de Empresas – API de Administração (Zona Neutra)**

Permite administrar empresas, unidades de negócio, configurações GHG e chaves de API durante o processo de onboarding.  
Todos os endpoints (exceto `/teste`) exigem permissão `gerenciar:usuarios` e são restritos à **Zona Neutra** (administradores da plataforma).

---

## **Endpoints de Criação e Configuração de Empresas**

### **1. POST /api/onboarding/admin/empresas**

Cria uma nova empresa **completa**, incluindo:
- Registro da empresa (com endereço detalhado)
- Usuário administrador
- Unidade de negócio nível 1 (raiz)
- API Key padrão com permissões pré-definidas

#### **Requisição:**
```json
{
  "nome": "Empresa Exemplo Ltda",
  "cnpj": "12345678000199",
  "cidade": "São Paulo",
  "bairro": "Centro",
  "logradouro": "Av. Paulista",
  "numero_endereco": "1000",
  "complemento": "Andar 10",
  "cep": "01310-100",
  "endereco": "Av. Paulista, 1000 - Andar 10, Centro, São Paulo, SP, 01310-100", // campo legado
  "obs": "Contratação via plano Enterprise",
  "usuario_admin": {
    "nome": "João Silva",
    "email": "joao.silva@exemplo.com",
    "senha": "Senha@123"
  },
  "permissoes_api": [
    "consultar:resultados",
    "calcular:escopo1",
    "calcular:escopo2",
    "calcular:escopo3"
  ]
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `nome` | string | Nome da empresa | "Empresa Exemplo Ltda" |
| `cnpj` | string | CNPJ (apenas números ou formatado) | "12345678000199" |

#### **Parâmetros Opcionais (Endereço):**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `cidade` | string | Cidade da sede |
| `bairro` | string | Bairro |
| `logradouro` | string | Logradouro (rua, avenida, etc.) |
| `numero_endereco` | string | Número |
| `complemento` | string | Complemento |
| `cep` | string | CEP (com ou sem formatação) |
| `endereco` | string | Campo livre legado (será concatenado se os campos detalhados forem fornecidos) |
| `obs` | string | Observações gerais |

#### **Parâmetros Opcionais (Administrador e API):**
| Campo | Tipo | Descrição | Padrão |
|-------|------|-----------|--------|
| `usuario_admin` | objeto | Dados do usuário administrador | Gerado automaticamente se não fornecido |
| `permissoes_api` | array | Lista de permissões para a API Key criada | `["consultar:resultados","calcular:escopo1","calcular:escopo2","calcular:escopo3"]` |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/onboarding/admin/empresas" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-chave-admin" \
  -d '{
    "nome": "Empresa Exemplo Ltda",
    "cnpj": "12345678000199",
    "cidade": "São Paulo",
    "bairro": "Centro",
    "logradouro": "Av. Paulista",
    "numero_endereco": "1000",
    "cep": "01310-100",
    "usuario_admin": {
      "nome": "João Silva",
      "email": "joao.silva@exemplo.com",
      "senha": "Senha@123"
    }
  }'
```

#### **Resposta de Sucesso (201 Created):**
```json
{
  "success": true,
  "message": "Empresa criada com sucesso",
  "empresa": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "nome": "Empresa Exemplo Ltda",
    "cnpj": "12345678000199",
    "obs": "",
    "endereco": "Av. Paulista, 1000, Centro, São Paulo, SP, 01310-100",
    "cidade": "São Paulo",
    "bairro": "Centro",
    "logradouro": "Av. Paulista",
    "numero_endereco": "1000",
    "complemento": null,
    "cep": "01310-100",
    "criada_em": "2025-03-21T14:30:00Z"
  },
  "usuario_admin_id": "123e4567-e89b-12d3-a456-426614174000",
  "unidade_nivel_1_id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "api_key": {
    "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
    "chave": "zk2h3j1k5h6g8f9d0s1a2p3l4q5w6e7r8",  // ⚠️ exibida APENAS UMA VEZ
    "nome": "API Key Padrão",
    "permissoes": [
      "consultar:resultados",
      "calcular:escopo1",
      "calcular:escopo2",
      "calcular:escopo3"
    ],
    "expiracao": null,
    "ativa": true
  }
}
```

---

### **2. POST /api/onboarding/teste**

**Endpoint exclusivo para testes e homologação – sem autenticação.**  
Cria uma empresa de teste com dados mínimos, ideal para ambientes de desenvolvimento.

#### **Requisição (mínima):**
```json
{
  "nome": "Empresa Teste",
  "cnpj": "12345678000199",
  "email": "admin@teste.com"
}
```

#### **Parâmetros Opcionais:**
Todos os campos de endereço e `obs`.

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/onboarding/teste" \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Empresa Teste",
    "cnpj": "12345678000199",
    "cidade": "Rio de Janeiro"
  }'
```

#### **Resposta:**
```json
{
  "success": true,
  "message": "Empresa de teste criada com sucesso",
  "empresa_id": "550e8400-e29b-41d4-a716-446655440000",
  "api_key": "zk2h3j1k5h6g8f9d0s1a2p3l4q5w6e7r8"
}
```

---

### **3. PUT /api/onboarding/admin/empresas/{empresa_id}**

Atualiza dados completos da empresa, incluindo endereço, versão GHG, anos auditados e status.

#### **Requisição (campos parciais):**
```json
{
  "nome": "Novo Nome da Empresa",
  "cidade": "Belo Horizonte",
  "versao_ghg": "v2025.0.1",
  "anos_auditados": ["2023", "2024", "2025"],
  "ativa": true
}
```

#### **Campos Atualizáveis:**
| Campo | Tipo | Validação |
|-------|------|-----------|
| `nome` | string | - |
| `cnpj` | string | Deve ser válido |
| `endereco` | string | Campo legado |
| `cidade`, `bairro`, `logradouro`, `numero_endereco`, `complemento`, `cep` | string | - |
| `obs` | string | - |
| `versao_ghg` | string | Formato: `vYYYY.M.P` |
| `anos_auditados` | array | Anos entre 2000 e 2100 |
| `ativa` | boolean | `true` ou `false` |

#### **Resposta:**
```json
{
  "success": true,
  "message": "Empresa atualizada com sucesso",
  "empresa": {
    "id": "550e8400-e29b-41d4-a716-446655440000",
    "nome": "Novo Nome da Empresa",
    "cnpj": "12345678000199",
    "cidade": "Belo Horizonte",
    "versao_ghg": "v2025.0.1",
    "anos_auditados": ["2023", "2024", "2025"],
    "ativa": true,
    "atualizada_em": "2025-03-21T15:00:00Z"
  }
}
```

---

### **4. PUT /api/onboarding/admin/empresas/{empresa_id}/configuracoes-ghg**

Atualiza especificamente a versão GHG e os anos auditados da empresa.

#### **Requisição:**
```json
{
  "versao_ghg": "v2025.0.1",
  "anos_auditados": [2023, 2024, 2025],
  "observacoes": "Atualização anual de inventário"
}
```

#### **Campos:**
| Campo | Obrigatório | Tipo | Descrição |
|-------|-------------|------|-----------|
| `versao_ghg` | não | string | Versão do Programa GHG |
| `anos_auditados` | não | array | Lista de anos (inteiros ou string) |
| `observacoes` | não | string | Complemento armazenado em `obs` |

#### **Resposta:**
```json
{
  "success": true,
  "message": "Configurações de GHG atualizadas com sucesso",
  "empresa": {
    "id": "...",
    "nome": "...",
    "cnpj": "...",
    "versao_ghg": "v2025.0.1",
    "anos_auditados": ["2023", "2024", "2025"],
    "observacoes": "Atualização anual de inventário",
    "ultima_atualizacao": "2025-03-21T15:10:00Z"
  }
}
```

---

### **5. PUT /api/onboarding/admin/empresas/{empresa_id}/status**

Ativa ou desativa uma empresa. Empresas inativas **não podem** realizar cálculos ou gerar relatórios.

#### **Requisição:**
```json
{
  "ativa": false
}
```

#### **Resposta:**
```json
{
  "success": true,
  "message": "Empresa desativada com sucesso",
  "empresa": {
    "id": "...",
    "nome": "...",
    "cnpj": "...",
    "ativa": false,
    "status_anterior": true,
    "data_atualizacao": "2025-03-21T15:20:00Z"
  }
}
```

---

## **Endpoints de Gestão de Unidades de Negócio**

### **1. POST /api/onboarding/admin/empresas/{empresa_id}/unidades**

Cria uma nova unidade de negócio para a empresa.

#### **Requisição:**
```json
{
  "nome": "Filial São Paulo",
  "nivel": 2,
  "unidade_pai_id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "cidade": "São Paulo",
  "bairro": "Vila Olímpia",
  "logradouro": "Rua das Empresas",
  "numero_endereco": "500",
  "cep": "04551-000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `nome` | string | Nome da unidade |
| `nivel` | número | Nível hierárquico (1–10) |
| `unidade_pai_id` | string | Obrigatório se `nivel > 1` |

#### **Parâmetros Opcionais:**
- Todos os campos de endereço detalhado (mesmos da empresa).
- `dados_adicionais` (objeto): para extensibilidade futura.

#### **Resposta:**
```json
{
  "success": true,
  "message": "Unidade criada com sucesso",
  "unidade": {
    "id": "7c9e6679-7425-40de-944b-e07fc1f90ae7",
    "nome": "Filial São Paulo",
    "nivel": 2,
    "empresa_id": "550e8400-e29b-41d4-a716-446655440000",
    "unidade_pai_id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "cidade": "São Paulo",
    "bairro": "Vila Olímpia",
    "logradouro": "Rua das Empresas",
    "numero_endereco": "500",
    "cep": "04551-000",
    "created_at": "2025-03-21T15:30:00Z"
  }
}
```

---

### **2. POST /api/onboarding/admin/empresas/{empresa_id}/hierarquia**

Cria uma **hierarquia completa** de unidades a partir de uma estrutura aninhada.

#### **Requisição (exemplo):**
```json
{
  "estrutura": [
    {
      "nome": "Matriz",
      "nivel": 1,
      "cidade": "São Paulo",
      "filhas": [
        {
          "nome": "Filial São Paulo",
          "nivel": 2,
          "cidade": "São Paulo",
          "filhas": [
            {
              "nome": "Depósito Zona Sul",
              "nivel": 3,
              "cidade": "São Paulo"
            }
          ]
        },
        {
          "nome": "Filial Rio",
          "nivel": 2,
          "cidade": "Rio de Janeiro"
        }
      ]
    }
  ]
}
```

#### **Validação:**
- Deve haver **uma única unidade raiz** (nível 1).
- Níveis devem ser sequenciais (1,2,3...).
- Unidades do mesmo pai não podem ter nomes duplicados.

#### **Resposta (sucesso):**
```json
{
  "success": true,
  "message": "Hierarquia criada com sucesso",
  "unidades_criadas": [
    {
      "id": "...",
      "nome": "Matriz",
      "nivel": 1
    },
    {
      "id": "...",
      "nome": "Filial São Paulo",
      "nivel": 2,
      "unidade_pai_id": "..."
    }
    // ...
  ]
}
```

#### **Resposta com avisos (207 Multi-Status):**
Retornada quando parte da hierarquia foi criada, mas ocorreram falhas em alguns nós.

---

### **3. GET /api/onboarding/admin/empresas/{empresa_id}/unidades**

Lista todas as unidades da empresa, agrupadas por nível hierárquico.

#### **Resposta:**
```json
{
  "success": true,
  "empresa_id": "550e8400-e29b-41d4-a716-446655440000",
  "unidades_por_nivel": {
    "1": [
      {
        "id": "...",
        "nome": "Matriz",
        "nivel": 1,
        "cidade": "São Paulo"
      }
    ],
    "2": [
      {
        "id": "...",
        "nome": "Filial São Paulo",
        "nivel": 2,
        "unidade_pai_id": "..."
      },
      {
        "id": "...",
        "nome": "Filial Rio",
        "nivel": 2,
        "unidade_pai_id": "..."
      }
    ],
    "3": [...]
  }
}
```

---

## **Endpoints de Consulta e Relatórios**

### **1. GET /api/onboarding/admin/empresas**

Lista empresas com paginação e filtros opcionais.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Descrição |
|-----------|-------------|-----------|
| `pagina` | não (padrão 1) | Número da página |
| `itens_por_pagina` | não (padrão 20) | Itens por página |
| `cnpj` | não | Filtro por CNPJ (exato) |
| `nome` | não | Filtro parcial por nome |
| `status` | não | `ativas` ou `inativas` |

#### **Exemplo:**
```
GET /api/onboarding/admin/empresas?pagina=2&itens_por_pagina=10&status=ativas
```

#### **Resposta:**
```json
{
  "success": true,
  "empresas": [
    {
      "id": "...",
      "nome": "Empresa A",
      "cnpj": "...",
      "cidade": "São Paulo",
      "ativa": true,
      "versao_ghg": "v2025.0.1",
      "created_at": "..."
    }
  ],
  "paginacao": {
    "pagina": 2,
    "itens_por_pagina": 10,
    "total": 45,
    "total_paginas": 5
  }
}
```

---

### **2. GET /api/onboarding/admin/empresas/{empresa_id}**

Retorna os **dados completos** da empresa, incluindo:
- Dados cadastrais
- Lista de usuários
- Lista de unidades (hierarquia completa)
- Lista de API Keys

#### **Resposta (resumida):**
```json
{
  "success": true,
  "empresa": { ... },
  "usuarios": [ ... ],
  "unidades": [ ... ],
  "api_keys": [ ... ],
  "totais": {
    "usuarios": 5,
    "unidades": 12,
    "api_keys": 3
  }
}
```

---

### **3. GET /api/onboarding/admin/empresas/{empresa_id}/status**

Verifica o status atual da empresa e se há pelo menos uma API Key ativa.

#### **Resposta:**
```json
{
  "success": true,
  "status": {
    "empresa_ativa": true,
    "total_api_keys": 3,
    "pode_operar": true,
    "data_verificacao": "2025-03-21T16:00:00Z",
    "ultima_atualizacao": "2025-03-21T15:20:00Z"
  }
}
```

---

### **4. GET /api/onboarding/admin/empresas/{empresa_id}/configuracoes-ghg**

Obtém as configurações GHG específicas da empresa.

#### **Resposta:**
```json
{
  "success": true,
  "configuracoes": {
    "versao_ghg": "v2025.0.1",
    "anos_auditados": ["2023", "2024", "2025"],
    "observacoes": "Atualização anual de inventário",
    "versao_ghg_padrao": "v2025.0.1",
    "ultima_atualizacao": "2025-03-21T15:10:00Z"
  }
}
```

---

### **5. GET /api/onboarding/admin/empresas/relatorio (IN PROGRESS)**

Relatório detalhado com filtros avançados e ordenação.

#### **Parâmetros:**
| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `pagina` | int | Padrão 1 |
| `itens_por_pagina` | int | Padrão 50 |
| `status` | string | `ativas`, `inativas` ou vazio (todos) |
| `versao_ghg` | string | Filtro exato |
| `ano_auditado` | string | Ano (ex: `2024`) |
| `cidade` | string | Filtro parcial |
| `cnpj` | string | Filtro exato |
| `nome` | string | Filtro parcial |
| `ordenar_por` | string | `nome`, `cnpj`, `cidade`, `created_at` (padrão: `nome`) |
| `ordenar_direcao` | string | `asc` ou `desc` (padrão: `asc`) |

#### **Resposta:**
```json
{
  "success": true,
  "empresas": [ ... ],
  "estatisticas": {
    "total_ativas": 42,
    "total_inativas": 3,
    "versoes_ghg": {
      "v2025.0.1": 30,
      "v2024.0.2": 12
    },
    "cidades_mais_frequentes": {
      "São Paulo": 15,
      "Rio de Janeiro": 8
    }
  },
  "paginacao": { ... }
}
```

---

## **Autenticação e Permissões**

Todos os endpoints **exceto `/teste`** exigem:

- **Header:** `X-API-Key: sua-chave-admin`
- **Permissão:** `gerenciar:usuarios`

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/admin/empresas` | POST | `gerenciar:usuarios` |
| `/admin/empresas` | GET | `gerenciar:usuarios` |
| `/admin/empresas/{id}` | GET | `gerenciar:usuarios` |
| `/admin/empresas/{id}` | PUT | `gerenciar:usuarios` |
| `/admin/empresas/{id}/unidades` | POST | `gerenciar:usuarios` |
| `/admin/empresas/{id}/hierarquia` | POST | `gerenciar:usuarios` |
| `/admin/empresas/{id}/unidades` | GET | `gerenciar:usuarios` |
| `/admin/empresas/{id}/configuracoes-ghg` | PUT | `gerenciar:usuarios` |
| `/admin/empresas/{id}/configuracoes-ghg` | GET | `gerenciar:usuarios` |
| `/admin/empresas/{id}/status` | PUT | `gerenciar:usuarios` |
| `/admin/empresas/{id}/status` | GET | `gerenciar:usuarios` |
| `/admin/empresas/relatorio` | GET | `gerenciar:usuarios` |
| `/teste` | POST | **Nenhuma** (apenas para homologação) |

---

## **Regras de Negócio e Validações**

### **CNPJ**
- Deve ser válido segundo algoritmo de dígitos verificadores.
- Aceito com ou sem formatação.
- **Único** no sistema.

### **Níveis de Unidade**
- Variam de **1 (raiz)** a **10**.
- Unidades de nível 1 não possuem `unidade_pai_id`.
- A hierarquia deve formar uma árvore sem ciclos.

### **Versão GHG**
- Formato recomendado: `vAAAA.M.P` (ex: `v2025.0.1`).
- Obtida automaticamente via CNPJ nos cálculos de emissão, mas pode ser sobrescrita manualmente.

### **Anos Auditados**
- Armazenados como string CSV no banco (ex: `2023,2024,2025`).
- A API sempre os expõe como **array** para facilitar o consumo.
- Valores fora do range `2000-2100` são ignorados.

### **API Key Padrão**
- Gerada automaticamente no momento da criação da empresa.
- **Exibida em texto plano apenas uma vez** – deve ser armazenada pelo cliente.
- Permissões podem ser customizadas no campo `permissoes_api`.

### **Empresa Inativa**
- Impede a criação de novos documentos de emissão.
- Impede a geração de relatórios e consultas operacionais (exceto via admin).
- Todas as API Keys da empresa continuam existindo, mas não funcionarão.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Requer `gerenciar:usuarios` |
| `404` | Recurso não encontrado | Verifique IDs de empresa ou unidade |
| `409` | Conflito (ex: CNPJ já cadastrado) | Utilize outro CNPJ |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **CNPJ já cadastrado:**
```json
{
  "success": false,
  "error": "CNPJ já cadastrado no sistema"
}
```

### **Nível de unidade inválido:**
```json
{
  "success": false,
  "error": "Nível deve estar entre 1 e 10"
}
```

### **Unidade pai não informada:**
```json
{
  "success": false,
  "error": "Para níveis > 1, o campo unidade_pai_id é obrigatório"
}
```

### **Empresa não encontrada:**
```json
{
  "success": false,
  "error": "Empresa não encontrada"
}
```

### **Permissão insuficiente:**
```json
{
  "success": false,
  "error": "Permissão 'gerenciar:usuarios' é necessária"
}
```

---

## **Observações Importantes**

1. **Chave de API é exibida UMA ÚNICA VEZ** – armazene-a imediatamente após a criação da empresa.
2. **Campos de endereço** – prefira os campos detalhados (`cidade`, `bairro`, `logradouro`, etc.) ao invés do campo legado `endereco`.
3. **Testes** – utilize o endpoint `/teste` para ambientes de desenvolvimento; ele **não exige autenticação**.
4. **Hierarquia em lote** – o endpoint de hierarquia `POST /hierarquia` realiza operações em transação; falhas parciais retornam `207 Multi-Status`.
5. **Anos auditados** – a API sempre entrega como **array**, mesmo que o banco armazene como string.
6. **Versão GHG padrão** – caso a empresa não possua versão definida, o sistema assume `v2025.0.1` (valor ilustrativo, sujeito a alteração).
7. **Soft Delete** – empresas não são excluídas fisicamente; utilize o status `ativa: false` para desativação.

---
