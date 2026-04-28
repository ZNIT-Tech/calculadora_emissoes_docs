# **Casa-Trabalho — Público**

Calcula emissões de GEE para deslocamentos casa-trabalho realizados em transporte público.

Implementação de referência: `src/app/escopos/escopo_3/casa_trabalho_publico`.

---

## **Endpoint de Cálculo**

### **POST /api/casa-trabalho-publico**

Calcula emissões com base em tipo de modal, número de passageiros, distância percorrida e dias trabalhados.

#### **Requisição:**
```json
{
	"tipo_veiculo": 1,
	"nro_passageiros": 1,
	"dist_percorrida": 18.5,
	"dias_trabalhados": 22,
	"data": "2024-09-01",
	"ida_volta": true
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `tipo_veiculo` | número | ID do modal de transporte público | 1 |
| `nro_passageiros` | número | Quantidade de passageiros considerada no cálculo | 1 |
| `dist_percorrida` | número | Distância por trajeto (km) | 18.5 |
| `dias_trabalhados` | número | Quantidade de dias no período | 22 |
| `data` | string | Data de referência (YYYY-MM-DD) | "2024-09-01" |

#### **Parâmetros Opcionais:**
| Campo | Tipo | Descrição | Padrão |
|-------|------|-----------|--------|
| `ida_volta` | boolean | Se `true`, duplica as emissões (ida + volta) | `false` |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/casa-trabalho-publico" \
	-H "Content-Type: application/json" \
	-d '{
		"tipo_veiculo": 1,
		"nro_passageiros": 1,
		"dist_percorrida": 18.5,
		"dias_trabalhados": 22,
		"data": "2024-09-01",
		"ida_volta": true
	}'
```

#### **Resposta de Sucesso (exemplo):**
```json
{
	"status": "success",
	"data": {
		"emissao_co2": 0.052,
		"emissao_ch4": 0.0001,
		"emissao_n2o": 0.00005,
		"emissao_total": 0.068,
		"fator_co2": 0.13,
		"fator_ch4": 0.00002,
		"fator_n2o": 0.00001,
		"fator_co2_bio": 0.0,
		"fator_ch4_bio": 0.0,
		"fator_n2o_bio": 0.0,
		"perc_biodiesel": 0.14,
		"tipo_veiculo": "Ônibus municipal"
	}
}
```

#### **Erros Comuns:**
- `400`: campo obrigatório ausente
- `400`: formato de data inválido (deve ser `YYYY-MM-DD`)
- `500`: erro interno de cálculo

---

## **Metodologia de Cálculo**

1. Obtém ano de referência a partir de `data`.
2. Busca percentual anual de biodiesel (`biodiesel no diesel`) para o ano.
3. Identifica o modal pelo `tipo_veiculo`.
4. Busca fatores de emissão do modal/ano em base de fatores.
5. Calcula deslocamento total:

$$
	ext{deslocamento_total} = \text{nro_passageiros} \times \text{dist_percorrida} \times \text{dias_trabalhados}
$$

6. Aplica regra por modal:
- Trem/Metro: apenas CO2 (fator elétrico).
- Balsas: CO2, CH4 e N2O diretos.
- Ônibus: separa parcela fóssil e biocombustível pelo percentual de biodiesel.
7. Converte CH4 e N2O para CO2e usando GWP (`CH4=28`, `N2O=265`).
8. Se `ida_volta=true`, dobra as emissões finais.

---

## **Observação Técnica**

No backend atual, o `calculator` possui parâmetro `cnpj` na assinatura, mas o `blueprint` não o envia explicitamente. Dependendo da versão em execução, isso pode gerar erro interno até o ajuste da integração.
