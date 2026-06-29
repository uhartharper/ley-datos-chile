---
name: ley-datos-chile
description: >
  Audita un sitio web frente a los requisitos de la Ley 21.719 de Protección de
  Datos Personales (Chile). Detecta el sector del sitio automáticamente y aplica
  requisitos específicos. Produce informe de cumplimiento con score, tabla de
  estado por artículo e issues priorizados.
user-invokable: true
argument-hint: "<url> [--docx]"
license: MIT
metadata:
  author: PubliUp SEO
  version: "1.0.0"
  category: legal-compliance
---

# Auditoría Ley 21.719 — Protección de Datos Personales (Chile)

## Uso

```
/ley-datos-chile https://ejemplo.cl
/ley-datos-chile https://ejemplo.cl --docx
```

Sin `--docx` el output es Markdown interno. Con `--docx` genera informe
ejecutivo con accionables en formato Word.

Si el usuario no especifica formato, preguntar siempre antes de generar
el documento: "¿El informe es para uso interno o para entregar al cliente?"

---

## Paso 1 — Recopilación de datos del sitio

Ejecutar en paralelo:

1. **WebFetch homepage** — detectar: sector, idioma, formularios visibles,
   banner de cookies, enlace a política de privacidad, datos que se recogen.
2. **WebFetch política de privacidad** — buscar el enlace en footer, header,
   formularios de contacto, y en rutas comunes: `/privacidad`, `/politica-de-privacidad`,
   `/privacy`, `/legal/privacidad`. Si no se encuentra, registrar como issue crítico.
3. **WebFetch página de contacto / formulario principal** — detectar campos
   solicitados, checkboxes de consentimiento, texto legal adjunto.
4. **Headers HTTP** — verificar HTTPS, HSTS, CSP, X-Frame-Options, Referrer-Policy.

No construir URLs. Usar solo las encontradas en el sitio.

---

## Paso 2 — Detección de sector

Analizar el contenido del homepage para clasificar el sitio en uno de estos sectores.
Un sitio puede pertenecer a más de uno.

| Sector | Señales de detección |
|--------|---------------------|
| **Salud / médico** | términos de salud, síntomas, diagnóstico, clínica, hospital, farmacia, ficha médica |
| **Ecommerce** | carrito de compras, productos con precio, checkout, SKUs, stock |
| **Fintech / financiero** | crédito, préstamo, inversión, cuenta, pago, CMF, Banco Central |
| **Educación** | cursos, alumnos, matrícula, colegio, universidad, menores de edad |
| **SaaS / tecnología** | software, API, suscripción, plan, dashboard, integración |
| **Medios / publicidad** | noticias, artículos, publicidad programática, newsletter |
| **Servicios profesionales** | consultoría, abogado, contador, RRHH, reclutamiento |
| **General / sin sector específico** | fallback si no hay señales claras |

Registrar sector(es) detectado(s). Aplicar requisitos adicionales del sector
en los bloques 7, 9 y 10 según corresponda.

---

## Paso 3 — Auditoría por bloques

Evaluar cada bloque. Asignar uno de cuatro estados:

- **Cumple** — verificado en el sitio live
- **Parcial** — presente pero incompleto
- **No cumple** — ausente o incorrecto
- **No evaluable** — requiere acceso al backend o no es visible externamente

### Bloque 1 — Identificación del responsable del tratamiento (Art. 5)
Peso: 10 pts

Verificar que en la política de privacidad aparezca:
- [ ] Nombre o razón social del responsable
- [ ] RUT o identificación legal
- [ ] Domicilio en Chile (o representante en Chile si es entidad extranjera)
- [ ] Dirección de correo o medio de contacto

Parcial si aparece el nombre pero faltan RUT o domicilio.

### Bloque 2 — Política de privacidad: existencia y acceso (Art. 14)
Peso: 5 pts

- [ ] Existe una política de privacidad publicada
- [ ] Enlace visible en el footer de todas las páginas
- [ ] Enlace accesible desde formularios que recogen datos
- [ ] Redactada en español (o idioma del sitio)
- [ ] Fecha de última actualización visible

### Bloque 3 — Política de privacidad: contenido mínimo (Art. 14 a–k)
Peso: 15 pts

