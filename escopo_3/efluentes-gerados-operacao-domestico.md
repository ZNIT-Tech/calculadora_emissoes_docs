# **Efluentes Gerados — Operação Doméstica**

Calcula emissões de efluentes domésticos considerando tratamentos, carga orgânica, recuperação de CH4, N2O e parâmetros associados.

---

## **Endpoint**

### **POST /api/v1/efluentes-gerados-operacao-domestico**

Payload mínimo (exemplos):
- `possui_tratamento` (bool)
- `lancado_ambiente` (bool)
- `disposicao_final` (str)
- `possui_volumeGerado_cargaOrganica_teorNitrogenio` (bool)

O endpoint aceita muitos parâmetros adicionais para modelagem (veja implementação para lista completa).

## **Metodologia**
- Considera fracionamento do material orgânico, remoção por lodo, número de pessoas, recuperação de CH4, fatores N2O por tratamento e disposição. 
- Produz emissões separadas de CH4 (recuperado e emitido) e N2O, retornando `emissao_total` quando aplicável.

Referência: `src/app/escopos/escopo_3/efluentes_gerados_operacao_domestico`
