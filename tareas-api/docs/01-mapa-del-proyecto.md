# Mapa del Proyecto: tareas-api

## 1. Qué hace el proyecto

Este proyecto es un gestor de tareas pendientes (to-do list) implementado en Java 17 con persistencia en memoria. Implementa una arquitectura en capas (modelo, repositorio, servicio) para administrar tareas con prioridades, fechas límite y estados de completitud.

## 2. Tabla de clases

| Clase | Responsabilidad | Depende de |
|-------|----------------|------------|
| `Tarea` | Entidad que representa una tarea con propiedades inmutables (título, descripción, prioridad, fecha límite) y estado mutable (completada) | `Prioridad` |
| `Prioridad` | Enumeración que define los niveles de prioridad (BAJA, MEDIA, ALTA) | ninguna |
| `TareaNoEncontradaException` | Excepción lanzada cuando se intenta operar sobre una tarea inexistente | ninguna |
| `TareaRepositorio` | Capa de persistencia en memoria usando LinkedHashMap; asigna IDs secuenciales automáticamente | `Tarea` |
| `TareaServicio` | Capa de lógica de negocio que implementa casos de uso (crear, completar, listar, generar reportes) | `TareaRepositorio`, `Tarea`, `Prioridad`, `TareaNoEncontradaException` |
| `App` | Aplicación de demostración por consola que muestra el uso del sistema | `TareaServicio`, `TareaRepositorio`, `Prioridad` |

## 3. Cómo se compila y se prueba

```bash
# Ejecutar todas las pruebas unitarias
mvn test

# Ejecutar la aplicación de demostración
mvn -q exec:java

# Limpiar y compilar el proyecto completo
mvn clean package

# Ejecutar una clase de pruebas específica
mvn test -Dtest=TareaRepositorioTest

# Ejecutar un método de prueba específico
mvn test -Dtest=TareaServicioTest#completar
```

## 4. Tres cosas sospechosas o incompletas

### 4.1. Validación de título faltante
**Archivo:** `src/main/java/mx/generation/tareas/Tarea.java:19`

El constructor de `Tarea` tiene un comentario TODO indicando que falta validar que el título no venga vacío o en blanco. Actualmente se acepta `""` como título válido, lo cual podría permitir la creación de tareas sin nombre.

### 4.2. Cálculo invertido de días restantes
**Archivo:** `src/main/java/mx/generation/tareas/TareaServicio.java:67`

El método `diasRestantes()` calcula `ChronoUnit.DAYS.between(tarea.getFechaLimite(), hoy)`, lo cual está invertido. Esto causa que retorne valores negativos para fechas futuras y valores positivos para fechas pasadas, contradiciendo la documentación del método que dice "positivo si está en el futuro, negativo si ya se venció".

### 4.3. Comparación excluye prioridad mínima solicitada
**Archivo:** `src/main/java/mx/generation/tareas/TareaServicio.java:51`

El método `listarPorPrioridadMinima()` usa `t.getPrioridad().ordinal() > minima.ordinal()`, con el operador `>` en lugar de `>=`. Esto excluye las tareas que tienen exactamente la prioridad mínima solicitada. Por ejemplo, si se pide `Prioridad.MEDIA`, solo retorna las de prioridad ALTA, excluyendo las MEDIA.

### 4.4. Búsqueda case-sensitive a pesar del comentario
**Archivo:** `src/main/java/mx/generation/tareas/TareaRepositorio.java:44`

El método `buscarPorTitulo()` usa `t.getTitulo().contains(texto)` sin convertir ambos strings a minúsculas, haciéndola case-sensitive. Sin embargo, el comentario en la línea 38-39 promete que la búsqueda no distingue mayúsculas de minúsculas. El ejemplo "informe encuentra Entregar INFORME mensual" no funcionaría en la implementación actual.
