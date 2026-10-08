# CLAUDE.md

## Stack

- **Java 17** con Maven 3.8+
- **JUnit 5** (Jupiter) para pruebas
- Arquitectura en capas: Modelo → Repositorio → Servicio
- Persistencia en memoria (LinkedHashMap)

## Comandos

```bash
# Compilar y ejecutar todas las pruebas
mvn test

# Ejecutar la aplicación de demostración
mvn -q exec:java

# Limpiar y empaquetar
mvn clean package
```

## Convenciones

- **Idioma**: Todo el código y pruebas en español (nombres de clases, métodos, variables, comentarios)
- **Pruebas**: JUnit 5 con `@Test` (no JUnit 4). Una prueba por comportamiento, no por método
- **Dependencias**: No agregar nuevas dependencias sin consultar primero
- **Nombres**: Métodos de prueba descriptivos que explican el escenario: `debeLanzarExcepcionCuandoTareaNoExiste()`
- **Estilo**: Preferir claridad sobre brevedad; evitar abreviaciones en nombres

## Reglas de trabajo

**CRÍTICO**: Todo cambio debe dejar `mvn test` en verde. Si una modificación rompe pruebas existentes, hay que arreglarlas antes de considerar el trabajo completo.

## Arquitectura

- `Tarea`: Entidad inmutable excepto por el estado `completada`
- `TareaRepositorio`: Asigna IDs automáticamente (1, 2, 3...); `buscar()` retorna `null` si no encuentra
- `TareaServicio`: Lógica de negocio; métodos como `completar()` lanzan `TareaNoEncontradaException` si el ID no existe
- `App`: Demo de consola (no es punto de entrada para producción)
