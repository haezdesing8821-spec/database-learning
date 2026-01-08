# **PROCESO DE NORMALIZACIÓN Y MODELADO DE LA BASE DE DATOS**

## **METODOLOGÍA DE TRABAJO PARA NUESTRO SISTEMA: CONTINUACIÓN**

Trabajaremos exactamente en este orden, sin saltarnos nada:

🅰️ BLOQUE EMPLEADOS

(primer bloque – el más complejo)

Incluye:

    datos_empleado

    empleado

    direccion_empleado

    historial_laboral

    jerarquía administrativo / directivo

    relación reflexiva empleado–empleado

**👉 Aquí se definen:**

    jerarquías

    reflexividad

    especialización

    muchas reglas de negocio transversales

🅱️ BLOQUE COMERCIAL

(segundo bloque)

Incluye:

    cliente

    orden

    detalle_orden

    venta

    pago

**👉 Aquí se modelan:**

    procesos

    estados

    dependencias temporales

    reglas financieras

🅲️ BLOQUE PRODUCTOS / SUMINISTRO

(tercer bloque)

Incluye:

    producto

    categoría

    subministrador

    transportista

**👉 Aquí se consolidan:**

    catálogos

    relaciones de abastecimiento

    dependencias externas

🧠 Qué garantiza este enfoque

Con este esquema que acabas de definir:

    ❌ No hay normalización “automática”

    ❌ No hay decisiones sin justificar

    ❌ No hay reglas escondidas

    ✔ Todo es explícito
    ✔ Todo es defendible
    ✔ Todo puede convertirse luego a SQL sin rediseñar

**Este es exactamente el enfoque de:**

    diseño académico serio

    arquitectura de datos real

    entrevistas técnicas de nivel medio–alto

**TRABAJAREMOS:**

Identificación y clasificación de entidades

(incluyendo tipo: `datos` / `catálogo` / `pivote`, y `tipo fuerte` / `débil`)

# **🅰️ BLOQUE EMPLEADOS**

### PASO 1 – Identificación, validación y clasificación de entidades

**📌 Entidades involucradas en el bloque EMPLEADOS**

    datos_empleado

    empleado

    direccion_empleado

    historial_laboral

No se analiza ninguna otra entidad en este paso.

1️⃣ datos_empleado

🔹 Tipo de entidad

    Entidad de datos

    Entidad fuerte

🔹 Justificación

**Contiene información intrínseca e identificatoria del empleado como persona:**

    nombre

    apellido

    fecha_nacimiento

    doc_identidad

    emails_empleados

    foto

**Tiene:**

    Clave primaria propia (empleado_ID)

    Existencia independiente desde el punto de vista conceptual (la persona existe aunque cambie su empleo)

🔹 Reglas aplicadas

    Regla MER:

    Una entidad fuerte posee identificador propio y no depende de otra entidad para existir.

🔹 Problema que se corrige

Evita:

    Mezclar datos personales con datos contractuales

    Redundancia de información si el empleado cambia de puesto o contrato

🔹 Estructura resultante

    Entidad separada y estable que no necesita reestructuración en este paso.

2️⃣ empleado

🔹 Tipo de entidad

    Entidad de datos

    Entidad fuerte

🔹 Justificación

**Representa la relación laboral entre la persona y la empresa:**

    fecha_ingreso

    puesto

    departamento

    notas

**Aunque comparte empleado_ID con datos_empleado, no es la misma entidad conceptual:**

    Una es la persona

    La otra es el vínculo laboral

🔹 Reglas aplicadas

**Regla de normalización (`3FN`):**

Los atributos deben depender únicamente de la clave y del hecho que representa la entidad.

🔹 Problema que se corrige

Evita:

    Actualizaciones inconsistentes

    Repetición de datos personales en registros laborales

🔹 Estructura resultante

    Entidad correcta, bien separada, preparada para:

    historial

    jerarquías

    especialización

3️⃣ direccion_empleado

🔹 Tipo de entidad

    Entidad de datos

        Entidad débil

🔹 Justificación

    No tiene sentido sin un empleado asociado:

    depende de empleado_ID

representa un atributo multivaluado normalizado

🔹 Reglas aplicadas

Regla de normalización (1FN):

    No se permiten atributos multivaluados ni repetidos en una misma entidad.

🔹 Problema que se corrige

Evita:

    Campos repetidos de dirección

    Violación de la Primera Forma Normal

🔹 Estructura resultante

    Entidad dependiente correctamente separada.

4️⃣ historial_laboral

🔹 Tipo de entidad

    Entidad de datos

    Entidad débil

🔹 Justificación

    Registra hechos históricos:

    fechas de inicio y fin

    dependencia directa del empleado

No puede existir sin un empleado.

🔹 Reglas aplicadas

**Regla de normalización (`3FN`):**

Los datos históricos no deben sobrescribir datos actuales.

🔹 Problema que se corrige

Evita:

    Pérdida de trazabilidad

    Inconsistencia temporal

    Sobrescritura de información laboral

🔹 Estructura resultante

Entidad histórica correcta y bien aislada.

| Entidad            | Tipo de entidad | Fuerte / Débil | Función principal  |
| ------------------ | --------------- | -------------- | ------------------ |
| datos_empleado     | Datos           | Fuerte         | Identidad personal |
| empleado           | Datos           | Fuerte         | Relación laboral   |
| direccion_empleado | Datos           | Débil          | Direcciones        |
| historial_laboral  | Datos           | Débil          | Historial          |

## **🅰️ BLOQUE EMPLEADOS**

**Complemento previo al PASO 2**
1️⃣ Recomendación de reglas de negocio coherentes

(compatibles con lo que ya diseñaste y sin contradecir tu archivo)

