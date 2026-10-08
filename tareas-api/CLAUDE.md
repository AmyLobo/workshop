# CLAUDE.md

Este archivo proporciona contexto a Claude Code (claude.ai/code) para trabajar en este repositorio.

## Descripción del Proyecto

**tareas-api** es un sistema gestor de tareas (to-do list) desarrollado en Java 17 para la mentoría "Programar con Claude" de Generation México (CH70 Java). Se trata de un proyecto de práctica que demuestra la implementación de una arquitectura en capas con persistencia en memoria.

## Comandos de Desarrollo

```bash
# Ejecutar todas las pruebas unitarias
mvn test

# Ejecutar una clase de pruebas específica
mvn test -Dtest=TareaRepositorioTest

# Ejecutar un método de prueba específico
mvn test -Dtest=TareaServicioTest#completar

# Ejecutar la aplicación de demostración (App.java)
mvn -q exec:java

# Limpiar y compilar el proyecto completo
mvn clean package
```

## Arquitectura del Sistema

El sistema implementa una arquitectura de tres capas dentro del paquete `mx.generation.tareas`:

**Capa de Modelo**
- `Tarea` - Entidad que representa una tarea con propiedades inmutables (titulo, descripcion, prioridad, fechaLimite). Únicamente el estado `completada` es mutable mediante el método `marcarCompletada()`.
- `Prioridad` - Enumeración con valores: BAJA, MEDIA, ALTA. El valor ordinal es relevante para operaciones de comparación.

**Capa de Repositorio**
- `TareaRepositorio` - Proporciona persistencia en memoria mediante `LinkedHashMap<Integer, Tarea>`.
- Asigna identificadores secuenciales automáticamente comenzando en 1 (id=0 indica que la tarea no ha sido persistida).
- El método `buscar(id)` retorna `null` cuando no encuentra la tarea (no lanza excepción).

**Capa de Servicio**
- `TareaServicio` - Implementa la lógica de negocio para las operaciones sobre tareas.
- El método `completar(id)` lanza `TareaNoEncontradaException` cuando la tarea no existe (comportamiento diferente al repositorio que retorna null).
- El método `generarReporte()` construye un reporte textual con estadísticas del sistema.

**Aplicación de Demostración**
- `App.java` - Aplicación de consola para demostración. No representa un punto de entrada para producción.

## Consideraciones Técnicas Importantes

- **Asignación de Identificadores**: El repositorio asigna el ID al momento de persistir por primera vez. Previo a la persistencia, `tarea.getId() == 0`.
- **Validaciones Pendientes**: La línea 19 en `Tarea.java` documenta que actualmente se aceptan títulos vacíos, pero esta validación debe implementarse.
- **Cálculo de Fechas**: El método `TareaServicio.diasRestantes()` contiene lógica invertida (línea 67) - retorna valores negativos para fechas futuras.
- **Construcción de Cadenas**: El método `generarReporte()` utiliza concatenación de strings (`r = r + ...`) en lugar de `StringBuilder`.

## Estrategia de Pruebas

- Framework: JUnit 5 (Jupiter) - utilizar anotación `@Test` de JUnit 5, no las de JUnit 4.
- Algunas pruebas están marcadas con `@Disabled` de manera intencional para el progreso del workshop.
- La estructura de las clases de prueba replica la estructura del código fuente: `TareaRepositorioTest`, `TareaServicioTest`.
