# **Dashboard do Escopo 1**

Fornece métricas, análises e relatórios consolidados sobre as emissões do Escopo 1, incluindo quantidade de documentos, emissões totais, tendências e performance.

---

## **Endpoints de Dashboard**

### **1. GET /api/dashboard/escopo1/status**

Retorna o status e disponibilidade dos dados do dashboard para uma empresa específica.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/status?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "empresa_nome": "Nome da Empresa",
    "dashboard_disponivel": true,
    "estatisticas_gerais": {
      "documentos_escopo1_ano_atual": 150,
      "emissoes_escopo1_ano_atual_tco2e": 12500.75,
      "media_emissao_por_documento": 83.34
    },
    "ultima_atualizacao": "2024-01-15T10:30:00Z",
    "endpoints_disponiveis": [
      {
        "nome": "documentos_escopo1",
        "url": "/dashboard/escopo1/documentos",
        "descricao": "Quantidade de documentos do Escopo 1"
      },
      {
        "nome": "emissoes_totais_escopo1",
        "url": "/dashboard/escopo1/emissoes-totais",
        "descricao": "Emissões totais do Escopo 1"
      }
    ],
    "recomendacao": "Dashboard disponível para uso"
  }
}
```

---

### **2. GET /api/dashboard/escopo1/documentos**

Retorna a quantidade e estatísticas de documentos do Escopo 1, com opções de filtragem.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | `2024-01-01` |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | `2024-12-31` |
| `nivel_unidade` | ✗ | número | Nível hierárquico da unidade (1-10) | `1` |
| `id_unidade` | ✗ | string | ID específico da unidade de negócio | `aaef2f34-af10-41bc-8060-1105596ebe46` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/documentos?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&data_inicio=2024-01-01&data_fim=2024-12-31" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "total_documentos": 150,
    "por_categoria": [
      {
        "categoria_id": 4,
        "categoria_nome": "Combustão Estacionária",
        "categoria_geral": "Processos de combustão",
        "quantidade": 80,
        "documentos": [
          {
            "id": "550e8400-e29b-41d4-a716-446655440000",
            "data": "2024-06-15",
            "descricao": "Combustão estacionária - Setor: Industrial, Combustível ID: 5"
          }
        ]
      }
    ],
    "por_mes": {
      "2024-01": 10,
      "2024-02": 15
    },
    "periodo": {
      "data_inicio": "2024-01-01",
      "data_fim": "2024-12-31",
      "filtro_nivel": null,
      "filtro_unidade": null
    }
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "documentos_escopo1"
  }
}
```

---

### **3. GET /api/dashboard/escopo1/emissoes-totais**

Retorna as emissões totais do Escopo 1 em tCO2e, com opções de agrupamento.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `data_inicio` | ✗ | string | Data inicial (YYYY-MM-DD) | `2023-01-01` |
| `data_fim` | ✗ | string | Data final (YYYY-MM-DD) | `2024-12-31` |
| `agrupar_por` | ✗ | string | Tipo de agrupamento (`ano`, `mes`, `categoria`, `unidade`). Padrão: `ano` | `categoria` |
| `nivel_unidade` | ✗ | número | Nível hierárquico da unidade (1-10) | `1` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/emissoes-totais?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&agrupar_por=categoria&data_inicio=2024-01-01&data_fim=2024-12-31" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "total_emissoes_tco2e": 12500.75,
    "agrupamento": "categoria",
    "detalhes": [
      {
        "categoria_id": 4,
        "categoria_nome": "Combustão Estacionária",
        "categoria_geral": "Processos de combustão",
        "total": 8000.5,
        "co2": 7500.0,
        "ch4": 300.5,
        "n2o": 200.0,
        "biogenica": 0.0,
        "quantidade_documentos": 80
      }
    ],
    "periodo": {
      "data_inicio": "2024-01-01",
      "data_fim": "2024-12-31"
    }
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "emissoes_totais_escopo1",
    "agrupamento_aplicado": "categoria"
  }
}
```

---

### **4. GET /api/dashboard/emissoes-todos-escopos**

Retorna o total de emissões somando todos os escopos (1, 2 e 3) para uma empresa.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `ano` | ✗ | número | Ano específico para filtrar (YYYY) | `2024` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/emissoes-todos-escopos?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&ano=2024" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "ano": 2024,
    "total_geral": 35000.5,
    "por_escopo": [
      {
        "escopo": 1,
        "descricao": "Escopo 1",
        "total_tco2e": 12500.75,
        "percentual": 35.71
      },
      {
        "escopo": 2,
        "descricao": "Escopo 2",
        "total_tco2e": 15000.25,
        "percentual": 42.86
      },
      {
        "escopo": 3,
        "descricao": "Escopo 3",
        "total_tco2e": 7500.5,
        "percentual": 21.43
      }
    ],
    "unidades_medida": "tCO2e"
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "total_todos_escopos"
  }
}
```