⚠️ Importante:
Todas las reglas que siguen son reglas propuestas.
No sustituyen a las tuyas y requieren tu validación.

🔹 A. Reglas para datos_empleado
📌 Regla propuesta 1

Un empleado debe tener un único documento de identidad válido en el sistema.

Regla aplicada: unicidad lógica del identificador natural

Problema que corrige: duplicación de personas con diferentes IDs técnicos

Estructura resultante:

doc_identidad como UQ obligatorio

Refuerza la identidad real del empleado

📌 Regla propuesta 2

Un correo electrónico no puede estar asociado a más de un empleado.

Regla aplicada: unicidad funcional

Problema que corrige: ambigüedad en autenticación, comunicación o auditoría

Estructura resultante:

emails_empleados tratado como atributo multivaluado normalizado

Cada email con restricción UQ

🔹 B. Reglas para empleado
📌 Regla propuesta 3

Todo empleado debe tener una fecha de ingreso válida y no futura.

Regla aplicada: integridad temporal

Problema que corrige: registros laborales inconsistentes

Estructura resultante:

fecha_ingreso obligatoria

Regla de validación temporal

📌 Regla propuesta 4

Un empleado solo puede pertenecer a un departamento activo a la vez.

Regla aplicada: dependencia funcional

Problema que corrige: asignaciones simultáneas ambiguas

Estructura resultante:

departamento dependiente únicamente del vínculo laboral actual

🔹 C. Reglas para direccion_empleado
📌 Regla propuesta 5

Un empleado puede tener una o varias direcciones registradas, pero solo una activa.

Regla aplicada: cardinalidad controlada

Problema que corrige: ambigüedad operativa (envíos, contratos)

Estructura resultante:

Relación 1:N

Posible atributo de estado (a validar más adelante)

🔹 D. Reglas para historial_laboral
📌 Regla propuesta 6

Un empleado no puede tener períodos laborales solapados.

Regla aplicada: integridad temporal

Problema que corrige: incoherencia histórica

Estructura resultante:

Validación fecha_fin > fecha_inicio

No superposición entre registros del mismo empleado

📌 Regla propuesta 7

Todo cambio de puesto o departamento debe generar un nuevo registro en el historial.

Regla aplicada: trazabilidad

Problema que corrige: pérdida de información histórica

Estructura resultante:

empleado refleja estado actual

historial_laboral conserva estados pasados

2️⃣ Identificación de la entidad con referencia reflexiva
🔍 Entidad que cumple referencia reflexiva

👉 empleado

🔹 Justificación formal

Del enunciado original:

“Un empleado puede tener control sobre uno o varios empleados.”

Esto implica:

La entidad se relaciona consigo misma

Existe una jerarquía interna (superior ↔ subordinado)

No interviene ninguna otra entidad

**🔹 Tipo de relación reflexiva🔹 Tipo de relación reflexiva**

| Característica | Valor                    |
| -------------- | ------------------------ |
| Tipo           | Relación reflexiva       |
| Cardinalidad   | 1 : N                    |
| Participación  | Parcial                  |
| Rol            | Supervisor / Subordinado |

🔹 **Regla aplicada**

Regla MER – Relaciones reflexivas:

Una entidad puede participar más de una vez en una misma relación, asumiendo roles distintos.

🔹 **Problema que corrige**

Permite modelar:

Estructuras jerárquicas reales

Líneas de mando

Control organizacional

Sin duplicar entidades ni crear inconsistencias.

🔹 **Estructura conceptual resultante**

empleado mantiene una FK hacia sí misma

Ejemplo conceptual (no SQL aún):

empleado_supervisor_ID → empleado_ID

(La implementación física se verá más adelante, no ahora)

🧠 Conclusión antes de avanzar

    ✔ Reglas de negocio coherentes y compatibles con tu diseño
    ✔ Referencia reflexiva correctamente identificada
    ✔ No se agregó ninguna entidad nueva
    ✔ No se violó ninguna regla previa

🔹 **Regla aplicada**

Regla MER – Relaciones reflexivas:

Una entidad puede participar más de una vez en una misma relación, asumiendo roles distintos.

🔹 **Problema que corrige**

Permite modelar:

Estructuras jerárquicas reales

Líneas de mando

Control organizacional

Sin duplicar entidades ni crear inconsistencias.

🔹 **Estructura conceptual resultante**

empleado mantiene una FK hacia sí misma

Ejemplo conceptual (no SQL aún):

empleado_supervisor_ID → empleado_ID

(La implementación física se verá más adelante, no ahora)

🧠 Conclusión antes de avanzar

✔ Reglas de negocio coherentes y compatibles con tu diseño
✔ Referencia reflexiva correctamente identificada
✔ No se agregó ninguna entidad nueva
✔ No se violó ninguna regla previa

### **📊 Resumen del PASO 2 – Bloque Empleados**

| Entidad            | PK           | UQ            | Multivaluados    | Observaciones             |
| ------------------ | ------------ | ------------- | ---------------- | ------------------------- |
| datos_empleado     | empleado_ID  | doc_identidad | emails_empleados | emails se normalizan      |
| empleado           | empleado_ID  | —             | —                | puesto/depto candidatos   |
| direccion_empleado | direccion_ID | —             | —                | atributos bien atomizados |
| historial_laboral  | historial_ID | —             | —                | control temporal          |

🧠 Conclusión técnica del PASO 2

✔ Se detectaron correctamente atributos únicos
✔ Se identificaron multivaluados sin eliminarlos
✔ No se forzó normalización innecesaria
✔ El modelo sigue siendo fiel a tu diseño original

### **🅰️ BLOQUE EMPLEADOS**

