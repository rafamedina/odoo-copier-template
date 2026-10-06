# Odoo dev template

Entorno base para desarrollar módulos de Odoo 18: Docker Compose para levantar Odoo y
PostgreSQL, y pre-commit con las reglas de OCA (ruff, pylint-odoo, prettier, eslint,
detect-secrets, commitlint).

## Empezar un proyecto nuevo

1. Copia esta carpeta con el nombre del proyecto y entra en ella.
2. Inicializa git (pre-commit necesita un repo para instalar los hooks):

   ```bash
   git init -b main
   ```

3. Configura el proyecto:

   ```bash
   cp .env.example .env    # pon PROJECT_NAME, ODOO_DB, ODOO_MODULES, ODOO_TEST_TAGS
   ```

   Cambia también `name` en `pyproject.toml`.

4. Instala las herramientas y los hooks:

   ```bash
   uv sync
   uv run pre-commit install --install-hooks
   ```

5. Crea tu módulo en la raíz del repo (cada carpeta con `__manifest__.py` es un addon):

   ```bash
   docker compose run --rm --no-deps --user "$(id -u):$(id -g)" \
     odoo odoo scaffold my_module /mnt/extra-addons
   ```

   `--user` hace que los archivos generados sean tuyos y no del usuario del contenedor.

6. Levanta el entorno:

   ```bash
   docker compose up -d
   ```

   Odoo queda en `http://localhost:8069` (`admin`/`admin`).

## Tests

```bash
docker compose -f docker-compose.test.yml up --abort-on-container-exit
docker compose -f docker-compose.test.yml down -v
```

## Qué hay que saber

- **Autor y licencia obligatorios.** `.pylintrc` y `.pylintrc-mandatory` exigen
  `"author": "Wavext"` y `"license": "OPL-1"` en cada manifest. Si el proyecto usa otros,
  cambia `manifest-required-authors` y `license-allowed` en ambos archivos.
- **Módulos de terceros.** No los metas en la raíz sin más: pylint-odoo los rechazará por
  autor y licencia. Ponlos en una carpeta y añádela al `exclude` de
  `.pre-commit-config.yaml`.
- **OdooLS.** `odools.toml` espera el código fuente de Odoo en `../odoo` (rama 18.0).
- **Imagen reproducible.** `ODOO_VERSION=18` cambia con cada publicación de la imagen
  oficial. Para fijarla, usa una etiqueta con fecha en `.env`.
- **Discos NTFS/exFAT.** Ahí todos los archivos aparecen como ejecutables. `git init`
  pone `core.fileMode=false` y no pasa nada; si copias el repo a ext4 y el hook
  `check-executables-have-shebangs` falla, quita el bit con `chmod -x`.
- **Secretos.** Las contraseñas de los compose son de desarrollo y están en
  `.secrets.baseline`. Si mueves esas líneas, regenera el baseline:
  `uvx detect-secrets==1.5.0 scan --baseline .secrets.baseline`.

## Documentación

- `CONTRIBUTING.md`: flujo de trabajo y Definition of Done.
- `docs/pre-commit.md`: qué hace cada hook.
- `docs/odoo-docs.md`: enlaces a la documentación de Odoo 18 y OCA.
