## 🚀 Guía de Uso y Demostración

El sistema expone una interfaz interactiva basada en **Swagger UI**, permitiendo probar el ciclo de vida del dato clínico de forma inmediata.

### 1. Exploración de la API (FastAPI + Swagger)

Al levantar el servicio, la documentación automática está disponible en `/docs`.

* **GET `/patients/{id}**`: Recupera un recurso **FHIR Patient** completo, transformando los datos relacionales de SQL Server a JSON estándar.
* **POST `/patients/**`: Permite el alta de nuevos pacientes validando el esquema contra modelos Pydantic.
* **PUT/DELETE**: Operaciones de actualización y borrado lógico (cumplimiento de integridad referencial).

### 2. Simulación de Flujo MLLP (HL7 v2)

El repositorio incluye un script de testeo (`test_mllp_sender.py`) que simula un sistema externo (SIA) enviando un mensaje **ADT^A01**.

* **Acción**: El servidor recibe el mensaje, genera un **ACK (AA)** y realiza la inserción en la BD.
* **Verificación**: Puedes consultar la tabla `HL7_Messages` para ver el payload original y la tabla `Audit_Logs` para ver la trazabilidad de la IP emisora.

<div style="page-break-after: always;"></div>

### 3. Integración de Terminologías

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

### ¿Cuál es tu siguiente paso?

Con este README, tu perfil en GitHub pasa de "aprendiz" a **"Especialista en Interoperabilidad"**.

Para cerrar con broche de oro, ¿te gustaría que te ayude a redactar una **"Guía de Contribución"** o una sección de **"Futuros Desarrollos"**? En esta última podríamos poner cosas como "Implementación de SMART on FHIR" o "Módulo de IA para predicción de reingresos basada en SNOMED", lo cual demuestra que tienes visión de futuro para el producto.