PASO 3 – Relaciones, cardinalidades, clasificación de entidades, jerarquías

(+ recomendación final de reglas de negocio)

📌 Alcance del paso
Aquí no tocamos atributos nuevos.
Analizamos cómo se relacionan las entidades, qué tipo de entidades son y qué reglas gobiernan esas relaciones.

Como siempre, para cada decisión:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

1️⃣ Clasificación formal de las entidades del bloque
📊 Clasificación

| Entidad            | Clasificación                    | Justificación                             |
| ------------------ | -------------------------------- | ----------------------------------------- |
| datos_empleado     | **Entidad de datos**             | Registra información primaria de personas |
| empleado           | **Entidad de datos**             | Representa el vínculo laboral             |
| direccion_empleado | **Entidad de datos dependiente** | Datos operativos asociados al empleado    |
| historial_laboral  | **Entidad de datos histórica**   | Registra eventos pasados                  |


📌 Regla aplicada
Las entidades de este bloque no son catálogos ni pivotes, porque:

Cambian frecuentemente

Representan hechos del negocio

Tienen alta variabilidad

📌 Problema que se corrige
Evita tratar datos operativos como catálogos estáticos.

📌 Estructura resultante
Bloque homogéneo de entidades de datos, bien definido.

2️⃣ Relaciones entre entidades (no reflexivas)
🔹 Relación: datos_empleado ↔ empleado

Tipo: 1 : 1

Participación: total en ambos lados

📌 Regla aplicada
Separación conceptual persona ↔ vínculo laboral

📌 Problema que se corrige
Evita:

Duplicar datos personales

Mezclar identidad con contrato

📌 Estructura resultante

PK compartida (empleado_ID)

Relación uno a uno estricta

🔹 Relación: empleado ↔ direccion_empleado

Tipo: 1 : N

Participación:

empleado: parcial

direccion_empleado: total

📌 Regla aplicada
Normalización de atributos multivaluados

📌 Problema que se corrige
Evita múltiples direcciones en una sola fila

📌 Estructura resultante
Entidad dependiente correctamente asociada

🔹 Relación: empleado ↔ historial_laboral

Tipo: 1 : N

Participación:

empleado: parcial

historial_laboral: total

📌 Regla aplicada
Separación de estado actual vs histórico

📌 Problema que se corrige
Pérdida de trazabilidad laboral

📌 Estructura resultante
Historial independiente, consistente y auditable

3️⃣ Relación reflexiva (jerarquía interna)
🔹 Relación: empleado ↔ empleado

📌 Tipo: Relación reflexiva
📌 Cardinalidad: 1 : N
📌 Roles:

Supervisor

Subordinado

📌 Regla aplicada
Modelo Entidad–Relación: una entidad puede relacionarse consigo misma asumiendo roles distintos

📌 Problema que se corrige
Evita:

Tablas duplicadas para jerarquías

Estructuras artificiales

📌 Estructura resultante

FK autorreferenciada en empleado

Jerarquía organizacional real

4️⃣ Generalización / Especialización

(administrativo – directivo)

🔹 Entidad base

empleado

🔹 Subtipos

administrativo

directivo

📌 Reglas explícitas del negocio

Un administrativo puede ser directivo, pero no al revés.

📌 Clasificación de la jerarquía

| Característica | Valor           |
| -------------- | --------------- |
| Tipo           | Especialización |
| Exclusividad   | **Solapada**    |
| Cobertura      | **Parcial**     |

📌 Regla aplicada
Herencia en el MER

📌 Problema que se corrige
Evita:

Campos condicionales

Lógica confusa por roles mezclados

📌 Estructura resultante

empleado como supertipo

Subtipos con PK = FK al empleado

(la implementación física se verá más adelante)

🧠 Conclusión técnica del PASO 3

✔ Todas las relaciones están justificadas
✔ La reflexividad está bien ubicada
✔ La jerarquía está correctamente modelada
✔ No hay entidades mal clasificadas
✔ El bloque es coherente y escalable.

### **🅱️ BLOQUE COMERCIAL**

PASO 1 – Identificación, validación y clasificación de entidades

📌 Alcance del paso
En este paso NO analizamos atributos en detalle todavía.
Nos concentramos en:

Qué entidades existen

Si están correctamente definidas

Qué tipo de entidad es cada una

Qué papel juegan dentro del proceso comercial

Y, como acordamos, cada decisión incluye:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

📦 Entidades incluidas en el Bloque Comercial

(extraídas exclusivamente de tu archivo)

cliente

ordene (orden)

detalles_ordene (detalle_orden)

venta

pago

No se introduce ninguna entidad nueva en este paso.

1️⃣ cliente
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa a un actor externo real del negocio:

Empresa o persona que compra

Tiene identidad propia

Existe independientemente de órdenes, ventas o pagos

🔹 Regla aplicada

Regla MER – Entidad fuerte

Una entidad fuerte tiene clave primaria propia y existencia independiente.

🔹 Problema que se corrige

Evita:

Tratar al cliente como un simple atributo de la orden

Pérdida de trazabilidad comercial

🔹 Estructura resultante

Entidad base del bloque comercial, correctamente definida como origen del proceso.

2️⃣ ordene (orden)
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa un evento de negocio:

Iniciada por un cliente

Gestionada por un empleado

Puede o no culminar en venta

Tiene sentido propio incluso si no se factura aún.

🔹 Regla aplicada

Separación de procesos

Una orden no es necesariamente una venta.

🔹 Problema que se corrige

Evita:

Forzar ventas inexistentes

Mezclar intención de compra con facturación

🔹 Estructura resultante

Entidad transaccional independiente, correctamente separada.

3️⃣ detalles_ordene (detalle_orden)
🔹 Clasificación

