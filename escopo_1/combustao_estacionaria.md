# **Combustão Estacionária**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes da combustão estacionária (geradores, caldeiras, fornos, etc.).

---

## **Endpoints de Cálculo**

### **1. POST /api/combustao-estacionaria/calcular**

Calcula emissões de GEE para uma fonte estacionária específica **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "combustivel_id": 5,
  "setor_id": 3,
  "consumo": 1000.0,
  "unidade": "litros",
  "data": "2024-06-15",
  "cnpj": "00.000.000/0001-91",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `combustivel_id` | número | ID do combustível | 5 |
| `setor_id` | número | ID do setor | 3 |
| `consumo` | número | Quantidade de combustível consumida | 1000.0 |
| `unidade` | string | Unidade de medida ("litros", "m³", "kg") | "litros" |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-06-15" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | ID da empresa (para metadados) |
| `id_unidade_negocio` | string | ID da unidade de negócio (para metadados) |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/combustao-estacionaria/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "combustivel_id": 5,
    "setor_id": 3,
    "consumo": 1000.0,
    "unidade": "litros",
    "data": "2024-06-15",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_co2": 2.5,
    "emissao_ch4": 0.003,
    "emissao_n2o": 0.001,
    "emissao_total": 2.876,
    "emissao_biogenica": 0.0,
    "metadados": {
      "combustivel": {
        "id": 5,
        "nome": "Óleo Combustível",
        "tipo_fossil": "Sim",
        "tipo_biocombustivel": "Não"
      },
      "setor": {
        "id": 3,
        "nome": "Industrial",
        "descricao": "Setor industrial"
      },
      "consumo": {
        "valor": 1000.0,
        "unidade": "litros"
      },
      "data_consumo": "2024-06-15"
    }
  }
}
```

---

### **2. POST /api/combustao-estacionaria**

Calcula e persiste emissões de GEE para uma fonte estacionária **com persistência no banco**.

#### **Requisição:**
```json
{
  "combustivel_id": 5,
  "setor_id": 3,
  "consumo": 1000.0,
  "unidade": "litros",
  "data": "2024-06-15",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `combustivel_id` | número | ID do combustível | 5 |
| `setor_id` | número | ID do setor | 3 |
| `consumo` | número | Quantidade consumida | 1000.0 |
| `unidade` | string | Unidade de medida | "litros" |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-06-15" |
| `id_empresa` | string | ID da empresa | "dd68b58c-9155-42df..." |
| `id_unidade_negocio` | string | ID da unidade de negócio | "680a4fe4-d2f1-4f2d..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1-10) | 2 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | ID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor |
| `link_referencia` | string | Link para documento de referência |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/combustao-estacionaria" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "combustivel_id": 5,
    "setor_id": 3,
    "consumo": 1000.0,
    "unidade": "litros",
    "data": "2024-06-15",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de combustão estacionária realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_co2": 2.5,
      "emissao_ch4": 0.003,
      "emissao_n2o": 0.001,
      "emissao_total": 2.876,
      "emissao_biogenica": 0.0
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "combustao_estacionaria_id": 456,
      "emissao_id": 789
    },
    "metadados": {
      "empresa": "Nome da Empresa",
      "unidade": "Nome da Unidade",
      "setor": "Setor Industrial",
      "combustivel": "Óleo Combustível",
      "data": "2024-06-15"
    }
  }
}
```

#### **Notas Importantes:**
1. Este endpoint realiza **validação de auditoria** automaticamente
2. É necessário ter permissão `calcular:escopo1`
3. A empresa e unidade devem existir no banco de dados
4. A versão GHG é obtida automaticamente do CNPJ da empresa

---

## **Endpoints de Consulta**

### **1. GET /api/combustao-estacionaria/unidade**

Consulta emissões por unidade de negócio.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_unidade` | ✓ | string | ID da unidade |
| `nivel` | ✓ | número | Nível hierárquico (1-10) |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas (padrão: true) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-estacionaria/unidade?id_unidade=550e8400-e29b-41d4-a716-446655440000&nivel=2" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "nome": "São Paulo",
      "nivel": 2
    },
    "emissao_total": 1500.75,
    "quantidade_documentos": 5,
    "quantidade_unidades": 3,
    "detalhes": [
      {
        "documento_id": "123e4567-e89b-12d3-a456-426614174000",
        "data": "2024-06-15",
        "descricao": "Combustão estacionária - Setor: Industrial, Combustível ID: 5",
        "unidade": {
          "id": "550e8400-e29b-41d4-a716-446655440000",
          "nome": "São Paulo",
          "nivel": 2
        },
        "combustivel": {
          "id": 5,
          "nome": "Óleo Combustível"
        },
        "emissoes": {
          "co2": 2.5,
          "ch4": 0.003,
          "n2o": 0.001,
          "total": 2.876,
          "biogenica": 0.0
        },
        "created_at": "2024-06-15T10:30:00Z"
      }
    ],
    "filtros": {
      "data_inicio": null,
      "data_fim": null,
      "incluir_descendentes": true
    }
  }
}
```

---

### **2. GET /api/combustao-estacionaria/hierarquia-completa**

Consulta hierarquia completa de uma empresa.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | ID da empresa |
| `nivel_inicio` | ✗ | número | Nível inicial (padrão: 1) |
| `ano` | ✗ | string | Ano para filtrar (YYYY) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-estacionaria/hierarquia-completa?id_empresa=123e4567-e89b-12d3-a456-426614174000&nivel_inicio=1&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "nome": "Empresa Exemplo",
      "cnpj": "00.000.000/0000-00"
    },
    "emissao_total_empresa": 3250.5,
    "nivel_inicio": 1,
    "quantidade_unidades_raiz": 3,
    "periodo": {
      "ano": "2024",
      "filtro_aplicado": true
    },
    "hierarquia": [
      {
        "id": "unidade-1-uuid",
        "nivel": 1,
        "nome": "Unidade Matriz",
        "emissao_direta": 1200.0,
        "emissao_total": 3250.5,
        "quantidade_documentos": 10,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 800.0,
            "emissao_total": 1500.0,
            "quantidade_documentos": 5,
            "filhas": []
          }
        ]
      }
    ]
  }
}
```

