### **Aurora Global Nexus**

7. **Identificar los tipos de relaciones del sistema.**  **listo**

## **Relaciones O Cardinalidades**

### **UNO A UNO (1:1)**

**1. info_personal_empleados <---> info_laboral_empleados**

* Cada **empleados** _pertenece_ a un_único_registro **Laboral** (_1 a 1_).

### **2. info_personal_empleados** <----> **historial_laboral**

* (si se maneja como **cargo actual**) puede ser (_1:1_) se se guarda como cargo vigente; si se guarda el hiatorial completo seria (1:N)

### **UNO A MUCHOS (1:N)**

**clientes** <----> **ordenes**

* Un **cliente** puede tener muchas ordenes, pero cada **orden** pertenece a un solo **cliente**

**transportistas** <----> **ordenes** 

* un **transportista** puede enviar muchas **ordenes**, pero cada orden se envia por un solo **transportista**

**ciudades** <----> **clientes/subministradores/transportistas**

* una **ciudad** puede tener muchos **clientes, proveedores o transportistas**, pero cada uno e estos pertenece a una sola **ciudad**

**paises** <----> **ciudadeses**

Un **país** puede tener muchas ciudades, pero cada **ciudad** pertenece a un **pais**

**info_laboral_empleados** <----> **historial_laboral**(Si guardas multiples registros)

Un **empleado** puede tener muchos registros de historial, pero cada**registro** pertenece a un solo **empleado**

### **MUCHOS A MUCHOS (N:M)**

**ordenes** <----> **productos**

Una **orden** puede incluir muchos productos, y y un **producto** puede estar en muchas **ordenes**

**categorias** <----> **productos**

Una **categoria** puede tener muchos productos, y un **producto** puede pertenecer a nuchas **categorias**