Entidad pivote (asociativa)

Entidad débil

🔹 Justificación

Resuelve una relación N:M implícita:

Una orden puede tener muchos productos

Un producto puede aparecer en muchas órdenes

Además:

No tiene sentido sin una orden

No tiene sentido sin un producto

🔹 Regla aplicada

Regla MER – Entidad asociativa

🔹 Problema que se corrige

Evita:

Listas de productos dentro de la orden

Violación de la 1FN

🔹 Estructura resultante

Entidad pivote correcta, esencial para la normalización del proceso comercial.

4️⃣ venta
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa la concreción económica de una orden:

Puede existir solo si hay una orden

Registra datos fiscales y monetarios

Separa claramente:

Proceso comercial

Proceso contable/fiscal

🔹 Regla aplicada

Separación de hechos del negocio

🔹 Problema que se corrige

Evita:

Tratar toda orden como venta

Inconsistencias fiscales

🔹 Estructura resultante

Entidad transaccional clara, orientada a facturación.

5️⃣ pago
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa el movimiento financiero:

Asociado a un cliente

Asociado a un detalle u obligación de pago

Puede no coincidir temporalmente con la venta

🔹 Regla aplicada

Separación financiera

🔹 Problema que se corrige

Evita:

Asumir pago inmediato

Mezclar facturación con cobro

🔹 Estructura resultante

Entidad financiera independiente, necesaria para escenarios reales:

Pagos parciales

Pagos diferidos

Auditoría financiera

📊 Resumen del PASO 1 – Bloque Comercial

| Entidad         | Tipo   | Fuerte / Débil | Rol en el proceso   |
| --------------- | ------ | -------------- | ------------------- |
| cliente         | Datos  | Fuerte         | Actor comercial     |
| ordene          | Datos  | Fuerte         | Inicio del proceso  |
| detalles_ordene | Pivote | Débil          | Productos por orden |
| venta           | Datos  | Fuerte         | Hecho fiscal        |
| pago            | Datos  | Fuerte         | Hecho financiero    |

🧠 Conclusión técnica del PASO 1
✔ Las entidades están correctamente separadas
✔ El proceso comercial está bien descompuesto
✔ No hay entidades mal clasificadas
✔ El diseño soporta escenarios reales (no simplificados)

## BLOQUE 2 - PASO 2

🅱️ BLOQUE COMERCIAL
PASO 2 – Análisis profundo de atributos

(PK, UQ, multivaluados, dependencias funcionales)

📌 Alcance
Analizamos solo atributos, entidad por entidad.
Para cada decisión, explico explícitamente:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

1️⃣ cliente
📌 Atributos definidos

cliente_ID (PK)

nombre_empresa

contacto_nombre

emails_clientes

cedula_o_pasaporte

direccion

ciudad_ID (FK)

🔹 Clave primaria

cliente_ID

1️⃣ Regla aplicada
Entidad fuerte → PK propia

2️⃣ Problema que corrige
Identificación técnica inequívoca del cliente

3️⃣ Estructura resultante
PK simple y estable

🔹 Atributos únicos (UQ)
emails_clientes

1️⃣ Regla aplicada
Unicidad funcional de contacto

2️⃣ Problema que corrige
Un mismo correo asociado a múltiples clientes

3️⃣ Estructura resultante

Marcado como UQ

Detectado como multivaluado (ver abajo)

cedula_o_pasaporte

1️⃣ Regla aplicada
Identificador natural único

2️⃣ Problema que corrige
Duplicación de clientes reales

3️⃣ Estructura resultante
Atributo UQ, candidato a clave

🔹 Atributo multivaluado

emails_clientes

1️⃣ Regla aplicada (1FN)
No se permiten atributos multivaluados

2️⃣ Problema que corrige
Listas de correos en una sola columna

3️⃣ Estructura resultante

emails_clientes no debe permanecer aquí

Se normaliza a entidad dependiente (más adelante)

🔹 Atributos simples

nombre_empresa

contacto_nombre

direccion

1️⃣ Regla aplicada
Atributos atómicos

2️⃣ Problema que corrige
Ninguno (correctos conceptualmente)

3️⃣ Estructura resultante
Se mantienen sin cambios en este paso

🔹 Clave foránea

ciudad_ID (FK)

1️⃣ Regla aplicada
Integridad referencial

2️⃣ Problema que corrige
Direcciones sin ubicación válida

3️⃣ Estructura resultante
Relación correcta con catálogo geográfico

2️⃣ ordene (orden)
📌 Atributos definidos

orden_ID (PK)

cliente_ID (FK)

empleado_ID (FK)

fecha_orden

transportista_ID (FK)

🔹 Clave primaria

orden_ID

1️⃣ Regla aplicada
Entidad transaccional → PK propia

2️⃣ Problema que corrige
Identificación única de la orden

3️⃣ Estructura resultante
PK simple

🔹 Claves foráneas

cliente_ID

empleado_ID

transportista_ID

1️⃣ Regla aplicada
Integridad referencial

2️⃣ Problema que corrige
Órdenes sin cliente, empleado o logística

3️⃣ Estructura resultante
Relaciones obligatorias con actores del proceso

🔹 Dependencias funcionales

fecha_orden depende solo de orden_ID

1️⃣ Regla aplicada
2FN – dependencia completa de la clave

2️⃣ Problema que corrige
Dependencias parciales

3️⃣ Estructura resultante
Entidad correctamente normalizada

3️⃣ detalles_ordene (detalle_orden)
📌 Atributos definidos

detalle_orden_ID (PK)

orden_ID (FK)

producto_ID (FK)

precio_unitario

cantidad

🔹 Clave primaria

detalle_orden_ID

1️⃣ Regla aplicada
Entidad pivote con identificador propio

