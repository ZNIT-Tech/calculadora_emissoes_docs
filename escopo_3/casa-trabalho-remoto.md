# **Casa-Trabalho — Remoto**

Calcula emissões associadas ao trabalho remoto com base em número de trabalhadores, dias trabalhados e fator anual de emissão.

Implementação de referência: `src/app/escopos/escopo_3/casa_trabalho_remoto`.

---

## **Endpoint de Cálculo**

### **POST /api/trabalho-remoto**

#### **Requisição:**
```json
{
	"nro_trabalhadores": 50,
	"dias_trabalhados": 22,
	"dias_trabalhados_mes": 22,
	"input_date": "2024-09-01"
}
```

#### **Parâmetros Obrigatórios:**
| Campo | Tipo | Descrição | Exemplo |
|-------|------|-----------|---------|
| `nro_trabalhadores` | número | Quantidade de trabalhadores remotos | 50 |
| `dias_trabalhados` | número | Dias de trabalho remoto considerados no período | 22 |
| `dias_trabalhados_mes` | número | Dias úteis de referência do mês | 22 |
| `input_date` | string | Data de referência (YYYY-MM-DD) | "2024-09-01" |

#### **Exemplo de Uso:**
```bash
curl -X POST "http://localhost:8080/api/trabalho-remoto" \
	-H "Content-Type: application/json" \
	-d '{
		"nro_trabalhadores": 50,
		"dias_trabalhados": 22,
		"dias_trabalhados_mes": 22,
		"input_date": "2024-09-01"
	}'
```

#### **Resposta de Sucesso (exemplo):**
```json
{
	"status": "success",
	"data": {
		"ano": 2024,
		"consumo_medio": 0.2904,
		"fator_emissao": 0.0817,
		"emissao_total": 0.02372568
	}
}
```

#### **Erros Comuns:**
- `400`: campos obrigatórios faltando
- `400`: erro de conversão numérica ou data inválida
- `500`: erro interno de cálculo

---

## **Metodologia de Cálculo**

1. Define o ano com base em `input_date`.
2. Usa consumo médio fixo por trabalhador (`150`).
3. Calcula consumo médio total com:

$$
	ext{consumo_medio} = \frac{\text{nro_trabalhadores} \times 150 \times \text{dias_trabalhados_mes}}{5} \times \frac{\text{dias_trabalhados} \times 8}{10^6}
$$

4. Busca fator anual `fe sin` para o ano/país.
5. Calcula emissão total:

$$
	ext{emissao_total} = \text{consumo_medio} \times \text{fator_emissao}
$$

---

## **Observação Técnica**

No backend atual, o `calculator` inclui parâmetro `cnpj` na assinatura, mas o `blueprint` não o envia. Em algumas versões, isso pode gerar erro interno até que a integração seja ajustada.
