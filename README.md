practica_5_noviembre - Sistema de Activos Fijos

API REST (Spring Boot 4.1.1, Java 21, PostgreSQL, Gradle) migrada desde el sistema dBase VSIAF.

Ejecución
Crear la BD vsiaf en PostgreSQL.
Definir las variables de entorno DB_URL, DB_USERNAME y DB_PASSWORD.
./gradlew bootRun (en Windows: gradlew.bat bootRun).
Probar los endpoints en http://localhost:8080/swagger-ui.html.
python scripts/dbf_a_csv.py y psql -U postgres -d vsiaf -f db/importar.sql
5 Restricciones
No modificar db/dbf/ ni ejecutar DELETE/DROP sobre la BD sin confirmación explícita.
Los controllers nunca exponen entidades: solo DTOs (los nuevos como record de Java 21).
Sin credenciales en el código: usar variables de entorno (DB_URL, DB_USERNAME, DB_PASSWORD); no subir .env.
No agregar dependencias ni cambiar la versión de Java/Spring Boot sin aprobación.
ddl-auto=update solo en desarrollo; no editar migraciones ya aplicadas.
5 Reglas para las entidades
Una entidad por tabla en Entities/, con @Table(name) y @Column(name) explícitos en minúscula.
Clave compuesta con @IdClass (con equals/hashCode); sin clave natural, id con IDENTITY.
Prohibido @Data en entidades: usar @Getter/@Setter; sin toString que dispare relaciones lazy.
Relaciones LAZY por defecto; nunca CascadeType.ALL ni REMOVE (los activos requieren trazabilidad).
Montos con BigDecimal (precision 15, scale 2), fechas con LocalDate; nunca double/float.
5 Análisis obligatorios (antes de escribir código)
Leer el esquema real (db/dbf o la BD) y confirmar claves y tipos antes de crear o cambiar una entidad.
Listar las capas y archivos afectados: Entities, Repositories, dto, service, Controllers.
Revisar relaciones e impacto en datos existentes (bajas, reval y trasfe dependen de activo).
Validar las reglas contables (vida útil, depreciación acumulada, UFV) antes de tocar cálculos.
Presentar un plan y esperar aprobación si el cambio toca más de 3 archivos o el modelo de datos; al terminar, ./gradlew clean build y tests.
5 Objetivos
Compilar sin errores en Java 21 (./gradlew clean build).
CRUD funcionando en las 25 tablas del sistema.
Migrar los datos DBF a PostgreSQL sin pérdida (mismos conteos por tabla).
Mapear las relaciones con @ManyToOne y FKs en la BD.
Agregar seguridad (login con JWT) y tests de servicios.
