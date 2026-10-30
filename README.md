# Eventos NMS — catálogo, facturación e inventario

Sitio estático (funciona en GitHub Pages o abriendo `index.html` desde un servidor). Archivos:

- `index.html`: catálogo, panel administrativo, factura y registro de facturas.
- `styles.css`: identidad visual de NMS.
- `precios.json`: inventario inicial (generado desde la hoja «inventario» de INVENTARIO_GENERAL.xlsx, sin productos inactivos ni costos).
- `logo-nms.png` y `logo-nms-blanco.png`: logo del camión.

## Uso diario

1. **Buscar**: el buscador acepta código, nombre o línea de negocio (puedes combinar palabras, p. ej. «nestle bebidas»). El selector filtra por línea de negocio.
2. **Facturar**: indica la cantidad y pulsa «Agregar». La factura está a la derecha (en el teléfono, debajo del catálogo, con el botón «Ver factura»).
3. **Guardar factura**: pide confirmación («¿Está seguro?»), guarda la factura en el registro y descuenta las cantidades del inventario. No deja guardar si algún producto supera el stock.
4. **Registro de facturas** (al final de la página): vista «Facturas» con el desglose de cada una, y vista «Desglose por producto» con lo facturado acumulado por producto. «Descargar .xlsx» exporta las tres hojas (Facturas, Detalle, Desglose por producto).

## Panel administrativo

Está en la columna derecha, encima de la factura, y se oculta o muestra con su barra.
Contraseña por defecto: **NMS2026**.

- **Cargar inventario desde Excel**: elige el archivo; el sistema detecta la hoja «inventario» y sus columnas (Codigo, Productos, Unidad, Stock, Precio, Unidad de Negocio, Lineas, Modulo, estatus, Impuesto). Puedes reemplazar todo o actualizar por código.
- **Edición rápida**: permite cambiar precio y stock directamente en la tabla.
- **Anular facturas**: con la sesión abierta aparece «Anular» en el registro; la factura queda marcada y su mercancía vuelve al inventario.
- **Respaldos**: descarga inventario y registro en Excel.
- **Publicar en GitHub (opcional)**: actualiza `precios.json` del repositorio con un token de acceso (permiso Contents: read/write).

## Dónde se guardan los datos

El inventario cargado y las facturas se guardan en el navegador (localStorage) del equipo donde se usa. Otro equipo o navegador no ve esas facturas. Descarga el registro en Excel con frecuencia como respaldo.

## Cambiar la contraseña

En la consola del navegador:

```js
crypto.subtle.digest('SHA-256', new TextEncoder().encode('NuevaClave')).then(b => console.log([...new Uint8Array(b)].map(x => x.toString(16).padStart(2,'0')).join('')))
```

Copia el resultado en `PASSWORD_HASH` dentro de `index.html`. Es una barrera básica: el código de una página estática es visible.
