# Aurora Global Nexus

## **Reglas de Negocio PARTE 1 (CRUD)**

### **ENTIDADES PRINCIPALES DLE SISTEMA:**

**clientes**

1. Crear el registro de un cliente
1. Leer el registro de un(os) cliente(s) dada una condición en particular.
1. Leer todos los registros de la entidad clientes.
1. Actualizar los datos de un cliente dada una condición en particular.
1. Eliminar los datos de una cliente dada una condición en particular.

**subministradores**

1. Todos los valores del atributo nombre_compañia, deberán estar expresados en **orden alfabetico** y no se podrán repetir.
1. Crear el registro de un tipo de carrera
1. Leer el registro de un(os) tipo(s) de subministradore(s) dada una condición en particular.
1. Leer todos los registros de la entidad subministradores.
1. Actualizar los datos de un subministrador dada una condición en particular.
1. Eliminar los datos de una subministrdor dada una condición en particular.

**paises**

1. Crear el registro de un país
1. Leer el registro de un(os) pais(es) dada una condición en particular.
1. Leer todos los registros de la entidad paises.
1. Actualizar los datos de un país dada una condición en particular.
1. Eliminar los datos de un páis dada una condición en particular.

### **Reglas de Negocio PARTE 2 (CRUD), Generales**

las operaciones CRUD (Create, Read, Update, Delete) en tu sistema de tienda deben estar gobernadas por las reglas de negocio, y que esas reglas se manifiestan automáticamente en las transacciones recurrentes. n este caso las reglas de nuestro sistema integrando **CRUD** son las siguientes:

## 📘 **Integración de reglas de negocio con CRUD**

🔹 **Consultar (Read)**
Regla de negocio: solo ciertos roles pueden acceder a ciertos datos.  
Manifestación automática:  
Un cliente solo puede consultar sus propias órdenes.  
Un administrador puede consultar todas las órdenes.  
Ejemplo SQL:

```sql
SELECT * FROM Orden WHERE clienteID = @clienteID;
```

🔹 **Agregar (Create)**
Regla de negocio: no se puede crear una orden si el cliente no existe o si el stock del producto es 0.  
Manifestación automática:  
La FK asegura que el cliente exista.  
Un trigger o procedimiento valida stock antes de insertar.  
Ejemplo SQL:

```sql
INSERT INTO Orden (fecha, clienteID, total)
VALUES (CURRENT_DATE, 105, 250.00);
```
👉 Si el clienteID no existe, la transacción falla automáticamente por la FK.

🔹 **Actualizar (Update)**
Regla de negocio: no se puede cambiar una orden de “pagada” a “pendiente”.  
Manifestación automática:  
**CHECK** o trigger bloquea cambios inválidos.  
Ejemplo SQL:

```sql
UPDATE Orden
SET estado = 'pendiente'
WHERE ordenID = 2001;
```
👉 Si la regla lo prohíbe, la transacción se rechaza.

🔹 **Eliminar (Delete)**
Regla de negocio: no se puede eliminar un cliente con órdenes activas.  
Manifestación automática:  
La FK con ON DELETE RESTRICT impide borrar el cliente si tiene órdenes relacionadas.  
Ejemplo SQL:

```sql
DELETE FROM Cliente WHERE clienteID = 105;
```
👉 Si el cliente tiene órdenes, la transacción falla automáticamente.

Así, cada operación CRUD refleja las reglas de negocio sin necesidad de “reprogramarlas” en cada consulta. Por favor implementar con cada una de ellas así, el DBMS las hace cumplir de manera recurrente.

## **📘Integración de reglas de negocio con PARTE 3**
Integración de CHECK con reglas de negocio en una tienda

Tu enfoque es acertado: los CHECK son ideales para validar reglas de negocio a nivel de fila, garantizando que cada registro cumpla condiciones coherentes sin depender de la aplicación. Te dejo una guía clara y estándar SQL para integrarlos en entidades clave de una tienda.

-**Principios prácticos de CHECK**

- Ámbito: Valida condiciones dentro de la misma fila; no puede consultar otras tablas.
- Nulos: Un CHECK evalúa a verdadero si su expresión es verdadera o desconocida; usa NOT NULL para evitar que NULL pase reglas sin querer.
- Portabilidad: Usa expresiones estándar (comparaciones, IN, AND/OR, BETWEEN, LIKE, DATE/DECIMAL/INT) y nombra las restricciones para administración.
- Nombrado: Define nombres claros (CONSTRAINT chkpreciono_negativo) para auditoría y mantenimiento.