---

### **5. GET /api/dashboard/escopo1/tendencias**

Retorna tendências de emissões do Escopo 1 ao longo dos anos, com análise de crescimento.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `anos_comparacao` | ✗ | número | Número de anos para comparação (1-10). Padrão: 3 | `5` |
| `nivel_unidade` | ✗ | número | Nível hierárquico da unidade (1-10) | `1` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/tendencias?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&anos_comparacao=5" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "tendencias": [
      {
        "ano": 2020,
        "total_tco2e": 15000.0,
        "quantidade_documentos": 120
      },
      {
        "ano": 2021,
        "total_tco2e": 14500.0,
        "quantidade_documentos": 130
      },
      {
        "ano": 2022,
        "total_tco2e": 14000.0,
        "quantidade_documentos": 140
      },
      {
        "ano": 2023,
        "total_tco2e": 13500.0,
        "quantidade_documentos": 145
      },
      {
        "ano": 2024,
        "total_tco2e": 12500.75,
        "quantidade_documentos": 150
      }
    ],
    "anos_analisados": [2020, 2021, 2022, 2023, 2024],
    "crescimento_percentual": -16.67,
    "direcao": "baixa",
    "recomendacao": "Excelente: Redução significativa. Continue com as boas práticas."
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "tendencias_escopo1",
    "anos_comparacao": 5
  }
}
```

---

### **6. GET /api/dashboard/escopo1/performance**

Retorna métricas de performance do Escopo 1, incluindo comparação com ano anterior e status.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `ano` | ✗ | número | Ano para análise (YYYY). Se não informado, usa o ano atual. | `2023` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/performance?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&ano=2024" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "ano": 2024,
    "total_emissoes_tco2e": 12500.75,
    "quantidade_documentos": 150,
    "media_emissao_por_documento": 83.34,
    "variacao_vs_ano_anterior_percent": -7.41,
    "variacao_vs_ano_anterior_tco2e": -1000.25,
    "intensidade_emissoes": {
      "valor": 12500.75,
      "unidade": "tCO2e/ano",
      "metodologia": "Emissões totais anuais"
    },
    "status_performance": "boa"
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "performance_escopo1"
  }
}
```

---

### **7. GET /api/dashboard/escopo1/relatorio-consolidado**

Retorna um relatório consolidado completo do Escopo 1, incluindo resumo executivo, distribuição de emissões, métricas detalhadas, tendências e insights.

#### **Parâmetros:**
| Parâmetro | Obrigatório | Tipo | Descrição | Exemplo |
|-----------|-------------|------|-----------|---------|
| `id_empresa` | ✓ | string | ID da empresa (UUID) | `bf725c29-2b8c-46dc-a3ad-880e511e7804` |
| `ano` | ✗ | número | Ano para relatório (YYYY). Se não informado, usa o ano atual. | `2023` |

#### **Exemplo de Uso:**
```bash
curl -X GET "http://localhost:8080/api/dashboard/escopo1/relatorio-consolidado?id_empresa=bf725c29-2b8c-46dc-a3ad-880e511e7804&ano=2024" \
  -H "X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q"
```