Verificar que la política incluya explícitamente:
- [ ] **a)** Finalidad(es) del tratamiento — para qué se usan los datos
- [ ] **b)** Base legal de cada finalidad (consentimiento / interés legítimo / contrato / obligación legal)
- [ ] **c)** Tipos de datos que se recogen
- [ ] **d)** Destinatarios o categorías de destinatarios (terceros, proveedores, filiales)
- [ ] **e)** Plazos de conservación o criterios para determinarlos
- [ ] **f)** Derechos del titular: acceso, rectificación, cancelación, oposición, portabilidad, limitación
- [ ] **g)** Procedimiento para ejercer derechos y plazo de respuesta (15 días hábiles)
- [ ] **h)** Transferencias internacionales (si aplica) y garantías
- [ ] **i)** Uso de cookies y tecnologías de rastreo
- [ ] **j)** Contacto del Encargado de Protección de Datos (si aplica)
- [ ] **k)** Derecho a reclamar ante el CPLT (Consejo para la Transparencia)

Puntuación parcial: 1 pt por ítem presente, máx. 15.

### Bloque 4 — Base legal del tratamiento declarada (Art. 12)
Peso: 10 pts

- [ ] Se indica explícitamente la base legal de cada finalidad
- [ ] No se usa "consentimiento" como única base cuando el tratamiento es necesario para el contrato
- [ ] El consentimiento declarado es previo, informado, libre, específico y unívoco
- [ ] No se usan casillas pre-marcadas como mecanismo de consentimiento
- [ ] Se distingue entre finalidades obligatorias y opcionales

### Bloque 5 — Consentimiento y cookies (Art. 12 + 17)
Peso: 15 pts

**Banner de cookies:**
- [ ] Existe banner o mecanismo de consentimiento de cookies
- [ ] El banner permite aceptar o rechazar antes de que se carguen cookies no esenciales
- [ ] No hay cookies de análisis, publicidad o rastreo activas antes del consentimiento
- [ ] Existe opción de configuración granular (analítica / marketing / funcional)
- [ ] El rechazo es tan sencillo como la aceptación (mismo nivel de acceso)

**Formularios:**
- [ ] Los formularios con tratamiento de datos incluyen checkbox de consentimiento no pre-marcado
- [ ] El checkbox vincula a la política de privacidad
- [ ] Se indica la finalidad concreta del formulario antes de enviar

Verificar que no haya cookies de terceros activas antes del consentimiento inspeccionando
el código fuente del homepage. Si el banner no existe o es solo informativo (sin botón de rechazo),
reportar como No cumple.

### Bloque 6 — Derechos ARCOP+L: canal habilitado (Art. 4–11)
Peso: 15 pts

Los derechos bajo Ley 21.719 son: Acceso, Rectificación, Cancelación/Supresión,
Oposición, Portabilidad y Limitación del tratamiento.

- [ ] Existe un mecanismo explícito para ejercer derechos (email, formulario, dirección postal)
- [ ] Se menciona el plazo de respuesta (15 días hábiles desde Art. 11)
- [ ] Se mencionan los seis derechos, incluidos Portabilidad y Limitación (nuevos en Ley 21.719)
- [ ] Se indica qué información debe adjuntar el titular al hacer la solicitud
- [ ] Se menciona el derecho a reclamar ante el CPLT si la solicitud no es atendida

### Bloque 7 — Datos sensibles (Art. 2 + 16)
Peso: 10 pts

Primero determinar si el sitio recoge datos sensibles. Señales:
formularios de salud, datos étnicos, orientación sexual, biometría, afiliación política
o sindical, creencias religiosas, datos de menores.

**Si el sitio no recoge datos sensibles:** bloque N/A, asignar 10 pts automáticamente.

**Si recoge datos sensibles:**
- [ ] Se identifican explícitamente como datos sensibles en la política
- [ ] Se solicita consentimiento explícito y diferenciado (no agrupado con otros datos)
- [ ] Se indica finalidad específica para esos datos
- [ ] Se implementan medidas de seguridad reforzadas (mencionadas en la política)

**Requisitos adicionales por sector:**

*Salud/médico:*
- [ ] Datos de salud tratados solo con consentimiento del paciente o autorización legal
- [ ] No se comparten datos clínicos con terceros sin consentimiento específico
- [ ] Si hay ficha clínica electrónica, se indica el marco legal aplicable (Ley 20.584 superpuesta)

*Educación (menores):*
- [ ] Datos de menores de 14 años requieren consentimiento del padre/madre/tutor
- [ ] Se identifica explícitamente si el servicio está dirigido a menores
- [ ] No se usa la dirección como dato requerido para menores

*Ecommerce:*
- [ ] Datos de pago no se almacenan localmente si se usa pasarela de terceros (indicarlo)
- [ ] Historial de compras declarado como dato personal con plazo de retención

*Fintech/financiero:*
- [ ] Se menciona la regulación CMF superpuesta si aplica
- [ ] Datos financieros tratados como categoría especial con garantías adicionales

