# **Combustão Móvel 2**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes da **combustão móvel** (frota veicular, equipamentos móveis, etc.) – versão 2 do módulo.

---

## **Endpoints de Cálculo**

### **1. POST /api/combustao-movel-2/calcular**

Calcula emissões de GEE para uma fonte móvel específica **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "id_combustivel": 8,
  "data": "2024-07-20",
  "cnpj": "00.000.000/0001-91",
  "consumo_mensal": 5000.0,
  "unidade": "l"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_combustivel` | número | ID do combustível | 8 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `consumo_mensal` | número | Consumo mensal (fornecer **ou** `consumo_anual`) | 5000.0 |
| `consumo_anual` | número | Consumo anual (fornecer **ou** `consumo_mensal`) | 60000.0 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição | Padrão |
|-------|------|-----------|--------|
| `unidade` | string | Unidade de medida ("l" = litros, "m³", "kg") | "l" |
| `cnpj_usuario` | string | CNPJ do usuário (para auditoria) | "" |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_combustivel": 8,
    "data": "2024-07-20",
    "cnpj": "00.000.000/0001-91",
    "consumo_mensal": 5000.0,
    "unidade": "l"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_co2": 1.25,
    "emissao_ch4": 0.002,
    "emissao_n2o": 0.0005,
    "emissao_total": 1.418,
    "emissao_co2_biogenico": 0.0
  }
}
```

---

### **2. POST /api/combustao-movel-2**

Calcula e persiste emissões de GEE para uma fonte móvel **com persistência no banco** (cria documento, registro de combustão e emissões).

#### **Requisição:**
```json
{
  "id_combustivel": 8,
  "data": "2024-07-20",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "consumo_mensal": 5000.0,
  "unidade": "l",
  "descricao": "Abastecimento frota SP",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/nota-fiscal",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `id_combustivel` | número | ID do combustível | 8 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `consumo_mensal` | número | Consumo mensal (ou `consumo_anual`) |
| `consumo_anual` | número | Consumo anual (ou `consumo_mensal`) |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição | Padrão |
|-------|------|-----------|--------|
| `unidade` | string | Unidade de medida | "l" |
| `descricao` | string | Descrição adicional | "Combustão móvel 2 – Combustível ID: {id}" |
| `cnpj_fornecedor` | string | CNPJ do fornecedor | null |
| `link_referencia` | string | Link para documento comprobatório | null |
| `id_usuario` | string | UUID do usuário responsável | null |
| `cnpj_usuario` | string | CNPJ do usuário | "" |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "id_combustivel": 8,
    "data": "2024-07-20",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2,
    "consumo_mensal": 5000.0,
    "unidade": "l"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de combustão móvel 2 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_co2": 1.25,
      "emissao_ch4": 0.002,
      "emissao_n2o": 0.0005,
      "emissao_total": 1.418,
      "emissao_co2_biogenico": 0.0
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "combustao_movel_2_id": 123,
      "emissao_id": 456
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "data": "2024-07-20"
    }
  }
}
```

#### **Notas Importantes:**
- Este endpoint executa **validação de auditoria** automática (decorator `@require_audit_validation`).
- A empresa e a unidade devem existir no banco.
- O CNPJ da empresa é obtido automaticamente e utilizado para determinar a versão GHG.
- A categoria do documento é definida como a de **Combustão Móvel 2** (recuperada via `get_categoria_movel_2_id()`).
- Apenas o campo de consumo preenchido (`consumo_mensal` **ou** `consumo_anual`) é persistido na tabela de combustão móvel.

---

## **Endpoints de Consulta**

### **1. GET /api/combustao-movel-2/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de combustão móvel 2, incluindo dados da combustão, combustível e emissões.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2/550e8400-e29b-41d4-a716-446655440000/detalhes" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "documento": {
      "id": "550e8400-e29b-41d4-a716-446655440000",
      "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
      "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
      "nvl_unidade_negocio": 2,
      "data": "2024-07-20",
      "descricao": "Abastecimento frota SP",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/nota-fiscal",
      "id_categoria": 5,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "combustao_movel_2": {
      "id": 123,
      "id_combustivel": 8,
      "consumo_mensal": 5000.0,
      "consumo_anual": null,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "combustivel": {
      "id": 8,
      "nome": "Gasolina Comum",
      "tipo_fossil": "Sim",
      "tipo_biocombustivel": "Não"
    },
    "emissoes": {
      "id": 456,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 1.25,
      "emissao_ch4": 0.002,
      "emissao_n2o": 0.0005,
      "emissao_total": 1.418,
      "emissao_biogenica": 0.0,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **2. GET /api/combustao-movel-2/combustiveis**

Lista todos os combustíveis disponíveis para combustão móvel 2, podendo ser filtrados por CNPJ da empresa (para considerar a versão GHG específica).

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `cnpj_empresa` | ✗ | string | CNPJ da empresa para filtrar combustíveis por versão GHG |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2/combustiveis?cnpj_empresa=00.000.000/0001-91" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 8,
      "nome": "Gasolina Comum",
      "tipo_fossil": "Sim",
      "tipo_biocombustivel": "Não",
      "fatores_emissao": {
        "co2": 0.00025,
        "ch4": 0.0000004,
        "n2o": 0.0000001,
        "co2_biogenico": 0.0
      }
    },
    {
      "id": 9,
      "nome": "Etanol",
      "tipo_fossil": "Não",
      "tipo_biocombustivel": "Sim",
      "fatores_emissao": {
        "co2": 0.0,
        "ch4": 0.0000002,
        "n2o": 0.00000005,
        "co2_biogenico": 0.00018
      }
    }
  ],
  "total": 2
}
```

---

### **3. GET /api/combustao-movel-2/hierarquia-completa**

Retorna a hierarquia completa de emissões de combustão móvel 2 para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | UUID da empresa |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (padrão: 1, min:1, max:10) |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "dd68b58c-9155-42df-97ba-8ec86953c360",
      "nome": "Empresa Exemplo",
      "cnpj": "00.000.000/0000-00"
    },
    "emissao_total_empresa": 5280.35,
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
        "nome": "Matriz",
        "emissao_direta": 1250.0,
        "emissao_total": 5280.35,
        "quantidade_documentos": 5,
        "filhas": [
          {
            "id": "unidade-2-uuid",
            "nivel": 2,
            "nome": "Filial São Paulo",
            "emissao_direta": 980.5,
            "emissao_total": 4030.35,
            "quantidade_documentos": 8,
            "filhas": [
              {
                "id": "unidade-3-uuid",
                "nivel": 3,
                "nome": "Depósito SP",
                "emissao_direta": 3049.85,
                "emissao_total": 3049.85,
                "quantidade_documentos": 3,
                "filhas": []
              }
            ]
          }
        ]
      }
    ]
  }
}
```

