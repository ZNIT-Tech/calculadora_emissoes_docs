# **Combustão Móvel 3**

Permite calcular e consultar emissões de gases de efeito estufa (GEE) provenientes da **combustão móvel** (frota veicular, equipamentos móveis rodoviários) com base no **tipo de veículo**, **ano de fabricação** e **distância percorrida**. Módulo versão 3, específico para veículos rodoviários.

---

## **Endpoints de Cálculo**

### **1. POST /api/combustao-movel-3/calcular**

Calcula emissões de GEE para um veículo específico **SEM persistir no banco**.

#### **Requisição:**
```json
{
  "tipo_veiculo_id": 1,
  "ano_veiculo": 2020,
  "data": "2024-07-20",
  "cnpj": "00.000.000/0001-91",
  "distancia_mensal": 1500.5
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `tipo_veiculo_id` | número | ID do tipo de veículo (consulte `/veiculos`) | 1 |
| `ano_veiculo` | número | Ano de fabricação do veículo (1900–2100) | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `cnpj` | string | CNPJ da empresa para obtenção da versão GHG | "00.000.000/0001-91" |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `distancia_mensal` | número | Distância percorrida no mês (km) – **ou** `distancia_anual` | 1500.5 |
| `distancia_anual` | número | Distância percorrida no ano (km) – **ou** `distancia_mensal` | 18000.0 |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_empresa` | string | UUID da empresa (para metadados, não persiste) |
| `id_unidade_negocio` | string | UUID da unidade de negócio (para metadados, não persiste) |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3/calcular" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "tipo_veiculo_id": 1,
    "ano_veiculo": 2020,
    "distancia_mensal": 1500.5,
    "data": "2024-07-20",
    "cnpj": "00.000.000/0001-91"
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões realizado com sucesso",
  "data": {
    "emissao_total": 252.0,
    "emissao_co2": 250.5,
    "emissao_ch4": 1.2,
    "emissao_n2o": 0.3,
    "emissao_co2_biogenico": 10.5,
    "metadados": {
      "veiculo": {
        "id": 1,
        "nome": "Automóvel - Gasolina",
        "combustivel_fossil": "Sim",
        "combustivel_biocombustivel": "Não"
      },
      "distancia": {
        "mensal": 1500.5,
        "anual": 0,
        "unidade": "km"
      },
      "ano_veiculo": 2020,
      "data_consumo": "2024-07-20",
      "versao_ghg": "2023"
    }
  }
}
```

---

### **2. POST /api/combustao-movel-3**

Calcula e persiste emissões de GEE para um veículo **com persistência no banco** (cria documento, registro de combustão móvel 3 e emissões).

#### **Requisição:**
```json
{
  "tipo_veiculo_id": 1,
  "ano_veiculo": 2020,
  "data": "2024-07-20",
  "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
  "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
  "nvl_unidade_negocio": 2,
  "distancia_mensal": 1500.5,
  "descricao": "Consumo mensal frota executiva",
  "cnpj_fornecedor": "12.345.678/0001-90",
  "link_referencia": "http://exemplo.com/planilhas/frota.xlsx",
  "id_usuario": "550e8400-e29b-41d4-a716-446655440000"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `tipo_veiculo_id` | número | ID do tipo de veículo | 1 |
| `ano_veiculo` | número | Ano de fabricação do veículo | 2020 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-07-20" |
| `id_empresa` | string | UUID da empresa | "dd68b58c-9155..." |
| `id_unidade_negocio` | string | UUID da unidade de negócio | "680a4fe4-d2f1..." |
| `nvl_unidade_negocio` | número | Nível hierárquico (1–10) | 2 |

#### **Parâmetros Condicionalmente Obrigatórios:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `distancia_mensal` | número | Distância mensal (km) – **ou** `distancia_anual` |
| `distancia_anual` | número | Distância anual (km) – **ou** `distancia_mensal` |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição |
|-------|------|-----------|
| `id_usuario` | string | UUID do usuário responsável |
| `descricao` | string | Descrição adicional |
| `cnpj_fornecedor` | string | CNPJ do fornecedor |
| `link_referencia` | string | Link para documento comprobatório |

#### **Exemplo de Uso:**
```bash
curl -X POST "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: sua-api-key" \
  -d '{
    "tipo_veiculo_id": 1,
    "ano_veiculo": 2020,
    "distancia_mensal": 1500.5,
    "data": "2024-07-20",
    "id_empresa": "dd68b58c-9155-42df-97ba-8ec86953c360",
    "id_unidade_negocio": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
    "nvl_unidade_negocio": 2
  }'
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "message": "Cálculo de emissões de combustão móvel 3 realizado com sucesso",
  "data": {
    "resultado_calculo": {
      "emissao_total": 252.0,
      "emissao_co2": 250.5,
      "emissao_ch4": 1.2,
      "emissao_n2o": 0.3,
      "emissao_co2_biogenico": 10.5
    },
    "ids_gerados": {
      "documento_id": "550e8400-e29b-41d4-a716-446655440000",
      "combustao_movel_3_id": 12345,
      "emissao_id": 67890
    },
    "metadados": {
      "empresa": "Empresa Exemplo",
      "unidade": "Filial São Paulo",
      "veiculo": "Automóvel - Gasolina",
      "ano_veiculo": 2020,
      "data": "2024-07-20"
    }
  }
}
```

#### **Notas Importantes:**
- Endpoint submete à **validação de auditoria** (`@require_audit_validation`).
- A categoria do documento é automaticamente definida como **Combustão Móvel 3** (ID recuperado dinamicamente; fallback = 3).
- A empresa e a unidade devem existir e estar associadas.
- O CNPJ da empresa é utilizado para obter a versão GHG e os fatores de emissão adequados.
- Apenas a distância informada (`mensal` **ou** `anual`) é persistida.

---

## **Endpoints de Consulta**

### **1. GET /api/combustao-movel-3/unidade**

Consulta emissões de combustão móvel 3 agregadas por unidade de negócio, com possibilidade de incluir unidades descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_unidade` | ✓ | string | UUID da unidade de negócio | — |
| `nivel` | ✓ | número | Nível hierárquico da unidade (1–10) | — |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | — |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | — |
| `incluir_descendentes` | ✗ | boolean | Incluir unidades filhas | `true` |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3/unidade?id_unidade=680a4fe4-d2f1-4f2d-ab99-556effb557c9&nivel=2&incluir_descendentes=true" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": {
    "unidade": {
      "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
      "nome": "Filial São Paulo",
      "nivel": 2
    },
    "emissao_total": 1250.75,
    "quantidade_documentos": 8,
    "quantidade_unidades": 3,
    "detalhes": [
      {
        "documento_id": "550e8400-e29b-41d4-a716-446655440000",
        "data": "2024-07-20",
        "descricao": "Consumo mensal frota executiva",
        "unidade": {
          "id": "680a4fe4-d2f1-4f2d-ab99-556effb557c9",
          "nome": "Filial São Paulo",
          "nivel": 2
        },
        "veiculo": {
          "id": 1,
          "nome": "Automóvel - Gasolina"
        },
        "emissoes": {
          "co2": 250.5,
          "ch4": 1.2,
          "n2o": 0.3,
          "total": 252.0,
          "biogenica": 10.5
        },
        "created_at": "2024-07-20T14:30:00Z"
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

### **2. GET /api/combustao-movel-3/{documento_id}/detalhes**

Consulta detalhes completos de um documento específico de combustão móvel 3.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `documento_id` | ✓ | string | UUID do documento (path parameter) |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3/550e8400-e29b-41d4-a716-446655440000/detalhes" \
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
      "descricao": "Consumo mensal frota executiva",
      "cnpj_fornecedor": "12.345.678/0001-90",
      "link_referencia": "http://exemplo.com/planilhas/frota.xlsx",
      "id_categoria": 3,
      "id_usuario": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "combustao_movel_3": {
      "id": 12345,
      "id_veiculo": 1,
      "ano_veiculo": 2020,
      "consumo_mensal": 1500.5,
      "consumo_anual": null,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "created_at": "2024-07-20T14:30:00Z"
    },
    "veiculo": {
      "id": 1,
      "nome": "Automóvel - Gasolina",
      "combustivel_fossil": "Sim",
      "combustivel_biocombustivel": "Não"
    },
    "emissoes": {
      "id": 67890,
      "id_documento": "550e8400-e29b-41d4-a716-446655440000",
      "emissao_co2": 250.5,
      "emissao_ch4": 1.2,
      "emissao_n2o": 0.3,
      "emissao_total": 252.0,
      "emissao_biogenica": 10.5,
      "created_at": "2024-07-20T14:30:00Z"
    }
  }
}
```

---

### **3. GET /api/combustao-movel-3/veiculos**

Lista todos os veículos disponíveis para cálculo de combustão móvel 3, opcionalmente filtrados por versão GHG.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição |
|-----------|-------------|------|-----------|
| `versao_ghg` | ✗ | string | Versão GHG para filtrar os fatores de emissão |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3/veiculos?versao_ghg=2023" \
  -H "X-API-Key: sua-api-key"
```

#### **Resposta:**
```json
{
  "status": "success",
  "data": [
    {
      "id": 1,
      "veiculo": "Automóvel - Gasolina",
      "fossil": "Sim",
      "biocombustivel": "Não",
      "fatores_emissao": {
        "co2": 0.167,
        "ch4": 0.0008,
        "n2o": 0.0002,
        "co2_biogenico": 0.0
      }
    },
    {
      "id": 2,
      "veiculo": "Automóvel - Etanol",
      "fossil": "Não",
      "biocombustivel": "Sim",
      "fatores_emissao": {
        "co2": 0.0,
        "ch4": 0.0004,
        "n2o": 0.0001,
        "co2_biogenico": 0.12
      }
    }
  ],
  "total": 2
}
```

---

### **4. GET /api/combustao-movel-3/hierarquia-completa**

Retorna a hierarquia completa de emissões de combustão móvel 3 para uma empresa, agregando emissões por unidade de negócio e suas descendentes.

#### **Parâmetros de Query:**
| Parâmetro | Obrigatório | Tipo | Descrição | Padrão |
|-----------|-------------|------|-----------|--------|
| `id_empresa` | ✓ | string | UUID da empresa | — |
| `nivel_inicio` | ✗ | número | Nível hierárquico inicial (1–10) | 1 |
| `ano` | ✗ | string | Ano para filtrar (formato YYYY) | — |

#### **Exemplo:**
```bash
curl "https://calculadora-emissoes-849629781489.southamerica-east1.run.app/api/combustao-movel-3/hierarquia-completa?id_empresa=dd68b58c-9155-42df-97ba-8ec86953c360&nivel_inicio=1&ano=2024" \
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

## **Autenticação e Permissões**

Todos os endpoints exigem **API Key** no header `X-API-Key` e a permissão adequada.

| Endpoint | Método | Permissão Requerida |
|----------|--------|---------------------|
| `/combustao-movel-3/calcular` | POST | `calcular:escopo1` |
| `/combustao-movel-3` (persistência) | POST | `calcular:escopo1` |
| `/unidade` | GET | `consultar:resultados` |
| `/{documento_id}/detalhes` | GET | `consultar:resultados` |
| `/veiculos` | GET | `consultar:resultados` |
| `/hierarquia-completa` | GET | `consultar:resultados` |

**Header de Autenticação:**
```http
X-API-Key: sua-chave-api-aqui
```

---

## **Regras de Cálculo Atualizadas**

### **Versão GHG**
- A versão GHG é obtida automaticamente a partir do **CNPJ** informado (endpoint `/calcular`) ou do CNPJ da empresa cadastrada (endpoint com persistência).
- Os fatores de emissão variam conforme a **versão GHG** e o **tipo de veículo**.

### **Fluxo de Cálculo:**
1. **Obter versão GHG** através do CNPJ.
2. **Identificar veículo** e buscar fatores de emissão na tabela `lista_veiculos_combustiveis` (específicos da versão GHG).
3. **Validar ano do veículo** (entre 1900 e 2100).
4. **Calcular emissões** com base na distância informada:
   - Emissão CO₂ = Distância × Fator CO₂
   - Emissão CH₄ = Distância × Fator CH₄
   - Emissão N₂O = Distância × Fator N₂O
   - Emissão Total = Σ(Emissões × Potenciais de Aquecimento Global – GWP)
   - Emissão biogênica (CO₂ de origem renovável) é calculada apenas para veículos que utilizam biocombustíveis.
5. **Consumo**: deve ser informada **distancia_mensal** **ou** **distancia_anual**. Apenas o campo preenchido é utilizado e persistido.

### **Validações:**
- **CNPJ obrigatório** em `/calcular`; no fluxo persistente, o CNPJ é obtido da empresa.
- **Empresa e unidade** devem existir e estar ativas.
- **Nível da unidade** deve estar entre 1 e 10.
- **Categoria de Combustão Móvel 3** deve estar configurada no sistema (fallback ID = 3).
- **Auditoria automática** é aplicada no fluxo persistente.

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Campos obrigatórios ausentes ou inválidos | Verifique o corpo da requisição |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Recurso não encontrado | Verifique IDs de empresa, unidade, tipo de veículo |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Distância não informada:**
```json
{
  "error": "Pelo menos um dos campos deve ser fornecido: distancia_mensal ou distancia_anual"
}
```

### **Ano do veículo inválido:**
```json
{
  "error": "ano_veiculo deve estar entre 1900 e 2100"
}
```

### **Veículo não encontrado:**
```json
{
  "error": "Veículo ID 99 não encontrado"
}
```

### **Empresa não encontrada:**
```json
{
  "error": "Empresa não encontrada"
}
```

### **CNPJ não localizado (cálculo sem persistência):**
```json
{
  "error": "Não foi possível obter a versão GHG para o CNPJ: 00.000.000/0001-91"
}
```

### **Versão GHG não encontrada para o CNPJ:**
```json
{
  "error": "Versão GHG não encontrada para o CNPJ: 00.000.000/0001-91"
}
```

### **Nível da unidade fora do intervalo:**
```json
{
  "error": "nvl_unidade_negocio deve estar entre 1 e 10"
}
```

---

## **Observações Importantes**

1. **Dois modos de cálculo:** Utilize `/calcular` para testes e validações rápidas; utilize o POST sem `/calcular` para lançamento oficial com persistência e auditoria.
2. **Versão GHG dinâmica:** Os fatores de emissão são determinados pela versão GHG vigente para o CNPJ da empresa.
3. **Distância mensal vs. anual:** Forneça **apenas um** dos campos; o sistema ignorará o outro e persistirá somente o preenchido.
4. **Ano do veículo:** É obrigatório e deve estar entre 1900 e 2100. Pode influenciar fatores de emissão para algumas versões GHG (veículos mais novos podem ter padrões distintos).
5. **Categoria:** A categoria **Combustão Móvel 3** é obtida dinamicamente; se não existir, o sistema utiliza o fallback `3`. Certifique-se de que a categoria está cadastrada.
6. **Hierarquia flexível:** A consulta de hierarquia permite definir o nível inicial e inclui recursivamente todas as unidades descendentes, fornecendo uma visão consolidada.
7. **Auditoria:** Todos os cálculos persistentes passam por validação de auditoria, garantindo rastreabilidade e conformidade.
8. **Metadados opcionais:** Nos cálculos sem persistência, você pode incluir `id_empresa` e `id_unidade_negocio` para enriquecer os metadados, mesmo sem gravação no banco.

---