### Bloque 8 — Transferencias internacionales (Art. 26)
Peso: 10 pts

Detectar transferencias probables: CDNs, plataformas de email marketing, CRMs,
herramientas de análisis, servicios en la nube con sede fuera de Chile.

- [ ] La política declara si se realizan transferencias internacionales
- [ ] Se identifican los países o regiones de destino
- [ ] Se indican las garantías adoptadas: contrato de encargo con cláusulas estándar,
     país con nivel de protección adecuado reconocido, o consentimiento explícito del titular
- [ ] Si no se realizan transferencias internacionales, se indica explícitamente

### Bloque 9 — Seguridad técnica (Art. 19)
Peso: 5 pts

- [ ] HTTPS activo en todas las páginas (sin mixed content)
- [ ] Certificado SSL válido y no expirado
- [ ] HSTS habilitado (Strict-Transport-Security en headers)
- [ ] No hay redirección de HTTP a HTTPS rota
- [ ] La política menciona las medidas de seguridad implementadas (aunque sea de forma general)

**Sector salud / fintech:** verificar también:
- [ ] CSP (Content-Security-Policy) configurado
- [ ] X-Frame-Options o frame-ancestors configurado

### Bloque 10 — Encargado de Protección de Datos / EPD (Art. 36)
Peso: 5 pts

El EPD es obligatorio para: organismos públicos, responsables que traten datos
a gran escala, tratamientos de categorías especiales a gran escala, y monitoreo
sistemático de personas.

- [ ] La política indica si aplica o no un EPD al responsable
- [ ] Si aplica: nombre o rol del EPD publicado
- [ ] Si aplica: dirección de contacto del EPD publicada
- [ ] Si no aplica: se indica el motivo o simplemente que no se requiere

Si el sitio es pequeño o PYME sin tratamiento masivo ni datos sensibles,
indicar "No aplica EPD — PYME sin tratamiento de datos a gran escala" y asignar pts completos.

---

## Paso 4 — Score global

```
Score = suma de puntos obtenidos en cada bloque / 100
```

| Rango | Interpretación |
|-------|---------------|
| 80–100 | Postura defensible — cumplimiento sustancial |
| 60–79 | Riesgo moderado — gaps importantes sin issues críticos |
| 40–59 | Riesgo alto — múltiples incumplimientos relevantes |
| 0–39 | Riesgo crítico — ausencia de elementos esenciales |

Los bloques con estado "No evaluable" no penalizan ni suman.
Los bloques N/A (datos sensibles cuando no aplica) suman pts completos.

---

## Paso 5 — Clasificación de issues

**Crítico** (bloquea cumplimiento mínimo):
- Política de privacidad inexistente
- Sin mecanismo de ejercicio de derechos
- Cookies no esenciales activas sin consentimiento
- Datos sensibles sin consentimiento explícito diferenciado
- Sin HTTPS

**Medio** (gap relevante, plazo recomendado de corrección: 30–60 días):
- Política existe pero falta base legal declarada
- Derechos mencionados pero sin canal ni plazo de respuesta
- Transferencias internacionales no declaradas
- Banner de cookies sin opción de rechazo equivalente al aceptar

**Bajo** (mejora de postura, no urgente):
- Falta fecha de actualización en la política
- EPD no mencionado en sitios donde no aplica
- Política sin enlace desde formularios secundarios
- Plazos de retención no especificados para algún tipo de dato

---

## Paso 6 — Output según modo

### Modo interno (Markdown, sin `--docx`)

```markdown
# Auditoría Ley 21.719 — [dominio]
Fecha: [fecha]  |  Sector: [sector detectado]  |  Score: [X/100]

## Resumen
[2–3 líneas: postura general, issues críticos detectados]

## Tabla de cumplimiento
| Bloque | Peso | Pts obtenidos | Estado | Evidencia |
|--------|------|--------------|--------|-----------|
| 1. Identificación responsable | 10 | X | Cumple/Parcial/No cumple | ... |
...

## Issues críticos

**[Nombre del issue]** (Bloque X — Art. Y)
Evidencia: [qué se encontró o qué falta]
→ Acción: [qué hay que hacer concretamente]

## Issues medios

**[Nombre del issue]** (Bloque X — Art. Y)
Evidencia: [qué se encontró o qué falta]
→ Acción: [qué hay que hacer]
→ Plazo sugerido: [30 / 60 días]

## Issues bajos

**[Nombre del issue]** (Bloque X — Art. Y)
→ Acción: [qué hay que hacer]

## Notas metodológicas
- Qué se verificó externamente
- Qué requiere acceso al backend para confirmar
```