### **Reglas De Negocios Implementando Sentencia (CHECK) por entidad para confirmación de condiciones en las transacciones**

**Entidad Productos**

- Precio no negativo:
  **- CONSTRAINT chkproductoprecio CHECK (precio >= 0)**
- Stock no negativo:
  **- CONSTRAINT chkproductostock CHECK (stock >= 0)**
- Estado operativo:
  **- CONSTRAINT chkproductoestado CHECK (estado IN ('activo','inactivo'))**
- Relación estado–stock (producto inactivo no se vende):
  **- CONSTRAINT chkproductoestado_stock CHECK (NOT (estado = 'activo' AND stock = 0))**

**Ejemplo:**

```sql
CREATE TABLE Producto (
  producto_ID INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  nombre VARCHAR(100) NOT NULL,
  precio DECIMAL(10,2) NOT NULL,
  stock INT NOT NULL,
  estado VARCHAR(10) NOT NULL,
  CONSTRAINT chkproductoprecio       CHECK (precio >= 0),
  CONSTRAINT chkproductostock        CHECK (stock >= 0),
  CONSTRAINT chkproductoestado       CHECK (estado IN ('activo','inactivo')),
  CONSTRAINT chkproductoestado_stock CHECK (NOT (estado = 'activo' AND stock = 0))
);
```

**Entidad Clientes**

- Campos obligatorios y formato básico:
 **- NOT NULL en nombre/email.**
 **- Validación ligera de email (portabilidad): email LIKE '%@%._%' (no perfecto, pero estándar).**
- Tipo de cliente limitado:
  **- CONSTRAINT chkclientetipo CHECK (tipo IN ('regular','vip'))**

**Ejemplo:**

```sql
CREATE TABLE Cliente (
  cliente_ID INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  nombre VARCHAR(100) NOT NULL,
  emails  VARCHAR(100) NOT NULL,
  tipo   VARCHAR(10)  NOT NULL,
  CONSTRAINT chkclientetipo  CHECK (tipo IN ('regular','vip')),
  CONSTRAINT chkclienteemail CHECK (email LIKE '%@%._%')
);
```

**Entidad Órdenes**

- Fecha válida (no futura, según política):
 **- CONSTRAINT chkordenfecha CHECK (fecha <= CURRENT_DATE)**
- Estado acotado:
  **- CONSTRAINT chkordenestado CHECK (estado IN ('pendiente','pagada','cancelada'))**
- Total coherente:
  **- CONSTRAINT chkordentotal CHECK (total >= 0)**

**Ejemplo:**

```sql
CREATE TABLE Orden (
  orden_ID INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  fecha DATE NOT NULL,
  cliente_ID INT NOT NULL,
  estado VARCHAR(10) NOT NULL,
  total DECIMAL(12,2) NOT NULL,
  CONSTRAINT chkordenfecha  CHECK (fecha <= CURRENT_DATE),
  CONSTRAINT chkordenestado CHECK (estado IN ('pendiente','pagada','cancelada')),
  CONSTRAINT chkordentotal  CHECK (total >= 0),
  CONSTRAINT fkordencliente FOREIGN KEY (clienteID) REFERENCES Cliente(clienteID)
);
```

**Entidad Detalle de orden**

- Cantidad y precio unitario válidos:
  **- CONSTRAINT chkdetallecantidad CHECK (cantidad > 0)**
  **- CONSTRAINT chkdetalleprecio   CHECK (precio_unitario >= 0)**
- Subtotal coherente (si lo almacenas):
  **- CONSTRAINT chkdetallesubtotal CHECK (subtotal = cantidad * precio_unitario)**

**Ejemplo:**

