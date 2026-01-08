# Aurora Global Nexus

## **Listado de Entidades**

1. Identificar las entidades del sistema.                                           **listo**
2. Identificar los atributos de las entidades.                                      **listo**
3. Identificar las llaves primarias y foráneas.                                     **listo**
4. Asignar una nomenclatura adeacuada a las entidades y sus atributos.
5. Identificar los tipos de dato de los atributos de las entidades del sistema.     **listo**
6. Identificar los atributos que puedan ser únicos en el sistema.                   **liatos**

**CARASTERISTICAS ADICIONALES AL DISEÑO:**

* SE NECESITA ESTABLECER JERARQUÍA EN EL BLOQUE DE ENTIDADES QUE ESTAN RELACIONADAS CON EMPLEADOS, ESPESIFICAMENTE EN LA TABLA DE EMPLEADOS,
SE DESEA ESTABLECER GERARQUIA A NIVEL DE ESPECIALIZACIÓN PARA DESIDNAR DOS NUEVA SENTIDADES DEPENDIENTES DE LA SUPER ENTIDAD `EMPLEADO`, ESTAS SERÁN
`ADMINISTRATIVOS` Y/O `DIRECTIVOS`.

* la empresa cuenta con una **estructura jerárquica, por lo que un empleado puede supervisar a otros empleados. Todo empleado, salvo el de mayor jerarquía, debe tener un supervisor asignado, y un empleado no puede supervisarse a sí mismo.**

* **Los empleados** pueden pertenecer a dos subtipos: **administrativos y directivos**. No todos los empleados pertenecen necesariamente a alguno de estos subtipos. Un **empleado puede ser administrativo** y, adicionalmente, puede llegar a ser **directivo**, pero un **directivo** siempre debe haber sido previamente **administrativo**, no puede pertenercer a ambos subtipos a la vez, debe ser unica y exclusivamente uno u otro.

**RESULTADO FINAL**

### clientes         **(EDD)**

- cliente_ID         **INT - AUTO,  (PK)**
- nombre_empresa     **VARCHAR(25)**
- contacto_nombre    **VARCHAR(30)**
- emails_clientes    **VARCHAR(50),  (UQ)**
- cedula_o_pasaporte **VARCHAR(15),  (UQ)**
- direccion          **VARCHAR(15)**
- ciudad_ID          **(FK)**

### datos_empleados **(EDD)**

- empleado_ID       **INT - AUTO, (PK)**
- fecha_nacimiento  **DATE**
- doc_identidad     **VARCHAR(25), (UQ)**
- emails_empleados  **(UQ)**
- telefono          **VARCHAR(12)**
- fotografía        **TEXT(100)**

### dieccion_empleados **(EDD)**

- direccion_ID      **INT - AUTO, (PK)**
- empleado_ID       **INT, (FK)**
- calle             **VARCHAR(15)**
- numero_calle      **VARCHAR(10)**
- ciudad            **VARCHAR(10)**
- pais              **VARCHAR(10)**
- codigo_postal     **VARCHAR(10)**

### empleados       **(EDD)**

- empleado_ID       **INT - AUTO, (PK)**
- nombre            **VARCHAR(15)**
- apellido          **VARCHAR(15)**
- fecha_ingreso     **DATE**
- puesto            **VARCHAR(15)**
- departamento      **VARCHAR(15)**
- surpervisor_ID    **VARCHAR(15)**
- notas             **VARCHAR(100)**

### empAdministrativo    **(ED)**

- Empleado_ID       **INT - AUTO, (PK)**
- Nivel_Admin       **VARCHAR(15)**
- Puesto_Admin      **VARCHAR(15)**
- Deprt_Asignado    **VARCHAR(15)**
- Tipo_Contrato     **VARCHAR(15)**
- Horario_Fijo      **VARCHAR(20)**

### empDirectivo         **(ED)**

- Empleado_ID            **INT - AUTO, (PK)**
- Nivel_Directivo        **VARCHAR(15)**
- Presupuesto_Asignado   **VARCHAR(15)**
- area_responsable       **VARCHAR(15)**
- firma_autorizada       **VARCHAR(15)**
- num_Report_Autorizado  **VARCHAR(15)**

### transportistas **(EC)**

- transportista_ID **INT - AUTO, (PK)**
- nombre_empresa   **VARCHAR(25), (UQ)**
- telefono         **VARCHAR(15)**
- ciudad_ID        **INT (FK)**

### ordenes        **(ED)**