2️⃣ Problema que corrige
PK compuesta innecesaria

3️⃣ Estructura resultante
PK simple

🔹 Claves foráneas

orden_ID

producto_ID

1️⃣ Regla aplicada
Entidad asociativa N:M

2️⃣ Problema que corrige
Listas de productos en órdenes

3️⃣ Estructura resultante
Entidad pivote correcta

🔹 Dependencias funcionales críticas
precio_unitario

1️⃣ Regla aplicada
Separación precio histórico vs precio actual

2️⃣ Problema que corrige
Cambios de precio que alteran órdenes pasadas

3️⃣ Estructura resultante
Precio fijado al momento de la orden (correcto)

cantidad (actualmente VARCHAR)

1️⃣ Regla aplicada
Dominio semántico del dato

2️⃣ Problema que corrige
Valores no numéricos en cantidades

3️⃣ Estructura resultante
⚠️ Señalado como error de tipo, debe ser numérico
(esto se corrige en el modelo lógico, no aquí)

4️⃣ venta
📌 Atributos definidos

venta_ID (PK)

orden_ID (FK)

cliente_ID (FK)

ciudad_ID

fecha_de_venta

monto

impuesto_aplicado

montototal

metodo_venta

referencia_factura

🔹 Clave primaria

venta_ID

1️⃣ Regla aplicada
Entidad de hechos fiscales → PK propia

2️⃣ Problema que corrige
Identificación única de la venta

3️⃣ Estructura resultante
PK simple

🔹 Atributos únicos (UQ)
referencia_factura

1️⃣ Regla aplicada
Unicidad fiscal

2️⃣ Problema que corrige
Facturas duplicadas

3️⃣ Estructura resultante
Atributo UQ correcto

fecha_de_venta (UQ en el archivo)

⚠️ Observación técnica importante

1️⃣ Regla aplicada
Evaluación de unicidad lógica

2️⃣ Problema que corrige
Evitar restricciones incorrectas

3️⃣ Estructura resultante
❌ No debe ser UQ
→ múltiples ventas pueden ocurrir el mismo día
(se marca como corrección necesaria)

🔹 Atributos derivados
montototal

1️⃣ Regla aplicada
Atributos derivados no deben almacenarse

2️⃣ Problema que corrige
Inconsistencias por recálculo

3️⃣ Estructura resultante
⚠️ Candidato a derivado
(debe calcularse a partir de monto + impuesto)

5️⃣ pago
📌 Atributos definidos

pago_ID (PK)

detalle_orden_ID (FK)

cliente_ID (FK)

monto_de_pago

fecha_de_pago

metodo_de_pago

estado_de_pago

ref_tarjeta_cliente

🔹 Clave primaria

pago_ID

1️⃣ Regla aplicada
Entidad financiera → PK propia

2️⃣ Problema que corrige
Identificación única del pago

3️⃣ Estructura resultante
PK simple

🔹 Atributos únicos (UQ)
fecha_de_pago (UQ en el archivo)

⚠️ Observación técnica

1️⃣ Regla aplicada
Validación de unicidad real

2️⃣ Problema que corrige
Restricción inválida

3️⃣ Estructura resultante
❌ No debe ser UQ
→ múltiples pagos pueden realizarse el mismo día

🔹 Dependencias funcionales

monto_de_pago depende del pago

metodo_de_pago depende del pago

estado_de_pago depende del pago

✔ Correctas

**📊 Resumen del PASO 2 – Bloque Comercial**

| Entidad       | PK               | UQ válidos         | Observaciones                   |
| ------------- | ---------------- | ------------------ | ------------------------------- |
| cliente       | cliente_ID       | email, doc         | email multivaluado              |
| ordene        | orden_ID         | —                  | FKs correctas                   |
| detalle_orden | detalle_orden_ID | —                  | cantidad mal tipada             |
| venta         | venta_ID         | referencia_factura | fecha y monto total corregibles |
| pago          | pago_ID          | —                  | fecha_pago no es UQ             |

**📊 Resumen de reglas propuestas – Bloque Comercial**

| Entidad       | Reglas     |
| ------------- | ---------- |
| cliente       | 21, 22, 23 |
| ordene        | 24, 25, 26 |
| detalle_orden | 27, 28, 29 |
| venta         | 30, 31, 32 |
| pago          | 33, 34, 35 |

## 🅱️ **BLOQUE COMERCIAL**
PASO 3 – Relaciones, cardinalidades, clasificación final y jerarquías

(+ recomendación final de reglas de negocio al cierre del paso)

📌 Alcance
En este paso:

No se agregan atributos

No se implementa SQL

Se define cómo se conectan las entidades

Se fijan cardinalidades mín–máx

Se valida la coherencia del proceso comercial completo

Como siempre, para cada decisión:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

1️⃣ Clasificación final de entidades (bloque comercial)

| Entidad         | Clasificación        | Justificación               |
| --------------- | -------------------- | --------------------------- |
| cliente         | **Entidad de datos** | Actor comercial externo     |
| ordene          | **Entidad de datos** | Evento comercial inicial    |
| detalles_ordene | **Entidad pivote**   | Resuelve N:M orden–producto |
| venta           | **Entidad de datos** | Hecho fiscal                |
| pago            | **Entidad de datos** | Hecho financiero            |


✔ Regla aplicada

Clasificación por naturaleza del dato (operativo, asociativo, financiero).

✔ Problema que se corrige

Evita tratar transacciones como catálogos o viceversa.

✔ Estructura resultante

Bloque comercial claramente separado en datos y pivotes.

2️⃣ Relaciones principales del proceso comercial
🔹 Relación: cliente ↔ ordene

Tipo: 1 : N