```sql
CREATE TABLE DetalleOrden (
  detalle_ID INT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  orden_ID INT NOT NULL,
  producto_ID INT NOT NULL,
  cantidad INT NOT NULL,
  precio_unitario DECIMAL(10,2) NOT NULL,
  subtotal DECIMAL(12,2) NOT NULL,
  CONSTRAINT chkdetallecantidad CHECK (cantidad > 0),
  CONSTRAINT chkdetalleprecio   CHECK (precio_unitario >= 0),
  CONSTRAINT chkdetallesubtotal CHECK (subtotal = cantidad * precio_unitario),
  CONSTRAINT fkdetalleorden     FOREIGN KEY (ordenID) REFERENCES Orden(ordenID),
  CONSTRAINT fkdetalleproducto  FOREIGN KEY (productoID) REFERENCES Producto(productoID)
);
```

**Entidad Pagos**

- Monto y fechas coherentes:
  **- CONSTRAINT chkpagomonto CHECK (monto > 0)**
  **- CONSTRAINT chkpagofecha CHECK (fechapago <= CURRENTDATE)**
- Método acotado:
  **- CONSTRAINT chkpagometodo CHECK (metodo IN ('efectivo','tarjeta','transferencia'))**
- Estado acotado:
  **- CONSTRAINT chkpagoestado CHECK (estado IN ('pendiente','confirmado','rechazado'))**
- - Integridad referencial:
**OrdenID y ClienteID deben existir en sus tablas respectivas.**
- MontoPago > 0:
**no se permiten pagos nulos o negativos.**  
- EstadoPago:
**limitado a valores predefinidos (CHECK).**
- TarjetaCliente:
**almacenar solo datos parciales (ej. últimos dígitos) para cumplir con normas de seguridad (PCI DSS).**

**- Una Orden puede tener N Pagos (relación 1:N).**

**Ejemplo:**

```sql
CREATE TABLE Pago (
    PagoID INT PRIMARY KEY,
    OrdenID INT NOT NULL,
    ClienteID INT NOT NULL,
    FechaPago DATE NOT NULL,
    MontoPago DECIMAL(10,2) NOT NULL CHECK (MontoPago > 0),
    MetodoPago VARCHAR(30) NOT NULL,
    EstadoPago VARCHAR(20) CHECK (EstadoPago IN ('Pendiente','Confirmado','Rechazado')),
    TarjetaCliente VARCHAR(20),
  CONSTRAINT chkpagomonto   CHECK (monto > 0),
  CONSTRAINT chkpagofecha   CHECK (fechapago <= CURRENTDATE),
  CONSTRAINT chkpagometodo  CHECK (metodo IN ('efectivo','tarjeta','transferencia')),
  CONSTRAINT chkpagoestado  CHECK (estado IN ('pendiente','confirmado','rechazado')),
  CONSTRAINT fkpagoorden    FOREIGN KEY (ordenID) REFERENCES Orden(ordenID)
FOREIGN KEY (OrdenID) REFERENCES Ordenes(OrdenID),
FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID)
);
```

**Entidad ventas**

**Reglas  de negocio Para Integridad De Datos**

- Integridad referencial: 
**ClienteID, OrdenID, SucursalID y EmpleadoID deben existir en sus tablas respectivas.**
- TotalVenta > 0:
**no se permiten ventas con monto cero o negativo.**  
- Moneda:
debe estar limitada a un catálogo de monedas válidas (CHECK). 
**- Una Orden puede generar una o varias Ventas, pero cada Venta debe estar asociada a una Orden existente.**
- FechaVenta:
**no puede ser anterior a la fecha de creación de la orden.**  
- EmpleadoID:
**bligatorio para trazabilidad (quién realizó la venta).**

### **AGREGACIÓN DE REGLAS DE NEGOCIOS A LOS NUEVOS ATRIBUTOS DE LA ENTIDAD VENTAS (TIPO_VENTA, CEDE)**

**📖Nuevos atributos en Ventas**

**- Cede → nombre o código de la sucursal donde se realizó la venta**
**- CiudadID → FOREIGN KEY hacia la entidad Ciudades, que a su vez se conecta con Países.**

**⚖️Reglas de negocio aplicadas**

**- Cede (Sucursal)** 

- Debe existir en la tabla Sucursales (integridad referencial).

- Cada sucursal está asociada a una ciudad y país, lo que permite segmentar ventas por ubicación.  

- Puede usarse para aplicar reglas de negocio específicas (ej. descuentos locales, impuestos regionales).  

**- CiudadID**

- Debe existir en la tabla Ciudades.  

- Permite vincular la venta con su contexto geográfico (ciudad y país).  