- orden_ID         **INT - AUTO, (PK)**
- cliente_ID       **INT, (FK)**
- empleado_ID      **INT, (FK)**
- fecha_orden      **DATE**
- transportista_ID **INT, (FK)**

### detalles_ordenes **(EP)**

- detalle_orden_ID   **INT - AUTO, (PK)**
- orden_ID           **INT, (FK)**
- producto_ID        **INT, (FK)**
- precio_unitario    **FLOAT(18.2)**
- cantidad           **VARCHAR(15)**

### productos          **(EC)**

- producto_ID          **INT - AUTO, (PK)**
- nombre_producto      **VARCHAR(25)**
- subministrador_ID    **INT, (FK)**
- categoria_ID         **INT, (FK)**
- codigo_barras        **INT(16), (UQ)**
- cantidad_por_unidad  **INT**
- precio_unitario      **FLOAT(18.2)**

### subministradores **(EC)**

- subministradores_ID **INT - AUTO, (PK)**
- nombre_empresa      **VARCHAR(25), (UQ)**
- nombre_contacto     **VARCHAR(25)**
- direccion           **VARCHAR(100)**
- ciudad_ID           **INT, (FK)**
- telefono            **VARCHAR(15)**

### categoria      **(EC)**

- categoria_ID     **INT - AUTO, (PK)**
- categoria_nombre **VARCHAR(25), (UQ)**
- descripcion      **VARCHAR(100)**

### pais       **(EC)**

- pais__ID     **INT - AUTO, (PK)**
- nombre_pais  **VARCHAR(25), (UQ)**

### ciudad       **(EC)**

- ciudad_ID      **INT - AUTO, (PK)**
- nombre_ciudad  **VARCHAR(25), (UQ)**
- codigo_postal  **VARCHAR(25)**
- pais _ID       **INT, (FK)**

### historial_laboral **(ED)**

- historial_ID        **INT - AUTO, (PK)**
- empleado_ID         **VARCHAR(25), (FK)**
- fecha_inicio        **DATE**
- fecha_fin           **DATE**

### pagos             **(ED)**

- Pago_ID             **INT - AUTO, (PK)**
- detalle_orden_ID    **INT, (FK)**
- cliente_ID          **INT, (FK)**
- monto_de_pago       **FLOAT(18.2)**
- fecha_de_ pago      **DATE, (UQ)**
- metodo_de_pago      **VARCHAR(15), (FK)**
- esttado_de_pago     **VARCHAR(15)**
- ref_tarjeta_cliente **VARCHAR(16)**

### ventas            **(ED)**  

- venta_ID            **INT - AUTO, (PK)**
- orden_ID            **INT,         (FK)**
- cliente_ID          **INT,         (FK)**
- ciudad_ID           **INT**
- fecha_de_venta      **DATE, (UQ)**
- monto               **FLOAT(18.2)**
- impuesto_aplicado   **FLOAT(5.2)**
- montototal          **VARCHAR(15)**
- metodo_venta        **VARCHAR(15), (FK)**
- referencia_factura  **VARCHAR(15), (UQ)**
- cede                **VARCHAR(25)**
- tipo_venta          **VARCHAR(15)**

* SE NECESITA ESTABLECER JERARQUÍA EN EL BLOQUE DE ENTIDADES QUE ESTAN RELACIONADAS CON EMPLEADOS, ESPESIFICAMENTE EN LA TABLA DE EMPLEADOS,
SE DESEA ESTABLECER GERARQUIA A NIVEL DE ESPECIALIZACIÓN PARA DESIDNAR DOS NUEVA SENTIDADES DEPENDIENTES DE LA SUPER ENTIDAD `EMPLEADO`, ESTAS SERÁN
`ADMINISTRATIVOS` Y/O `DIRECTIVOS`.

* la empresa cuenta con una **estructura jerárquica, por lo que un empleado puede supervisar a otros empleados. Todo empleado, salvo el de mayor jerarquía, debe tener un supervisor asignado, y un empleado no puede supervisarse a sí mismo.**

* **Los empleados** pueden pertenecer a dos subtipos: **administrativos y directivos**. No todos los empleados pertenecen necesariamente a alguno de estos subtipos. Un **empleado puede ser administrativo** y, adicionalmente, puede llegar a ser **directivo**, pero un **directivo** siempre debe haber sido previamente **administrativo**, no puede pertenercer a ambos subtipos a la vez, debe ser unica y exclusivamente uno u otro.