Guardar en `C:/Users/cmano/claude-seo/[cliente o dominio]/auditoria_ley21719_[fecha].md`.

### Modo informe cliente (`--docx`)

Preguntar primero: "¿Quieres el informe en lenguaje técnico o ejecutivo simplificado?"

Estructura del .docx:
1. Portada: dominio, fecha, score global, sector
2. Resumen ejecutivo (máx. 1 página, sin jerga legal)
3. Tabla de cumplimiento con semáforo visual (Cumple / Parcial / No cumple)
4. Issues críticos con acción concreta requerida y artículo infringido
5. Issues medios con recomendación y plazo sugerido
6. Issues bajos como listado
7. Próximos pasos priorizados (top 5)
8. Nota metodológica: qué se auditó externamente y qué está fuera de scope

Guardar en `C:/Users/cmano/claude-seo/[cliente o dominio]/auditoria_ley21719_[fecha].docx`.

### Comparativa RGPD (solo si el usuario lo solicita explícitamente)

Añadir columna "Equivalente RGPD" en la tabla de cumplimiento y sección de gaps
entre Ley 21.719 y RGPD relevantes para el sitio auditado.
No incluir por defecto.

---

## Integración con el stack SEO y anonimización

### Cuándo se invoca esta skill automáticamente

`/seo audit <url>` la lanza como subagente condicional cuando detecta sitio chileno.
Señales de detección: TLD `.cl`, RUT/SII/CMF/SERNAC en el contenido, moneda CLP.
El score de cumplimiento Ley 21.719 se integra en el reporte unificado del audit.

### Flujos de trabajo combinados

**Auditoría completa de sitio chileno:**
```
/seo audit <url>           → lanza ley-datos-chile automáticamente
/ley-datos-chile <url>     → auditoría standalone si solo se necesita cumplimiento legal
```

**Post-auditoría recomendada (según hallazgos):**

| Hallazgo en ley-datos-chile | Skill a invocar después |
|-----------------------------|------------------------|
| Schema de política de privacidad ausente o mejorable | `/seo schema <url>` |
| Headers de seguridad insuficientes (Bloque 9) | `/seo technical <url>` — sección Security |
| Cookie consent sin configurar correctamente | `/seo technical <url>` — sección JS Rendering |
| Sitio con datos sensibles de salud | `/seo local <url>` si es clínica/consultorio — detecta señales E-E-A-T médico |
| Transferencias a CDN/plataformas internacionales no declaradas | `/seo geo <url>` — identifica terceros con rastreadores activos |

**Antes de compartir el reporte:**
```
# Anonimizar reporte si contiene PII del cliente auditado
/anonimizar C:/Users/cmano/claude-seo/[cliente]/auditoria_ley21719_[fecha].md --ley chile
/anonimizar C:/Users/cmano/claude-seo/[cliente]/auditoria_ley21719_[fecha].docx --ley chile
```

Usar `--ley chile` (no `--ley todo`) para evitar falsos positivos en números de artículos legales.

### Notas sobre skills de terceros

`seo-audit` y `seo` son de AgriciDaniel/claude-seo v2.2.0 — se sobreescriben al actualizar.
Tras cada actualización de claude-seo, re-aplicar:
- Línea en `seo-audit`: spawn condicional de `ley-datos-chile` para sitios `.cl`
- Línea en `seo` quick reference: `/ley-datos-chile <url>`
- Línea en `seo` orchestration: paso `10b` de detección de sitio chileno

---

## Notas de alcance

Esta auditoría cubre lo verificable externamente sin acceso al backend:
- Documentos públicos del sitio
- Comportamiento observable de cookies y formularios
- Headers HTTP

Quedan fuera de scope sin acceso interno:
- Contratos con encargados del tratamiento (Art. 24)
- Registros de actividades de tratamiento (Art. 15)
- Procedimientos internos de respuesta a solicitudes de derechos
- Medidas de seguridad técnicas internas (cifrado en reposo, control de accesos)
- Notificaciones de brechas al CPLT (Art. 42)

Siempre indicar este alcance en la sección de notas metodológicas del informe.

---

## Actualización normativa

Ley 21.719 publicada el 13 de diciembre de 2024. Período de adecuación de 24 meses
para la mayoría de obligaciones (plazo límite: diciembre 2026). Reglamento aún en
elaboración a la fecha de esta versión del skill — algunos requisitos de implementación
pueden refinarse cuando se publique. Señalar esta contingencia en el informe si afecta
a algún bloque auditado.