Participación:

cliente → parcial

ordene → total

📌 Regla aplicada
Un cliente puede realizar muchas órdenes; una orden pertenece a un solo cliente.

📌 Problema que se corrige
Órdenes sin cliente o clientes duplicados por orden.

📌 Estructura resultante
cliente_ID como FK obligatoria en ordene.

🔹 Relación: empleado ↔ ordene

Tipo: 1 : N

Participación:

empleado → parcial

ordene → total

📌 Regla aplicada
Responsabilidad operativa de la orden.

📌 Problema que se corrige
Órdenes sin responsable asignado.

📌 Estructura resultante
empleado_ID como FK obligatoria.

🔹 Relación: ordene ↔ detalles_ordene

Tipo: 1 : N

Participación:

ordene → total

detalles_ordene → total

📌 Regla aplicada
Una orden se compone de uno o más detalles.

📌 Problema que se corrige
Órdenes vacías o productos sin orden.

📌 Estructura resultante
Entidad pivote correctamente dependiente.

🔹 Relación: producto ↔ detalles_ordene

Tipo: 1 : N

Participación:

producto → parcial

detalles_ordene → total

📌 Regla aplicada
Un producto puede aparecer en múltiples órdenes.

📌 Problema que se corrige
Duplicación de productos por orden.

📌 Estructura resultante
FK obligatoria producto_ID.

3️⃣ Relación: ordene ↔ venta

Tipo: 1 : 1

Participación:

ordene → parcial

venta → total

📌 Regla aplicada
Separación entre intención de compra y hecho fiscal.

📌 Problema que se corrige
Facturación automática de órdenes no confirmadas.

📌 Estructura resultante

Una orden puede no generar venta

Una venta siempre proviene de una orden

4️⃣ Relación: venta ↔ pago

Tipo: 1 : N

Participación:

venta → parcial

pago → total

📌 Regla aplicada
Modelo financiero realista (pagos parciales).

📌 Problema que se corrige
Imposibilidad de manejar créditos, anticipos o cuotas.

📌 Estructura resultante
Relación flexible y auditable.

5️⃣ Relaciones geográficas (implícitas pero válidas)
🔹 cliente ↔ ciudad
🔹 venta ↔ ciudad

📌 Regla aplicada
Normalización geográfica

📌 Problema que se corrige
Inconsistencia de ubicaciones

📌 Estructura resultante
Uso correcto de catálogos geográficos.

🧠 Conclusión técnica del PASO 3

✔ El proceso comercial está completamente trazado
✔ No hay ambigüedades en cardinalidades
✔ Separación correcta entre:

Orden

Venta

Pago
✔ El modelo soporta escenarios reales y complejos

🔹 Recomendación final de reglas de negocio
(Cierre del PASO 3 – Bloque Comercial)
📌 Regla propuesta 36

Una venta no puede marcarse como pagada si el total de pagos no cubre el monto total.

1️⃣ Regla aplicada
Integridad financiera

2️⃣ Problema que corrige
Estados financieros incorrectos

3️⃣ Estructura resultante

Validación acumulativa de pagos

📌 Regla propuesta 37

Una orden cancelada no puede generar venta ni pagos.

1️⃣ Regla aplicada
Coherencia del proceso

2️⃣ Problema que corrige
Facturación de órdenes inválidas

3️⃣ Estructura resultante

Estado de orden controlado

📌 Regla propuesta 38

Un pago rechazado no debe afectar el saldo de la venta.

1️⃣ Regla aplicada
Integridad transaccional

2️⃣ Problema que corrige
Saldos negativos o incorrectos

3️⃣ Estructura resultante

Estado del pago obligatorio

📌 Regla propuesta 39

La referencia de factura debe ser única a nivel global.

1️⃣ Regla aplicada
Unicidad fiscal

2️⃣ Problema que corrige
Duplicación de documentos legales

3️⃣ Estructura resultante

Restricción UQ global

📊 Resumen del Bloque Comercial (final)

✔ Entidades bien clasificadas
✔ Relaciones coherentes y completas
✔ Cardinalidades defendibles
✔ Reglas de negocio alineadas al dominio real
✔ Listo para traducción a modelo lógico
---------------------------------------------------------------------

# **BLOQUE 3  PRODUCTOS / SUMINISTRO**

**Inicio formal**

A partir de aquí entramos al ecosistema de abastecimiento y catálogo, que alimenta al bloque comercial.

📦 Entidades involucradas (según tu archivo)

producto

categoria

subministradore

transportista

📌 Metodología (idéntica a los bloques anteriores)

Seguiremos el mismo flujo:

PASO 1

Identificación, validación y clasificación
(datos / catálogo / pivote)

PASO 2

Análisis profundo de atributos
(PK, UQ, multivaluados, dependencias)

PASO 3

Relaciones, cardinalidades y reglas de negocio
(+ reglas propuestas)

Traducción a modelo lógico preliminar

🅲 BLOQUE PRODUCTOS / SUMINISTRO
PASO 1 – Identificación, validación y clasificación de entidades

📌 Alcance
En este paso:

NO analizamos atributos en profundidad

NO definimos SQL

Validamos qué entidades existen, qué representan y qué tipo de entidad son

Como siempre, para cada decisión:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

📦 Entidades incluidas en el bloque

(extraídas exclusivamente de tu archivo)

producto

categoria

subministradore

transportista

1️⃣ producto
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa un bien comercializable real:

Se vende

Se almacena

Se repone

Tiene identidad propia y ciclo de vida independiente.

🔹 Regla aplicada

Entidad fuerte del MER

🔹 Problema que se corrige

Evita:

Tratar productos como simples atributos

Repetición de datos de producto en órdenes

🔹 Estructura resultante

