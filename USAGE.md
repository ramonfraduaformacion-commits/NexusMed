## 🚀 Guía de Uso y Demostración

El sistema expone una interfaz interactiva basada en **Swagger UI**, permitiendo probar el ciclo de vida del dato clínico de forma inmediata.

### 1. Exploración de la API (FastAPI + Swagger)

La documentación interactiva y el sandbox de pruebas están disponibles en la ruta `/docs`.

* **GET** `/fhir/Patient/{dni}`: Recupera un recurso **FHIR Patient**. Realiza una búsqueda por identidad legal, ejecuta un mapeo semántico de géneros y normaliza direcciones y telecomunicaciones desde SQL Server al estándar JSON FHIR.
* **GET** `/fhir/Practitioner/{id}`: Extrae el perfil de un **Profesional Sanitario**. Incluye la gestión de cualificaciones, títulos académicos y números de colegiado, transformando la estructura jerárquica de la BD en un recurso `Practitioner` válido.
* **POST** `/fhir/Patient`: Orquesta el **Alta de Pacientes**. Valida la integridad del JSON de entrada mediante modelos Pydantic, verifica la no duplicidad por DNI y persiste la información en el sistema mediante procedimientos almacenados de alta seguridad.
* **PUT** `/fhir/Patient/{id}`: Gestiona la **Actualización Integral**. Permite la sincronización de datos clínicos y de contacto, garantizando que los cambios en el recurso FHIR se reflejen de forma consistente en el modelo relacional.
* **DELETE** `/fhir/Patient/{id}`: Ejecuta un **Borrado Lógico (Soft Delete)**. En cumplimiento con la normativa sanitaria de conservación de historias clínicas, el sistema marca al paciente como inactivo en lugar de destruir el registro físico.
* **GET** `/LOINC/{code}`: Servicio de **Terminología Clínica**. Consulta el diccionario LOINC para obtener definiciones normalizadas de pruebas de laboratorio y observaciones, facilitando la interoperabilidad semántica.

### 2. Capacidades Avanzadas del Sistema

* **Gobernanza y Trazabilidad Dual**: El sistema implementa una auditoría de bajo nivel (`Security Log`) para accesos de red y una auditoría funcional (`Log de Operaciones`) que registra el éxito o fallo de cada transacción clínica directamente en la BD.
* **Interoperabilidad Semántica Integrada**: Capacidad nativa para resolver códigos LOINC, permitiendo que los datos locales se entiendan en cualquier red de salud internacional.
* **Capa de Abstracción de Datos (DAL)**: El acceso a datos está desacoplado mediante **Stored Procedures**. Esto blinda la base de datos contra inyecciones SQL y separa la lógica de negocio de la estructura de las tablas.
* **Validación de Esquemas y Sanitización**: Uso de tipado estricto con Pydantic para filtrar caracteres inválidos, normalizar formatos de fecha y asegurar que el sistema solo procese datos que cumplan el estándar FHIR.

### 3. Simulación de Flujo MLLP (HL7 v2)

El repositorio incluye un script de testeo (`test_mllp_sender.py`) que simula un sistema externo (SIA) enviando un mensaje **ADT^A01**.

* **Acción**: El servidor recibe el mensaje, genera un **ACK (AA)** y realiza la inserción en la BD.
* **Verificación**: Puedes consultar la tabla `HL7_Messages` para ver el payload original y la tabla `Audit_Logs` para ver la trazabilidad de la IP emisora.

<div style="page-break-after: always;"></div>

### 4. Integración de Terminologías

En el endpoint de **Observations**, el sistema requiere códigos **LOINC** (ej. `8867-4` para Heart Rate). Si el código no existe en las tablas auxiliares de terminología, el sistema devuelve un error de validación semántica, demostrando la robustez del modelo de datos. Se ha implementado un método **GET** para obtener los datos más significativos del sistema **LOINC** como son su código, denonimación oficial, unidades oficiales(en caso de existencia), estado del término(activo, desfasado,...) y por último su traducción al castellano(en caso de existir).

---

## 🛠️ Stack Tecnológico

* **Lenguaje:** Python 3.13 (Optimizado para tipos de datos modernos).
* **Base de Datos:** SQL Server (T-SQL) con uso intensivo de `UNIQUEIDENTIFIER` y `JSON support`.
* **Framework Web:** FastAPI (Asíncrono y de alto rendimiento).
* **Protocolos:** MLLP (TCP/IP), HTTP/HTTPS.
* **Estándares:** HL7 v2.x, FHIR R4/R5, DICOM, SNOMED CT, LOINC.

---
<div style="page-break-after: always;"></div>

## 🔍 Análisis Crítico del Desarrollador (Self-Assessment)

Como experto en el área, he diseñado este proyecto priorizando tres pilares que suelen fallar en los desarrollos sanitarios comerciales:

1. **Desacoplamiento:** El motor de parsing HL7 es independiente de la base de datos. Podrías cambiar SQL Server por PostgreSQL y solo tendrías que tocar la capa de persistencia.
2. **Auditabilidad:** En salud, "si no está registrado, no ocurrió". El uso de `Audit_Logs` no es un añadido, es el núcleo del sistema para cumplir con normativas **HIPAA/GDPR**.
3. **Semántica:** A diferencia de otros simuladores que usan texto plano, este sistema obliga al uso de **Sistemas de Codificación**. No aceptamos "Tensión Arterial", aceptamos el código LOINC correspondiente.

---
