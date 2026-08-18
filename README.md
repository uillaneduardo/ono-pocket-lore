# Ono Pocket — Lore & World Bible

Repositório canônico de lore, background, personagens, locais, facções e história do universo de **Ono Pocket**.

A documentação é escrita em Markdown e publicada com Material for MkDocs em:

**https://ono-pocket-lore.wapphub.com.br**

## Fonte de verdade

- `docs/` contém o conteúdo canônico.
- `mkdocs.yml` controla navegação e apresentação.
- alterações em `main` disparam o workflow de deploy para o homelab quando o runner self-hosted estiver configurado.

## Desenvolvimento local

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
mkdocs serve
```

Build de produção:

```bash
mkdocs build --strict
```

## Status de cânone

Os documentos podem usar metadados no front matter:

```yaml
---
title: A Coluna
status: canon
audience: author
spoiler: major
---
```

Valores recomendados de `status`: `canon`, `draft`, `mystery`, `deprecated`.