Entidad central del bloque de suministro.

2️⃣ categoria
🔹 Clasificación

Entidad de catálogo

Entidad fuerte

🔹 Justificación

Define una clasificación estable de productos:

Cambia poco en el tiempo

Se reutiliza ampliamente

🔹 Regla aplicada

Entidades de dominio / catálogo

🔹 Problema que se corrige

Evita:

Categorías escritas libremente

Inconsistencias semánticas

🔹 Estructura resultante

Catálogo controlado y reutilizable.

3️⃣ subministradore (suministrador)
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa una empresa externa:

Provee productos

Tiene identidad legal propia

🔹 Regla aplicada

Entidad fuerte con relaciones comerciales

🔹 Problema que se corrige

Evita:

Mezclar proveedores con productos

Repetir datos del proveedor

🔹 Estructura resultante

Entidad independiente, reutilizable y escalable.

4️⃣ transportista
🔹 Clasificación

Entidad de datos

Entidad fuerte

🔹 Justificación

Representa un actor logístico externo:

Transporta productos u órdenes

No forma parte del catálogo

🔹 Regla aplicada

Entidad de datos operativa

🔹 Problema que se corrige

Evita:

Tratar logística como atributo

Repetir datos de transporte por orden

🔹 Estructura resultante

Entidad logística separada y reutilizable.

### RESUMEN DE PASO 1 DEL BLOQUE 3

| Entidad         | Clasificación | Fuerte / Débil | Rol            |
| --------------- | ------------- | -------------- | -------------- |
| producto        | Datos         | Fuerte         | Bien comercial |
| categoria       | Catálogo      | Fuerte         | Clasificación  |
| subministradore | Datos         | Fuerte         | Proveedor      |
| transportista   | Datos         | Fuerte         | Logística      |

🧠 Conclusión técnica del PASO 1

✔ Entidades correctamente identificadas
✔ Clasificación coherente (datos vs catálogo)
✔ Separación clara entre:

Producto

Proveedor

Logística
✔ Diseño alineado con sistemas reales de abastecimiento

**🅲 BLOQUE PRODUCTOS / SUMINISTRO**
PASO 2 – Análisis profundo de atributos

(PK, UQ, multivaluados, dependencias funcionales)

📌 Alcance
Analizamos atributos por entidad, sin agregar nuevas entidades.
Para cada decisión explico explícitamente:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

1️⃣ producto
📌 Atributos definidos (según tu archivo)

producto_ID (PK)

nombre_producto

subministrador_ID (FK)

categoria_ID (FK)

codigo_barras (UQ)

cantidad_por_unidad

precio_unitario

🔹 Clave primaria

producto_ID

1️⃣ Regla aplicada
Entidad fuerte → PK propia

2️⃣ Problema que corrige
Identificación técnica inequívoca del producto

3️⃣ Estructura resultante
PK simple y estable

🔹 Atributo único (UQ): codigo_barras

1️⃣ Regla aplicada
Identificador natural único del producto

2️⃣ Problema que corrige
Duplicación de productos físicos iguales

3️⃣ Estructura resultante
codigo_barras como UQ, candidato a clave natural

📌 Observación técnica
El tipo INT(16) es conceptualmente incorrecto para códigos de barras:

Puede perder ceros a la izquierda

No siempre es estrictamente numérico

⚠️ Corrección señalada, no aplicada aún.

🔹 Claves foráneas

subministrador_ID

categoria_ID

1️⃣ Regla aplicada
Integridad referencial

2️⃣ Problema que corrige
Productos sin proveedor o sin clasificación

3️⃣ Estructura resultante
Relaciones obligatorias con proveedor y categoría

🔹 Dependencias funcionales

nombre_producto → producto

cantidad_por_unidad → producto

precio_unitario → producto

✔ Todas dependen solo de la PK

🔹 Atributos candidatos a reglas de negocio

precio_unitario

cantidad_por_unidad

📌 Se mantienen aquí; las restricciones se definen en reglas de negocio.

2️⃣ categoria
📌 Atributos definidos

categoria_ID (PK)

categoria_nombre (UQ)

descripcion

🔹 Clave primaria

categoria_ID

1️⃣ Regla aplicada
Entidad de catálogo → PK propia

2️⃣ Problema que corrige
Identificación técnica de la categoría

3️⃣ Estructura resultante
PK simple

🔹 Atributo único (UQ): categoria_nombre

1️⃣ Regla aplicada
Dominio controlado

2️⃣ Problema que corrige
Duplicación semántica de categorías

3️⃣ Estructura resultante
Nombre único y reutilizable

🔹 Dependencias funcionales

descripcion → categoria

✔ Correctas, sin dependencias transitivas

3️⃣ subministradore (suministrador)
📌 Atributos definidos

subministradores_ID (PK)

nombre_empresa (UQ)

nombre_contacto

direccion

ciudad_ID (FK)

telefono

🔹 Clave primaria

subministradores_ID

1️⃣ Regla aplicada
Entidad fuerte → PK propia

2️⃣ Problema que corrige
Identificación técnica del proveedor

3️⃣ Estructura resultante
PK simple

🔹 Atributo único (UQ): nombre_empresa

1️⃣ Regla aplicada
Unicidad comercial

2️⃣ Problema que corrige
Registro duplicado del mismo proveedor

3️⃣ Estructura resultante
Nombre de empresa único

📌 Observación
Esto asume que no existen proveedores con el mismo nombre legal.
Es válido, pero se marca como regla de negocio implícita.

🔹 Clave foránea

ciudad_ID

1️⃣ Regla aplicada
Normalización geográfica

2️⃣ Problema que corrige
Direcciones inconsistentes

3️⃣ Estructura resultante
Relación correcta con catálogo ciudad

