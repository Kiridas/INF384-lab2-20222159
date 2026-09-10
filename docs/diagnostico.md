# Diagnóstico

## 1. Defecto
Se usa requirements.txt en vez de lock (pipeline.yml, install deps): no fija versiones exactas. Se pierde reproducibilidad de la build.

## 2. Defecto
Sin cache de dependencias (pipeline.yml, setup Python / install deps): cada corrida descarga todo desde cero, dos veces. Se pierde estabilidad del tiempo de ejecución.

## 3. Defecto
Sonar sin Quality Gate (pipeline.yml, analisis de calidad): se envía el análisis pero nadie lee ni bloquea por su resultado. Se pierde que verde signifique que pasó el estándar de calidad.

## 4. Defecto 
publicar no depende de validar (pipeline.yml, job publicar): falta needs. Se pierde que el artefacto publicado haya pasado pruebas y análisis antes de salir.

# Defecto que explica duración
Entiendo que el defecto 2 es el que explica los tiempos, si por cada ejecución va a instalar dependencias cada vez que se corre entonces naturalmente cada corrida durará mucho en lugar de tener ya guardado lo que va a utilizar.

# Vínculo con CASO 2
El defecto 3, Sonar sin Quality Gate. La restriccion real del flujo no es el pipeline tecnico, es la revision manual de seguridad y el CAB. De los 14 hallazgos del trimestre, 11 son librerias de terceros desactualizadas, justo lo que un gate automatico de dependencias detecta sin intervencion humana. Automatizar eso descarga a la revision manual, que es el recurso mas restringido del flujo.

# Métrica DORA
Alcanzables sin tocar el despliegue: Lead Time for Changes y Change Failure Rate. Deployment Frequency y MTTR dependen de la politica del CAB, la ventana de despliegue y el proceso de rollback, no de un cambio dentro del pipeline de CI.
Elegimos Lead Time for Changes, porque la etapa de seguridad y la preparacion del expediente son la parte mas pesada del ciclo completo.

# El proxy
Para validar el cambio de la actualización del pipeline se usará la linea base .md en la que se registraron las ejecuciones antes de los cambios a realizar
