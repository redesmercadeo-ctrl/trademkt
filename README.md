[README.md](https://github.com/user-attachments/files/33879902/README.md)
# Trade Marketing — Control de solicitudes

App web de una sola página para gestionar solicitudes de Trade Marketing: Material POP, Inducciones al piso de venta y Exhibiciones.

## Archivos

| Archivo | Descripción |
|---|---|
| `index.html` | La app completa (HTML + JS en un solo archivo). |
| `README.md` | Este documento. |

## Accesos

| Rol | Quién | Clave |
|---|---|---|
| Vendedor | Se entra por región (Oriente, Centro-Occidente, Central, Occidente, Capital) | Sin clave |
| Coordinador Regional (solo ver) | Iván Marín (Centro-Occidente), Adelis Torres (Central), Enrique González (Capital), Miguel Contreras (Occidente), Joaquín Martínez (Oriente) | 3030 |
| Coordinador de Mercadeo | Luisana Jaramillo | 2828 |
| Gerente de Mercadeo | Luisana Harraka | 2828 |
| Equipo de producción (Material POP) | Edwin Vaquero | 2929 |

Las claves están escritas dentro del código de la página, así que sirven para ordenar el acceso pero no son seguridad real: cualquiera que vea el código fuente las puede leer.

## Solicitudes

Cada solicitud pide **nombre, apellido y correo electrónico** de quien la hace, la **marca** (FERMETAL, UNIQUE o MULTIMARCAS), el **cliente**, la fecha y la categoría (Comercial o Mercadeo).

Tipos:

- **Material POP:** casillas de selección con cantidad por material, y motivo.
  - Franelas $4
  - Cuadernos $6
  - Bolsas ecológicas $2
  - Pliegos $0,40
  - Volantes línea cobra $0,20
- **Inducción al piso de venta:** cantidad de personas, descripción y costo estimado opcional.
- **Exhibición:** medidas del espacio, sugerido (texto largo) y foto del espacio. El costo no lo pone el vendedor: lo define Mercadeo al aprobarla.

### Clasificación automática de clientes

La app incluye el listado `CLASIFICACIÓN_DE_CLIENTES_POR_REGIÓN.xlsx` (1.080 clientes en las 5 regiones). El vendedor escribe el nombre del cliente de su región, elige el resultado de la lista y la app asigna sola la clasificación (Baja contribución, En crecimiento, Estratégico o VIP) y muestra su compra promedio mensual. La clasificación queda guardada en la solicitud.

Reglas:

- Si la compra promedio mensual del cliente es de $0 a $100 (incluye promedios negativos), **no aplica ninguna solicitud** y no se puede enviar.
- Si el cliente es **Baja contribución** y se pide una **Exhibición**, aparece la alerta "Cliente no cumple con criterio para exhibición" y no se puede enviar esa solicitud.
- Si el nombre no está en el listado de la región, no se puede continuar.

Para actualizar la clasificación hay que reemplazar los datos de clientes dentro de la app (no se leen del Excel en vivo).

## Flujo

```
Solicitada ─┬─► Aprobada ─► (según el tipo, ver abajo) ─► Evidencia subida ─► Cerrada con evidencia
            └─► Rechazada
```

**Aprobación (solo Mercadeo)**

- Aprueban y rechazan Luisana Jaramillo y Luisana Harraka, con botones Aprobar y Rechazar. El rechazo exige motivo.
- Los coordinadores regionales solo ven las solicitudes de su región.
- Una campana junto al nombre de usuario de Mercadeo muestra cuántas solicitudes están por aprobar. Suma cada solicitud nueva y baja al aprobar o rechazar.

**Presupuesto**

- **Todo se descuenta al aprobar**, sea Material POP, Inducción o Exhibición.
- En las exhibiciones, Mercadeo escribe el monto del costo al aprobar y se descuenta de inmediato. Si el monto supera lo disponible, no se puede aprobar; solo el Gerente ajusta presupuestos.
- Los coordinadores regionales ven cuánto les queda disponible de su presupuesto, que se actualiza con cada aprobación.

**Material POP — lo actualiza Producción**

```
Aprobada ─► En preparación ─► Entregado a transporte ─► Entregado a vendedor
```

Cuando Mercadeo aprueba un Material POP, le llega a Producción (Edwin Vaquero) una notificación: una campana con el número de solicitudes nuevas por preparar. Producción avanza el estatus paso a paso.

**Exhibiciones — lo actualiza Mercadeo** (en su propia lista, separada de las inducciones)

```
Aprobada ─► Proceso de diseño/renderizado ─► En producción ─► Exhibición entregada a transporte
```

**Inducciones al piso de venta — las aprueba y actualiza Mercadeo** (en su propia lista, separada de las exhibiciones)

```
Aprobada ─► Programada ─► Realizada
```


**Cierre:** cuando el material, la exhibición o la inducción llega a su estatus final, el vendedor puede subir la foto de evidencia y Mercadeo registra el valor de compra del cliente. El ROI se calcula como `(valor de compra − costo) / costo × 100`; si la solicitud no tiene costo, solo se registra el valor de compra.

Cada solicitud guarda su historial de cambios.

## Aviso de estatus por correo

Cuando Mercadeo aprueba, rechaza, cierra una solicitud o actualiza el estatus de una exhibición, la app muestra una ventana con el correo ya redactado para el solicitante (asunto, estatus, motivo si fue rechazada y detalle de la solicitud). También hay un enlace "Avisar por correo" en cada solicitud de la vista de Mercadeo para reenviar el estatus actual.

- **"Abrir en mi correo"** abre el programa de correo del usuario con el mensaje listo; la persona de Mercadeo lo revisa y lo envía.
- **"Copiar mensaje"** sirve si el navegador no abre el programa de correo.
- No es un envío automático: la app no envía correos por sí sola. Para eso hace falta un servidor propio (ver "Pendiente").

## Datos compartidos

Los datos se guardan y comparten solo en la versión publicada en Claude, y únicamente quienes tengan permiso de escritura sobre esa página pueden crear o modificar solicitudes (el dueño y, según el plan, los miembros de la organización con acceso). Una persona externa con enlace público solo puede ver.

Si abres `index.html` por tu cuenta, funciona en modo local: los datos viven en la pestaña y se pierden al recargar.

Para uso real con vendedores y coordinadores externos a la organización hace falta una base de datos y autenticación propias (por ejemplo Supabase o Firebase).

## Pendiente

- Envío automático de correos (requiere servidor propio, por ejemplo Supabase con un servicio de correo).
- Seguimiento mensual de exhibiciones y resúmenes con IA (según el PRD).
