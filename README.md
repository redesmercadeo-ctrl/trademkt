[README.md](https://github.com/user-attachments/files/33840597/README.md)
# Trade Marketing — Control de solicitudes (MVP: Material POP)

App web de una sola página para gestionar solicitudes de Material POP: desde que el vendedor la pide hasta que Mercadeo la cierra con evidencia y ROI.

## Contenido

| Archivo | Descripción |
|---|---|
| `index.html` | La app completa (HTML + JS en un solo archivo). |
| `README.md` | Este documento. |

## Cómo abrirla

1. Descarga `index.html`.
2. Ábrelo con doble clic en tu navegador (Chrome, Edge, Safari o Firefox).

Necesita conexión a internet: carga React, Tailwind CSS, los iconos y las fuentes desde CDN.

### Importante: persistencia de datos

Fuera de Claude, la app corre en **modo local**: funciona completa, pero los datos viven solo en la memoria de esa pestaña. Al recargar, vuelve a los 3 datos de ejemplo, y nadie más ve tus cambios.

La versión publicada en Claude sí guarda y comparte los datos entre usuarios (usa el almacenamiento propio de la plataforma). Para tener datos compartidos fuera de Claude hace falta conectar una base de datos real (ver "Siguientes pasos").

## Usuarios de demo

La pantalla de login permite entrar como cualquiera de estos usuarios (no hay contraseñas, es una simulación para probar roles):

- **Vendedor** (uno por región): crea solicitudes y sube la foto de evidencia. Solo ve su región.
- **Coordinador Regional** (uno por región): aprueba, rechaza o escala las solicitudes de su región.
- **Coordinador de Mercadeo:** aprueba o rechaza las escaladas y registra el valor de compra (ROI).
- **Gerente de Mercadeo:** lo mismo que el anterior, y además ajusta presupuestos por región y el monto de escalamiento.
- **Equipo interno:** marca solicitudes como "En producción" y "Entregada".

Regiones: Oriente, Centro-Occidente, Central, Occidente y Capital.

## Flujo de una solicitud

```
Solicitada ─┬─► Aprobada ──► En producción ──► Entregada ──► Evidencia subida ──► Cerrada con evidencia
            ├─► Rechazada                                                          (ROI calculado)
            └─► En revisión central ─┬─► Aprobada (continúa igual)
                                     └─► Rechazada
```

Reglas principales:

- **Escalamiento:** si el costo supera el monto configurado (500 por defecto), el Coordinador Regional no puede aprobar; debe escalar a Mercadeo.
- **Presupuesto por región:** no se puede aprobar una solicitud que supere el presupuesto disponible. Solo el Gerente puede ajustar los totales.
- **Descuento de presupuesto:** se aplica cuando la solicitud pasa a "Entregada" (el presupuesto usado es la suma de las solicitudes entregadas o posteriores).
- **Rechazos:** el motivo es obligatorio.
- **Evidencia y ROI:** el vendedor sube una foto de la entrega; Mercadeo registra el valor de compra del cliente y el ROI se calcula como `(valor de compra − costo) / costo × 100`.
- **Historial:** cada solicitud guarda quién hizo qué y cuándo.

## Configuración por defecto

Está al inicio del código, dentro de `index.html`:

- Catálogo: Franela (8), Gorra (6), Cuaderno (3), Catálogo (4), Pliego publicitario (12).
- Presupuesto inicial: 3.000 por región.
- Monto de escalamiento: 500.

Son valores de ejemplo; ajústalos a los reales (el PRD deja pendientes el catálogo y los presupuestos).

## Publicarla en internet

Como es un solo archivo estático, sirve cualquier hosting de archivos estáticos (Netlify, Vercel, GitHub Pages, Cloudflare Pages): sube `index.html` y listo. Sin base de datos, seguirá en modo local.

## Siguientes pasos sugeridos

1. **Base de datos real** (Supabase, Firebase u otra) para compartir datos entre usuarios, reemplazando la capa de almacenamiento de la app.
2. **Autenticación real** con usuarios y contraseñas, en lugar del login simulado, y permisos por rol aplicados en el servidor.
3. **Almacenamiento de fotos** en un servicio de archivos, en lugar de guardarlas dentro del registro.
4. **Resto del PRD:** solicitudes de Exhibiciones e Inducciones, seguimiento mensual de exhibiciones y resúmenes con IA.

## Alcance actual

Incluye solo el flujo completo de **Material POP**, como validación del MVP. Exhibiciones, Inducciones y los resúmenes con IA no están implementados.