- Facilita reportes multinacionales: ventas por ciudad, región, país.  

```sql
📐 /*Ejemplo SQL actualizado*/

CREATE TABLE Ventas (
    VentaID INT PRIMARY KEY,
    OrdenID INT NOT NULL,
    ClienteID INT NOT NULL,
    FechaVenta DATE NOT NULL,
    TotalVenta DECIMAL(12,2) NOT NULL CHECK (TotalVenta > 0),
    Moneda VARCHAR(10) NOT NULL CHECK (Moneda IN ('USD','EUR','DOP','MXN','BRL')),
    SucursalID INT NOT NULL,
    Cede VARCHAR(50) NOT NULL,
    CiudadID INT NOT NULL,
    EmpleadoID INT NOT NULL,
    TipoVenta VARCHAR(20) NOT NULL CHECK (TipoVenta IN ('Minorista','Mayorista','Online','Internacional')),
    ImpuestoAplicado DECIMAL(5,2) NOT NULL CHECK (ImpuestoAplicado >= 0),
    FOREIGN KEY (OrdenID) REFERENCES Ordenes(OrdenID),
    FOREIGN KEY (ClienteID) REFERENCES Clientes(ClienteID),
    FOREIGN KEY (SucursalID) REFERENCES Sucursales(SucursalID),
    FOREIGN KEY (CiudadID) REFERENCES Ciudades(CiudadID),
    FOREIGN KEY (EmpleadoID) REFERENCES Empleados(EmpleadoID)
);
```

**🌍 Escenario multinacional**

**- Métodos de pago deben contemplar variantes globales (ej. PayPal, transferencias internacionales, tarjetas locales).**

**- Estados de pago pueden incluir procesando o en verificación según regulaciones de cada país.**

**- La tabla debe ser escalable para manejar múltiples monedas (ej. agregar campo Moneda).**

**- Cede permite identificar la sucursal exacta (ej. “Sucursal Santo Domingo – Zona Colonial”).**

**- CiudadID conecta con la tabla Ciudades, que a su vez se relaciona con Países, asegurando trazabilidad global.**

**- Esto habilita reportes como:**

**- Ventas por ciudad y país.**

**- Comparación de sucursales dentro de una misma ciudad.**

**- Aplicación automática de impuestos según jurisdicción.**

### **Reglas condicionales y transiciones con CHECK**

- **Transiciones de estado simples**: Usa IN para acotar estados y evita incoherencias con checks compuestos.
  - **Ejemplo**: Orden pagada requiere total **> 0**.

    - **CONSTRAINT** **chkordenpagada_total** **CHECK (NOT (estado = 'pagada' AND total = 0))**
- Dependencias en la misma fila: Puedes condicionar un campo por otro con lógica booleana.
  - Ejemplo: pago “confirmado” requiere método definido:
    - **CONSTRAINT** **chkpagoconfirmado_metodo** **CHECK (NOT (estado = 'confirmado' AND metodo IS NULL))**

Nota: Para reglas que requieren consultar otras tablas (ej. “no vender si stock del producto es 0”), usa triggers/procedimientos, no CHECK.

**Añadir CHECK a tablas existentes**

- Agregar:

```sql
ALTER TABLE Producto
ADD CONSTRAINT chkproductoprecio CHECK (precio >= 0);
```

- Deshabilitar/rehabilitar temporalmente (dependiendo del motor):
  - En SQL estándar se usa ALTER **TABLE** ... **DROP CONSTRAINT** para quitar y volver a crear; algunos motores permiten DISABLE CONSTRAINT.

- Validar datos existentes antes de aplicar: limpia datos que violen la regla para evitar fallos al agregar el **CHECK.**

**Consejos finos para mantener fidelidad al estándar**

- Evita funciones no portables en CHECK (p. ej., expresiones específicas del motor). CURRENT_DATE es estándar; funciones de texto avanzadas pueden no serlo.
- Prefiere almacenar menos derivaciones: si puedes calcular subtotal en consulta, evita persistirlo; si decides persistir, protege con CHECK.
- Combina NOT NULL + CHECK para evitar que NULL salte reglas.
- Nombra todas las constraints para trazabilidad y auditoría.

## **REGLAS DE NEGOCIOS PARTE 3**
