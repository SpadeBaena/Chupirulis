# Sistema Web de Gestión de Asistencias (SWGA) - Hackatón
## 1. Visión General del Proyecto

El **Sistema Web de Gestión de Asistencias (SWGA)** es una aplicación diseñada para automatizar y validar el registro de asistencia en un entorno académico.

Utilizamos una arquitectura robusta para manejar el alto volumen de datos generado por el registro de asistencia mediante **código QR** y validación por **Face ID**.

| Componente | Tecnología | Propósito |
| :--- | :--- | :--- |
| **Base de Datos** | **PostgreSQL (v17) en Docker** | SGBD robusto para garantizar la integridad y escalabilidad de los registros de asistencia. |
| **Backend** | *(Definir aquí: Python/Node.js/PHP)* | Lógica de validación de QR/Face ID y manejo de las consultas SQL. |
| **Cliente DB** | pgAdmin 4 / DBeaver | Administración visual de la base de datos. |

---

##  2. Diseño de la Base de Datos (DB)

El diseño se basa en el **Modelo Entidad-Relación (MER)** provisto, optimizado para rastrear alumnos, grupos, horarios, marcajes crudos y justificaciones.

### A. Estructura Clave

| Tabla | Función Principal | Campo Crítico |
| :--- | :--- | :--- |
| **`TblMarcajeRaw`** | Registro crudo de cada intento de escaneo (QR/Face ID). | `dtHoraMarcaje` |
| **`TblAsistencias`** | Registro formal y final de la asistencia de un alumno a una sesión. | `IntSesionClase` + `IntAlumno` |
| **`TblGrupos`** | Une profesores y materias con una cohorte de alumnos (`TblAlumnosGrupos`). | `IntProfesor` |
| **`TblDevices`** | Catálogo de los dispositivos de escaneo (para auditoría). | `strDeviceSN` |

### B. Indexación para Rendimiento

Se han añadido índices específicos para acelerar las consultas más frecuentes en un sistema de asistencia:

```sql
-- Búsqueda de identidad
CREATE UNIQUE INDEX idx_alumnos_matricula ON TblAlumnos (strMatricula);
CREATE UNIQUE INDEX idx_devices_sn ON TblDevices (strDeviceSN);

-- Optimización de alto volumen (Asistencias)
CREATE UNIQUE INDEX idx_asistencias_sesion_alumno ON TblAsistencias (IntSesionClase, IntAlumno);
CREATE INDEX idx_marcaje_hora ON TblMarcajeRaw (dtHoraMarcaje);