🔹 Atributos simples

nombre_contacto

direccion

telefono

✔ Atómicos, no multivaluados (por ahora)

4️⃣ transportista
📌 Atributos definidos

transportista_ID (PK)

nombre_empresa (UQ)

telefono

ciudad_ID (FK)

🔹 Clave primaria

transportista_ID

1️⃣ Regla aplicada
Entidad fuerte → PK propia

2️⃣ Problema que corrige
Identificación técnica del transportista

3️⃣ Estructura resultante
PK simple

🔹 Atributo único (UQ): nombre_empresa

1️⃣ Regla aplicada
Unicidad del actor logístico

2️⃣ Problema que corrige
Duplicación de transportistas

3️⃣ Estructura resultante
Nombre de empresa único

🔹 Clave foránea

ciudad_ID

1️⃣ Regla aplicada
Integridad geográfica

2️⃣ Problema que corrige
Transportistas sin ubicación válida

3️⃣ Estructura resultante
Relación correcta con ciudad

| Entidad         | PK                  | UQ               | Observaciones          |
| --------------- | ------------------- | ---------------- | ---------------------- |
| producto        | producto_ID         | codigo_barras    | tipo de dato mejorable |
| categoria       | categoria_ID        | categoria_nombre | catálogo estable       |
| subministradore | subministradores_ID | nombre_empresa   | unicidad asumida       |
| transportista   | transportista_ID    | nombre_empresa   | correcto               |

🧠 Conclusión técnica del PASO 2

✔ PK y UQ correctamente identificados
✔ Dependencias funcionales limpias
✔ No hay violaciones de 1FN / 2FN / 3FN
✔ Se detectaron errores reales de tipo de dato, no de concepto
✔ El modelo sigue siendo sólido y profesional

**📊 Resumen de reglas propuestas – Bloque Productos / Suministro**

| Entidad         | Reglas propuestas  |
| --------------- | ------------------ |
| producto        | 40, 41, 42, 43, 44 |
| categoria       | 45, 46             |
| subministradore | 47, 48, 49         |
| transportista   | 50, 51, 52         |


🧠 Conclusión antes de avanzar

✔ Reglas alineadas con tu modelo original
✔ Refuerzan integridad de inventario, catálogo y logística
✔ Preparan el sistema para escenarios reales de suministro
✔ Todas son defendibles técnica y académicamente

## 🅲 BLOQUE PRODUCTOS / SUMINISTRO

PASO 3 – Relaciones, cardinalidades y cierre conceptual

(+ recomendación final de reglas de negocio)

📌 Alcance
En este paso:

No se agregan atributos

No se implementa SQL

Se definen relaciones formales

Se fijan cardinalidades mín–máx

Se valida la coherencia del abastecimiento completo

Para cada decisión, como acordamos:

1️⃣ Qué regla se aplica
2️⃣ Qué problema se corrige
3️⃣ Qué estructura queda como resultado

1️⃣ Relación: categoria ↔ producto
🔹 Tipo de relación

1 : N

Participación:

categoria → parcial

producto → total

📌 Regla aplicada

Clasificación obligatoria de productos

📌 Problema que se corrige

Evita:

Productos sin categoría

Clasificaciones ambiguas o duplicadas

📌 Estructura resultante

FK categoria_ID obligatoria en producto

Catálogo estable y reutilizable

2️⃣ Relación: subministradore ↔ producto
🔹 Tipo de relación

1 : N

Participación:

subministradore → parcial

producto → total

📌 Regla aplicada

Integridad de abastecimiento

📌 Problema que se corrige

Evita:

Productos sin proveedor

Origen de suministro desconocido

📌 Estructura resultante

FK subministrador_ID obligatoria en producto

Relación clara proveedor–producto

3️⃣ Relación: transportista ↔ ordene

(interbloque: suministro ↔ comercial)

🔹 Tipo de relación

1 : N

Participación:

transportista → parcial

ordene → total

📌 Regla aplicada

Asignación logística única por orden

📌 Problema que se corrige

Evita:

Órdenes sin responsable logístico

Asignaciones múltiples ambiguas

📌 Estructura resultante

FK transportista_ID en ordene

Logística desacoplada del producto

4️⃣ Relación: subministradore ↔ ciudad
🔹 Tipo de relación

N : 1

Participación:

subministradore → total

ciudad → parcial

📌 Regla aplicada

Normalización geográfica

📌 Problema que se corrige

Direcciones de proveedor inconsistentes

📌 Estructura resultante

FK ciudad_ID obligatoria

Reutilización de catálogo geográfico

5️⃣ Relación: transportista ↔ ciudad
🔹 Tipo de relación

N : 1

Participación:

transportista → total

ciudad → parcial

📌 Regla aplicada

Integridad geográfica logística

📌 Problema que se corrige

Transportistas sin ubicación definida

📌 Estructura resultante

FK ciudad_ID obligatoria

Logística correctamente localizada

📊 **Resumen de relaciones – Bloque Productos / Suministro**

| Relación                   | Cardinalidad | Dependencia           |
| -------------------------- | ------------ | --------------------- |
| categoria → producto       | 1:N          | producto depende      |
| subministradore → producto | 1:N          | producto depende      |
| transportista → ordene     | 1:N          | ordene depende        |
| subministradore → ciudad   | N:1          | proveedor depende     |
| transportista → ciudad     | N:1          | transportista depende |

🧠 Conclusión técnica del PASO 3

✔ El bloque de suministro está conceptualmente cerrado
✔ No existen ambigüedades de abastecimiento
✔ La logística está correctamente desacoplada
✔ El diseño soporta crecimiento multinacional
✔ Integración limpia con el bloque comercial