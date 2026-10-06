# Guía de contribución

Gracias por contribuir. Lee el `README.md` antes de empezar.

## 1. Puesta en marcha rápida

Requisitos: Git, uv y Docker (con Compose v2).

### Configuración del entorno

    cp .env.example .env
    uv sync
    uv run pre-commit install --install-hooks

### Levantar el entorno

    docker compose up -d

- Odoo: `http://localhost:8069` (`admin`/`admin`)
- PostgreSQL: `localhost:5433` (`odoo`/`odoo`)

Los puertos se cambian en `.env` (`ODOO_PORT`, `DB_PORT`).

### Tests

    docker compose -f docker-compose.test.yml up --abort-on-container-exit
    docker compose -f docker-compose.test.yml down -v

## 2. Flujo de trabajo (Git Flow)

1. Crea una rama desde `develop`: `feature/<TICKET>-<id>-<slug>` (o `bugfix/…`,
   `hotfix/…`).
2. Haz cambios pequeños y cohesivos; añade o actualiza **tests** y **documentación**.
3. Comprueba que pasan `pre-commit` y la batería de tests.
4. Abre un **Pull Request** hacia `develop` y enlaza el ticket.
5. Supera la revisión (≥1 revisor; ≥2 si toca seguridad) con la CI en verde.
6. _Squash merge_. Nunca hagas _push_ directo a `main`/`develop`.

## 3. Mensajes de commit (Conventional Commits)

`tipo(scope): resumen` — tipos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`,
`test`, `build`, `ci`, `chore`. Ejemplo: `feat(projects): alta de proyecto`. Los
_breaking changes_ se marcan con `!` o `BREAKING CHANGE:` en el cuerpo.

## 4. Definición de Hecho (Definition of Done)

- [ ] Código conforme a las guías de `docs/odoo-docs.md` (Odoo y OCA).
- [ ] Tests añadidos o actualizados, y en verde.
- [ ] `pre-commit` sin hallazgos nuevos (lint, formato, secretos).
- [ ] Documentación del módulo actualizada.
- [ ] Revisión de seguridad (OWASP) considerada para cambios sensibles.