---

### **3. GET /api/combustao-estacionaria/{documento_id}/detalhes**

Consulta detalhes específicos de um documento de combustão estacionária.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-estacionaria/123e4567-e89b-12d3-a456-426614174000/detalhes" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "documento": {
      "id": "123e4567-e89b-12d3-a456-426614174000",
      "id_empresa": "uuid-empresa",
      "id_unidade_negocio": "uuid-unidade",
      "nvl_unidade_negocio": 2,
      "data": "2024-06-15",
      "descricao": "Combustão estacionária - Setor: Industrial, Combustível ID: 5",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/nota-fiscal",
      "id_categoria": 4,
      "id_usuario": "uuid-usuario",
      "created_at": "2024-06-15T10:30:00Z"
    },
    "combustao_estacionaria": {
      "id": 456,
      "id_combustivel": 5,
      "consumo": 1000.0,
      "id_documento": "123e4567-e89b-12d3-a456-426614174000",
      "created_at": "2024-06-15T10:30:00Z"
    },
    "combustivel": {
      "id": 5,
      "nome": "Óleo Combustível",
      "tipo_fossil": "Sim",
      "tipo_biocombustivel": "Não"
    },
    "emissoes": {
      "id": 789,
      "id_documento": "123e4567-e89b-12d3-a456-426614174000",
      "emissao_co2": 2.5,
      "emissao_ch4": 0.003,
      "emissao_n2o": 0.001,
      "emissao_total": 2.876,
      "emissao_biogenica": 0.0,
      "created_at": "2024-06-15T10:30:00Z"
    }
  }
}
```

---

### **4. GET /api/combustao-estacionaria/combustiveis**

Lista todos os combustíveis disponíveis para combustão estacionária.

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-estacionaria/combustiveis" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 5,
      "nome": "Óleo Combustível",
      "tipo_fossil": "Sim",
      "tipo_biocombustivel": "Não",
      "fatores_emissao": {
        "co2_fossil": 0.0025,
        "ch4_fossil": 0.000003,
        "n2o_fossil": 0.000001,
        "co2_bio": 0.0,
        "ch4_bio": 0.0,
        "n2o_bio": 0.0
      }
    }
  ],
  "total": 15
}
```

