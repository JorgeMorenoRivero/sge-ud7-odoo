# Errores de instalación

Instalé Odoo 19 Community con Docker, con dos contenedores: `odoo-web` (Odoo) y `odoo-db` (PostgreSQL 16). Al arrancarlos esta vez no me salió ningún error, así que provoqué uno de los fallos típicos para reproducirlo y resolverlo yo.

## Comprobaciones previas

Antes de abrir Odoo miro en Docker Desktop que el motor está en marcha ("Engine running"), que los dos contenedores están en verde y que el puerto 8069 está publicado (`8069:8069`). Se ve en la captura [01-docker-contenedores.png](../capturas/01-docker-contenedores.png).

## Problema reproducido: base de datos parada

| Error o mensaje | Causa | Solución aplicada | Fuente consultada |
|---|---|---|---|
| Al abrir `localhost:8069`, Odoo enseña el formulario "Create Database" aunque las bases existen. En los logs de `odoo-web`: `psycopg2.OperationalError: could not translate host name "db" to address: No address associated with hostname`. Un rato después el texto cambia a `Name or service not known`. | Había parado el contenedor `odoo-db`. Odoo llega a PostgreSQL por el nombre `db` dentro de la red de Docker, y sin ese contenedor el nombre no se resuelve. Como no puede leer la lista de bases, Odoo cree que no hay ninguna y propone crear una. | Arrancar otra vez `odoo-db` con el botón Play de Docker Desktop y recargar la página. Las bases seguían ahí (`db` y `forja_iron_club`), porque parar un contenedor no borra los datos. | Los logs de `odoo-web` en Docker Desktop y [este hilo del foro de Odoo](https://www.odoo.com/es/forum/ayuda-1/how-do-i-fix-this-docker-link-dbdb-error-running-the-current-odoo-docker-130-image-180826), que tiene el mismo mensaje. Allí la causa era otra (la red de macOS con `--link`), pero me sirvió para confirmar que el fallo es de resolución del nombre `db`. |

### Capturas

- Pantalla de Odoo con la base parada: [04-error-bd-parada.png](../capturas/04-error-bd-parada.png)
- Mensaje en los logs: [05-error-logs.png](../capturas/05-error-logs.png)
- Odoo funcionando otra vez: [06-error-resuelto.png](../capturas/06-error-resuelto.png)

## Cómo evitarlo

Antes de abrir Odoo compruebo que los dos contenedores están en verde. Y cuando quiero pararlos, uso el botón de parar y nunca la papelera, porque la papelera borra el contenedor.