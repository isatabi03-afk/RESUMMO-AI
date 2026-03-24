# Proteccion de Estructura del Workspace

## Regla principal
La estructura del workspace se estabiliza despues de Fase 1.
Cualquier cambio estructural requiere validacion explicita.

## Que se considera cambio estructural
- Crear, renombrar o eliminar directorios en primer nivel (landing/, automations/, content/, data/, docs/)
- Crear, renombrar o eliminar subdirectorios dentro de .claude/ (rules/, skills/, agents/)
- Modificar la jerarquia de subdirectorios existentes
- Agregar o quitar archivos de configuracion en .claude/ (CLAUDE.md, settings.json)
- Cambiar convenciones de naming

## Que NO se considera cambio estructural (permitido sin validacion)
- Agregar archivos dentro de directorios existentes (nuevo workflow, nuevo reporte)
- Editar contenido de archivos existentes (copy, configs, docs)
- Agregar archivos de codigo en landing/src/
- Actualizar memorias en memory/

## Protocolo ante cambio estructural detectado
1. ADVERTIR al usuario: "Esto modifica la estructura del workspace"
2. Explicar QUE cambia y POR QUE
3. Clasificar: es mejora, fix de bug, o reestructuracion?
4. Esperar aprobacion explicita antes de ejecutar
5. Si se aprueba: actualizar CLAUDE.md si la estructura documentada cambio
