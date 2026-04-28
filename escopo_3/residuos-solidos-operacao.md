# **Resíduos Sólidos — Operação**

Calcula emissões associadas ao gerenciamento de resíduos sólidos durante a operação (coleta, tratamento e disposição).

---

## **Endpoint**

### **POST /api/residuos-solidos-operacao**

#### **Payload (exemplo)**
```json
{
  "massa": 1000.0,
  "tratamento": "aterro",
  "id": 123
}
```

## **Metodologia (base)**
- Pondera massa por rota de tratamento (aterro, incineração, reciclagem) e usa fatores de emissão por tratamento para calcular CO2, CH4, N2O e total CO2e.

Observação: o `Documentation.md` no backend indica que o `calculator.py` é um stub; valide com engenharia se a lógica precisa ser ajustada.

Referência: `src/app/escopos/escopo_3/residuos_solidos_operacao`
