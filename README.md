# Eventos NMS — catálogo, facturación e inventario

Sitio estático para GitHub Pages. Archivos:

- `index.html`: todo el sitio (catálogo, factura, registro y panel administrativo).
- `styles.css`: identidad visual de NMS.
- `facturas.json`: **histórico** de las 50 facturas hechas antes del esquema por caja (2 al 5 de noviembre de 2026). Aparecen en el registro con la etiqueta «Histórico» y no descuentan existencias.
- `precios.json`: inventario base del sitio, tal como estaba publicado el 6 de noviembre de 2026: 62 productos de SARA con su precio y el stock ya descontado por las ventas de las facturas históricas.
- `tasa.json`: tasa BCV vigente. También se crea al publicarla.
- `logo-nms.png` y `logo-nms-blanco.png`.

## Pestañas

- **Catálogo**: búsqueda por código, nombre o línea, filtro por línea de negocio y el panel administrativo a la derecha (se oculta con su barra). El botón flotante «Ver factura» lleva a la factura.
- **Factura**: datos del cliente, productos y totales. «Guardar factura» pide confirmación («¿Está seguro?»), la guarda en el registro y descuenta el inventario.
- **Registro de facturas**: lista con desglose por factura y vista «Desglose por producto». Se puede descargar en Excel.

## Tasa BCV y convertidor

- Debajo de las pestañas hay una barra con la **tasa oficial del BCV** y un convertidor US$ ⇄ Bs. que funciona en los dos sentidos.
- Los precios del catálogo muestran su equivalente en bolívares, y la factura muestra el total en Bs. Cada factura guarda la tasa con la que se hizo; cambiar la tasa después no altera las facturas ya guardadas.
- **Panel administrativo → Tasa BCV**: «Actualizar desde el BCV» consulta una fuente pública (DolarApi, y PyDolarVE como respaldo) y rellena la tasa y la fecha para que las revises; «Guardar tasa» la fija. Si la consulta falla, escribe la tasa a mano desde bcv.org.ve.
- La tasa se publica en `tasa.json` con «Publicar tasa», o sola si está marcada la casilla de publicación automática. Quien abra el sitio ve la tasa publicada. Si pasan más de 3 días, la barra se pone ámbar y marca «desactualizada».
- El BCV no ofrece un servicio abierto para páginas web, por eso la consulta usa esas fuentes públicas. Si alguna deja de funcionar, la tasa manual siempre está disponible.

## Conectar con GitHub (panel administrativo)

Contraseña del panel por defecto: **NMS2026**.

1. En GitHub crea un token: Settings → Developer settings → Personal access tokens → Fine-grained tokens. Acceso solo a este repositorio, permiso **Contents: Read and write**.
2. En el panel, bloque «Conexión con GitHub»: usuario u organización, repositorio, rama, nombre del JSON del inventario (`precios.json`), nombre del JSON del registro (`facturas.json`) y el token.
3. «Guardar configuración» y «Probar conexión» (confirma que el token puede escribir).
4. Botones:
   - **Cargar desde GitHub**: reemplaza el inventario por `precios.json`, suma al registro las facturas de `facturas.json` (sin duplicar) y carga la tasa de `tasa.json`.
   - **Publicar inventario**, **Publicar facturas** y **Publicar tasa**: escriben los JSON en el repositorio.
   - Casilla **Publicar automáticamente**: al guardar o anular una factura se publican ambos archivos.

El token se guarda solo en la pestaña del navegador y se borra al cerrarla; hay que volver a escribirlo en cada sesión. La página pública se actualiza 1–2 minutos después de publicar. Al abrir el sitio, si existe `facturas.json` en el mismo repositorio, el registro lo incorpora solo.

## Varios equipos facturando

Cada equipo (caja) tiene un **código propio** (panel administrativo → «Este equipo (caja)»), que va en el número de cada factura: `NMS-C1-00001`. Así dos equipos nunca generan el mismo número, aunque facturen a la vez. Dale a cada equipo un código distinto y fácil de reconocer.

Con la casilla **«Publicar automáticamente»** marcada y el token puesto en cada equipo:

1. **Antes de guardar** una factura, el equipo trae de GitHub las facturas de los demás.
2. **Al guardar**, une su registro con el de GitHub (nunca lo sobrescribe) y lo publica. Si otro equipo publica justo al mismo tiempo, reintenta solo con una espera aleatoria (hasta 12 veces).
3. La pestaña **Registro de facturas** se actualiza sola cada minuto mientras está abierta, y tiene el botón **Actualizar**.

**Existencias:** el stock del inventario no se modifica al facturar. Lo disponible se calcula como *stock base − ventas del registro*, así que todos los equipos ven lo mismo en cuanto tienen el mismo registro. Anular una factura devuelve sus productos. Cada vez que cargas un Excel o editas un stock se crea una base nueva y las ventas anteriores dejan de descontar. Un equipo no puede impedir que dos cajas vendan la última unidad en el mismo instante; en ese caso el producto queda en «Agotado».

Un equipo **sin token** guarda la factura solo en su navegador y avisa. Cuando le pongas el token, usa «Publicar facturas» y se une con las demás. Los equipos sin token ven el registro publicado en el sitio, que GitHub Pages tarda 1–2 minutos en actualizar.

**Límite de GitHub:** cada factura es una escritura en el repositorio, y GitHub limita las escrituras (según su documentación, unas 80 por minuto y 500 por hora). Con 15 cajas facturando a ritmo normal sobra margen; una ráfaga sostenida de cientos de facturas por hora podría toparse con ese límite.

## Cargar inventario desde Excel

Panel → «Cargar inventario desde Excel». Detecta la hoja «inventario» y las columnas Codigo, Productos, Unidad, Stock, Precio, Unidad de Negocio, Lineas, Modulo, estatus e Impuesto. Puedes reemplazar todo o actualizar por código. Después usa «Publicar inventario» para subirlo.

## Cambiar la contraseña del panel

En la consola del navegador:

```js
crypto.subtle.digest('SHA-256', new TextEncoder().encode('NuevaClave')).then(b => console.log([...new Uint8Array(b)].map(x => x.toString(16).padStart(2,'0')).join('')))
```

Copia el resultado en `PASSWORD_HASH` dentro de `index.html`. Es una barrera básica: el código de una página estática es visible.

## Pantallas

El diseño se adapta de 320 px a pantallas grandes: teléfonos (catálogo y registro en tarjetas, botones y campos grandes para el dedo, botón «Ver factura» fijo abajo), tabletas (panel administrativo arriba en dos columnas) y escritorio (catálogo con panel a la derecha). Respeta las zonas seguras de los teléfonos con muesca.