---

### **5. GET /api/combustao-estacionaria/setores**

Lista todos os setores disponíveis para combustão estacionária.

#### **Exemplo:**
```bash
curl "http://localhost:8080/api/combustao-estacionaria/setores" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 3,
      "setor": "Industrial",
      "descricao": "Setor industrial"
    },
    {
      "id": 4,
      "setor": "Comercial",
      "descricao": "Setor comercial"
    }
  ],
  "total": 8
}
```

---

## **Autenticação e Permissões**

Todos os endpoints requerem autenticação via API Key e permissões específicas:

| Endpoint | Método | Permissão Requerida |
|----------|--------|-------------------|
| `/calcular` | POST | `calcular:escopo1` |
| `/` (persistência) | POST | `calcular:escopo1` |
| `/unidade` | GET | `consultar:resultados` |
| `/hierarquia-completa` | GET | `consultar:resultados` |
| `/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/combustiveis` | GET | `consultar:resultados` |
| `/setores` | GET | `consultar:resultados` |

### **Headers de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Versão GHG**
- A versão GHG é obtida automaticamente do CNPJ da empresa
- Esta versão determina quais fatores de emissão serão utilizados
- É obrigatório fornecer o CNPJ para o cálculo

### **Fluxo de Cálculo:**
1. **Obter versão GHG:** Busca automaticamente do CNPJ fornecido
2. **Identificar combustível:** Busca fatores de emissão específicos da versão
3. **Ajustar por setor:** Aplica fatores específicos do setor de atividade
4. **Calcular emissões:**
   - Emissão CO2 = Consumo × Fator CO2 (versão específica)
   - Emissão CH4 = Consumo × Fator CH4 (ajustado por setor)
   - Emissão N2O = Consumo × Fator N2O (ajustado por setor)
   - Emissão Total = Σ(Emissões × GWP correspondente)
   - Emissões biogênicas calculadas apenas para biocombustíveis

### **Validações:**
1. **CNPJ obrigatório:** Para todos os cálculos (persistente ou não)
2. **Versão GHG:** Deve existir para o CNPJ fornecido
3. **Combustível:** Deve existir para a versão GHG específica
4. **Empresa/Unidade:** Devem existir no banco para persistência
5. **Auditoria:** Validação automática para cálculos persistentes

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, setor, combustível |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **CNPJ não encontrado:**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
}
```

### **Versão GHG não encontrada:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
}
```

### **Validação de auditoria falhou:**
```json
{
  "status": "error",
  "message": "Validação de auditoria falhou para a empresa"
}
```

### **Permissão insuficiente:**
```json
{
  "status": "error",
  "message": "Permissão 'calcular:escopo1' é necessária"
}
```

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Use `/calcular` para testes ou visualização rápida, use `/` para persistência oficial
2. **Versão GHG dinâmica:** Os fatores de emissão variam conforme a versão GHG da empresa
3. **Hierarquia flexível:** Consultas por unidade podem incluir ou não descendentes
4. **Auditoria automática:** Todos os cálculos persistentes passam por validação de auditoria
5. **Metadados completos:** Todas as respostas incluem metadados para rastreabilidade

---

**Última atualização:** 2024-01-15  
**Versão da API:** 1.2.0