#### **Resposta de Sucesso:**
```json
{
  "status": "success",
  "data": {
    "empresa": {
      "id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
      "nome": "Empresa Exemplo",
      "cnpj": "00.000.000/0000-00",
      "cidade": "São Paulo"
    },
    "periodo": {
      "ano": 2024,
      "data_geracao": "2024-06-15T10:30:00Z"
    },
    "resumo_executivo": {
      "total_emissoes_tco2e": 12500.75,
      "quantidade_documentos": 150,
      "status_performance": "boa",
      "variacao_ano_anterior": -7.41,
      "tendencia_ultimos_anos": -16.67
    },
    "distribuicao_emissoes": {
      "por_categoria": [
        {
          "categoria": "Combustão Estacionária",
          "total_tco2e": 8000.5,
          "percentual": 64.0,
          "quantidade_documentos": 80
        }
      ],
      "categoria_maior_impacto": {
        "categoria": "Combustão Estacionária",
        "total_tco2e": 8000.5,
        "percentual": 64.0
      },
      "categoria_menor_impacto": {
        "categoria": "Emissões Fugitivas",
        "total_tco2e": 500.25,
        "percentual": 4.0
      }
    },
    "metricas_detalhadas": {
      "ano": 2024,
      "total_emissoes_tco2e": 12500.75,
      "quantidade_documentos": 150,
      "media_emissao_por_documento": 83.34,
      "variacao_vs_ano_anterior_percent": -7.41,
      "variacao_vs_ano_anterior_tco2e": -1000.25,
      "intensidade_emissoes": {
        "valor": 12500.75,
        "unidade": "tCO2e/ano",
        "metodologia": "Emissões totais anuais"
      },
      "status_performance": "boa"
    },
    "tendencias_historicas": {
      "tendencias": [
        {
          "ano": 2022,
          "total_tco2e": 14000.0,
          "quantidade_documentos": 140
        },
        {
          "ano": 2023,
          "total_tco2e": 13500.0,
          "quantidade_documentos": 145
        },
        {
          "ano": 2024,
          "total_tco2e": 12500.75,
          "quantidade_documentos": 150
        }
      ],
      "anos_analisados": [2022, 2023, 2024],
      "crescimento_percentual": -10.71,
      "direcao": "baixa",
      "recomendacao": "Excelente: Redução significativa. Continue com as boas práticas."
    },
    "insights_recomendacoes": [
      "Performance excelente! Continue com as práticas atuais.",
      "A categoria 'Combustão Estacionária' representa mais de 50% das emissões. Foco em eficiência nesta área pode ter grande impacto."
    ],
    "metadados": {
      "versao_relatorio": "1.0",
      "metodologia": "GHG Protocol",
      "unidade_medida": "tCO2e"
    }
  },
  "metadados": {
    "empresa_id": "bf725c29-2b8c-46dc-a3ad-880e511e7804",
    "tipo_consulta": "relatorio_consolidado_escopo1"
  }
}
```

---

## **Autenticação e Permissões**

Todos os endpoints requerem autenticação via API Key e a permissão `dashboard:consultar`.

### **Headers de Autenticação:**
```http
X-API-Key: Mjb5m4aFhHGDABZAidzsGDwLYLIc6M0Q
```

---

## **Códigos de Erro**

| Código | Descrição | Solução |
|--------|-----------|---------|
| `400` | Parâmetros obrigatórios ausentes ou inválidos | Verifique os parâmetros da query string |
| `401` | API Key ausente ou inválida | Configure o header `X-API-Key` |
| `403` | Permissão insuficiente | Verifique suas permissões de usuário |
| `404` | Empresa não encontrada | Verifique o `id_empresa` |
| `500` | Erro interno do servidor | Consulte os logs do sistema |

---

## **Exemplos de Erro Comuns**

### **Parâmetro obrigatório ausente:**
```json
{
  "error": "Parâmetro id_empresa é obrigatório"
}
```

### **Empresa não encontrada:**
```json
{
  "error": "Empresa não encontrada"
}
```

### **Permissão insuficiente:**
```json
{
  "status": "error",
  "message": "Permissão 'dashboard:consultar' é necessária"
}
```

---

## **Observações Importantes**

1. **Filtros de data:** Use `data_inicio` e `data_fim` para limitar o período de análise.
2. **Agrupamentos:** O endpoint de emissões totais permite agrupar por `ano`, `mes`, `categoria` ou `unidade`.
3. **Tendências:** O endpoint de tendências retorna dados históricos e cálculo de crescimento percentual.
4. **Performance:** O status de performance é classificado como `excelente`, `boa`, `estável`, `atenção` ou `crítica`.
5. **Relatório consolidado:** Oferece uma visão completa do Escopo 1, ideal para apresentações e relatórios anuais.

---
