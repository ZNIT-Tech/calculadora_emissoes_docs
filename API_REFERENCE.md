**API Reference**
- **Purpose:** gerar documentação por endpoint com tabelas de campos (campo | tipo | obrigatório | descrição), documentar enums e gerar types TypeScript.

Como gerar a documentação:

1. Garanta que o OpenAPI JSON foi gerado no repositório `calculadora_emissoes` em `src/app/calculadora_emissoes_docs/openapi.json` (veja instruções no repositório backend).
2. A partir deste repositório (`calculadora_emissoes_docs`), execute:

```powershell
python tools\generate_api_docs.py
```

Saída:
- `generated_api_docs/`: um arquivo Markdown por endpoint (`METHOD_path.md`) contendo tabelas de campos, enums e bloco TypeScript.
- `generated_api_docs/enums.md`: enums coletados.

Finalizado (definição):
- Todos os endpoints presentes em `openapi.json` têm um arquivo Markdown em `generated_api_docs/`.
- Enums encontrados estão listados em `generated_api_docs/enums.md`.
- As tipagens TypeScript geradas pelo script (no bloco `TypeScript` em cada MD) refletem o schema OpenAPI (campos e tipos).

Testes a realizar:
- Enviar payload com tipo errado → API deve retornar erro 400 com mensagem clara (backend).
- Comparar tabela gerada com `openapi.json` → todos os campos e tipos devem bater (100% match).
- Copiar bloco `TypeScript` de um endpoint para um projeto Next.js/TypeScript e compilar; deve compilar sem erros.

Notas:
- O script tenta inferir tipos básicos (string, integer/number, boolean, array, object).
- Para casos complexos (oneOf/allOf), revisar manualmente o MD e ajustar se necessário.