---

### **4. GET /api/combustao-movel-2/resumo**

Retorna um resumo estatístico das emissões de combustão móvel 2 para uma empresa, com possibilidade de filtro por ano.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `id_empresa` | ✓ | string | UUID da empresa |
| `ano` | ✗ | string | Ano específico para filtrar (YYYY) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-2/resumo?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&ano=2024" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "ano": 2024,
    "total_documentos": 10,
    "total_unidades": 5,
    "total_emissao": 1500.75,
    "media_emissao": 150.08,
    "min_emissao": 50.25,
    "max_emissao": 300.50,
    "total_co2": 1200.60,
    "total_ch4": 1.5,
    "total_n2o": 0.8
  }
}
```

*Nota:* Em caso de falha na consulta SQL avançada, o endpoint retorna um resumo simplificado com a contagem de documentos.

---

## **Autenticação e Permissões**

Todos os endpoints exigem **API Key** no header `X-API-Key` e a permissão adequada.

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/combustao-movel-2/calcular` | POST | `calcular:escopo1` |
| `/combustao-movel-2` (persistência) | POST | `calcular:escopo1` |
| `/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/combustiveis` | GET | `consultar:resultados` |
| `/hierarquia-completa` | GET | `consultar:resultados` |
| `/resumo` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Versão GHG**
- A versão GHG é obtida automaticamente a partir do **CNPJ da empresa** (informado diretamente no endpoint `/calcular` ou recuperado da base no endpoint com persistência).
- Os fatores de emissão aplicados são específicos para **combustão móvel** e variam conforme a versão GHG.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** através do CNPJ.
2. **Identificar combustível** e buscar fatores de emissão para a versão GHG e categoria móvel.
3. **Calcular emissões** com base no consumo informado:
   - Emissão CO₂ = Consumo × Fator CO₂ (versão específica)
   - Emissão CH₄ = Consumo × Fator CH₄
   - Emissão N₂O = Consumo × Fator N₂O
   - Emissão Total = Σ(Emissões × Potenciais de Aquecimento Global – GWP)
   - Emissão biogênica (CO₂ de origem renovável) é calculada apenas para biocombustíveis.
4. **Validar consumo**: deve ser informado **consumo_mensal** **ou** **consumo_anual**. Ambos são aceitos, mas apenas o campo preenchido é utilizado e persistido.

### **Validações:**
- **CNPJ obrigatório** em `/calcular`; no fluxo persistente, o CNPJ é obtido da empresa cadastrada.
- **Empresa e unidade** devem existir e estar ativas.
- **Nível da unidade** deve estar entre 1 e 10.
- **Categoria de Combustão Móvel 2** deve estar configurada no sistema.
- **Auditoria automática** é aplicada no fluxo persistente.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, combustível |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Consumo não informado:**
```json
{
  "error": "Informe consumo_mensal ou consumo_anual"
}
```

### **Empresa não encontrada:**
```json
{
  "error": "Empresa não encontrada"
}
```

### **Unidade não encontrada:**
```json
{
  "error": "Unidade de negócio não encontrada"
}
```

### **CNPJ não localizado (cálculo sem persistência):**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
}
```

### **Nível da unidade fora do intervalo:**
```json
{
  "error": "nvl_unidade_negocio deve estar entre 1 e 10"
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

1. **Dois modos de cálculo:** Use `/calcular` para testes e simulações; use o POST sem `/calcular` para lançamento oficial com persistência.
2. **Versão GHG dinâmica:** Os fatores de emissão dependem da versão GHG vigente para o CNPJ da empresa.
3. **Consumo mensal vs. anual:** Forneça **apenas um** dos campos; ambos são aceitos, mas o sistema tratará o preenchido.
4. **Unidade de medida:** O campo `unidade` (padrão `"l"`) é utilizado no cálculo, mas não é persistido. Certifique-se de que o fator de emissão corresponda à unidade informada.
5. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes.
6. **Categoria:** O sistema automaticamente associa os documentos à categoria **Combustão Móvel 2**, que deve estar previamente cadastrada.
7. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.

---
