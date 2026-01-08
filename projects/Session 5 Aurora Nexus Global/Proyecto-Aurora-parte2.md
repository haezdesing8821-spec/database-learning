# **PROCESO DE NORMALIZACIÓN Y MODELADO DE LA BASE DE DATOS

## **METODOLOGÍA DE TRABAJO PARA NUESTRO SISTEMA:**

### 🔹 FASE 1 – Revisión y validación conceptual (PASO 1)

Objetivo: confirmar que cada entidad es correcta y cumple su rol.

Haremos:

    Validación de cada entidad:

    ¿Es fuerte o débil?

    ¿Tiene sentido propio?

Detección de:

    Entidades duplicadas conceptualmente

    Entidades que deberían fusionarse o separarse

⚠️ Importante:

    No se elimina ni cambia nada sin justificación formal.

### 🔹 FASE 2 – Atributos (ajuste solicitado)

Queda confirmado y obligatorio:

**✔ Identificar y marcar TODOS los atributos únicos (`UQ`)**

**✔ Diferenciar claramente entre:**

    Clave primaria (PK)

    Atributo único (UQ)

    Atributo candidato a clave (si aplica)

    Atributo normal

    Atributos multivaluados

**📌 No asumir unicidad:**

    Solo la declarar cuando sea lógica, técnica y justificable en un sistema real.

    Cada (UQ) tendrá justificación explícita.

### 🔹 FASE 3 – Relaciones, cardinalidades y reglas de negocio (PASO 3)

**Objetivo: que el modelo refleje la realidad exacta del negocio.**

**Analizaremos:**

    Relaciones explícitas

    Relaciones reflexivas

    Cardinalidades mín–máx

    Dependencias de (existencia -Identificación)

    Jerarquías (generalización / especialización)

**Nada se asumirá: Si una cardinalidad no está explícita → se marca como no definida.**

**Clasificación de entidades (nuevo requisito)**

**En esta fase, todas las entidades serán clasificadas como:**

Entidad de datos

    → Registra hechos operativos (ej.: empleado, cliente, venta)

Entidad de catálogo

    → Valores relativamente estables y referenciales (ej.: país, categoría)

Entidad pivote (intermedia / asociativa)

    → Resuelve relaciones N:M o procesos (ej.: detalle_orden)

**Esta clasificación no es decorativa:**

    Afecta cardinalidades

    Afecta normalización

    Afecta diseño físico posterior

### 🔹 FASE 4 – Normalización formal (PASO 4)

**Aquí entramos en terreno 100 % técnico:**

    ✔ Primera Forma Normal (1FN)

    ✔ Segunda Forma Normal (2FN)

    ✔ Tercera Forma Normal (3FN)

**Para cada paso:**

    Qué regla se aplica

    Qué problema se corrige

    Qué estructura queda como resultado

Sin atajos.

### 🔹 FASE 5 – Más allá (opcional, pero recomendable)

**Si quieres ir más lejos (y sí se puede):**

    🔶 Modelo lógico relacional final

    🔶 Preparación para SQL (sin escribir aún)

    🔶 Evaluación de escalabilidad

    🔶 Detección de puntos críticos reales de una multinacional

    🔶 Conversión posterior a SQL Server / PostgreSQL / MySQL

**🧭 Cómo vamos a trabajar (muy importante)**

**Para mantener precisión total, lo haremos así:**

Paso por paso

    Una fase a la vez

    validar antes de avanzar

    Si algo no se puede confirmar, lo describirlo explícitamente

Este será un trabajo de arquitecto de datos, no de improvisación.

## Reglas de negocio (ampliación clave)

*+Aquí hago una distinción MUY importante:**

    🔸 Reglas de negocio explícitas de forma previa

Se respetan tal como han definido, sin modificación.

    🔸 Reglas de negocio implícitas (donde trabajaremos con sugerencias posibles)

**Apartir de ahora sí harémos lo siguiente:**

✔ Identificar reglas necesarias para la coherencia del sistema

✔ Proponer nuevas reglas, pero siempre:

    Lógicas

    Consistentes

    Realistas

**Alineadas con una multinacional de suministro**

**⚠️ Cada nueva regla será:**

    Claramente marcada como “Regla propuesta”

    Justificada

    Opcional hasta que sea valida

    Nada se impone sin tu aprobación.

**EN UN ESCENARIO REAL Todo lo propuesto:**

debe poder defenderse en un aula, una auditoría o una entrevista técnica.

# **PLAN DE ACCIÓN RESUMIDO:**

🧭 Plan actualizado y resumido

🔹 **FASE 1 – Validación conceptual de entidades**

    ✔ Qué es entidad

    ✔ Tipo fuerte / débil

    ✔ Sentido de negocio

🔹 **FASE 2 – Análisis completo de atributos**

    ✔ PK
    
    ✔ UQ
    
    ✔ Multivaluados
    
    ✔ Derivados
    
    ✔ Dependencias funcionales

🔹 **FASE 3 – Relaciones + clasificación + reglas**

✔ Cardinalidades mín–máx

✔ Relaciones reflexivas

✔ Generalización / especialización

✔ Clasificación de entidad (datos / catálogo / pivote)

✔ Reglas explícitas + reglas propuestas

🔹 **FASE 4 – Normalización formal**

✔ 1FN

✔ 2FN

✔ 3FN

✔ Justificación técnica de cada cambio

## **Siguiente paso (decisión técnica)**

Para empezar correctamente, necesitamos que eleguir el bloque inicial, porque de ahí se propagan reglas y dependencias.

### **PLANN DE ACCIÓN DIVIDIDO POR BLOQUES**

**🅰️ Bloque EMPLEADOS**

(Es el más complejo y define jerarquías, reflexividad y especialización)

**🅱️ Bloque COMERCIAL**

(cliente – orden – venta – pago)

**🅲️ Bloque PRODUCTOS / SUMINISTRO**

(producto – categoría – subministrador – transportista)
