# practica_5_noviembre - Sistema de Activos Fijos

API REST (Spring Boot 4.1.1, **Java 21**, PostgreSQL, **Gradle**) migrada desde el sistema dBase VSIAF.

## Ejecución
1. Crear la BD `vsiaf` en PostgreSQL.
2. Definir las variables de entorno `DB_URL`, `DB_USERNAME` y `DB_PASSWORD`.
3. `./gradlew bootRun` (en Windows: `gradlew.bat bootRun`).
4. Probar los endpoints en `http://localhost:8080/swagger-ui.html`.
5. `python scripts/dbf_a_csv.py` y `psql -U postgres -d vsiaf -f db/importar.sql`

## 5 Restricciones
1. No modificar `db/dbf/` ni ejecutar `DELETE`/`DROP` sobre la BD sin confirmación explícita.
2. Los controllers nunca exponen entidades: solo DTOs (los nuevos como `record` de Java 21).
3. Sin credenciales en el código: usar variables de entorno (`DB_URL`, `DB_USERNAME`, `DB_PASSWORD`); no subir `.env`.
4. No agregar dependencias ni cambiar la versión de Java/Spring Boot sin aprobación.
5. `ddl-auto=update` solo en desarrollo; no editar migraciones ya aplicadas.

## 5 Reglas para las entidades
1. Una entidad por tabla en `Entities/`, con `@Table(name)` y `@Column(name)` explícitos en minúscula.
2. Clave compuesta con `@IdClass` (con `equals`/`hashCode`); sin clave natural, `id` con `IDENTITY`.
3. Prohibido `@Data` en entidades: usar `@Getter`/`@Setter`; sin `toString` que dispare relaciones lazy.
4. Relaciones `LAZY` por defecto; nunca `CascadeType.ALL` ni `REMOVE` (los activos requieren trazabilidad).
5. Montos con `BigDecimal` (precision 15, scale 2), fechas con `LocalDate`; nunca `double`/`float`.

## 5 Análisis obligatorios (antes de escribir código)
1. Leer el esquema real (`db/dbf` o la BD) y confirmar claves y tipos antes de crear o cambiar una entidad.
2. Listar las capas y archivos afectados: Entities, Repositories, dto, service, Controllers.
3. Revisar relaciones e impacto en datos existentes (`bajas`, `reval` y `trasfe` dependen de `activo`).
4. Validar las reglas contables (vida útil, depreciación acumulada, UFV) antes de tocar cálculos.
5. Presentar un plan y esperar aprobación si el cambio toca más de 3 archivos o el modelo de datos; al terminar, `./gradlew clean build` y tests.

## 5 Objetivos
1. Compilar sin errores en Java 21 (`./gradlew clean build`).
2. CRUD funcionando en las 25 tablas del sistema.
3. Migrar los datos DBF a PostgreSQL sin pérdida (mismos conteos por tabla).
4. Mapear las relaciones con `@ManyToOne` y FKs en la BD.
5. Agregar seguridad (login con JWT) y tests de servicios.
