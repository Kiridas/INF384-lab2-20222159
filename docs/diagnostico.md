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
Para validar el cambio de la actualización del pipeline se usará la linea base .md en la que se registraron las ejecuciones antes de los cambios a realizar. Por lo mismo el tiempo medio de dichas ejecuciones es:
- 1m 24s

# ACTUALIZACIÓN Parte 4

# Medición posterior
Tras ejecutar 3 veces el pipeline en main se obtuvo:
- 1m 19s
- 1m 24s
- 1m 21s

Ahora bien el promedio resulta:
- 1m 23s

Que se puede observar es menor (aunque solo por 1 segundo) a 1m 24s. No es una proporción grande pero se debe considerar los cambios de calidad que vienen de por medio.

# Lo que no se resolvió
El Quality Gate en rojo no bloquea el merge en sí. El botón Merge pull request seguía habilitado aunque el check falló, porque nada en GitHub obliga a que ese check pase antes de fusionar. El pipeline avisa, pero no impide. Para resolverlo hace falta configurar branch protection rules sobre main (Settings, Branches, Add rule), marcando pipeline / Validar como required status check. Eso se trata de una configuración del repositorio, no del workflow.

# Justifiación de la versión
La versión pasó de 1.2.0 a 1.3.0, un bump menor (minor), no mayor ni parche.
Desde 1.2.0 hubo un commit que agrega funcionalidad nueva sin romper compatibilidad (el desglose de la tarifa), lo que justifica subir el número intermedio (minor), no el mayor. Ningún commit modifica o elimina comportamiento existente, así que no corresponde un cambio mayor. Los fix que aparecen junto a ese feat no cambian la versión por sí solos, quedan incluidos en el mismo bump. Los commits de configuración del pipeline, de Sonar o de documentación no tocan el código de la librería, así que no se consideran para el versionado. Por eso la versión pasa de 1.2.0 a 1.3.0.

# Declaración de uso de IA Generativa
Se reconoce el uso de inteligencia artificial Claude. principalmente para la detección de defectos, se usó como base 3 defectos los cuales fueron:
- No se usa requeriments.lock
- No se recibe el análisis de seguridad
- No se tiene relación de need entre los steps

De esa manera se obtuvo el cuarto defecto (caché) con lo que se complementó la documentación.
Prompt utilizado: "Se mapearon lo siguientes defectos: El uso del requeriments txt en lugar del lock. El análisis no recibe una respuesta porque no hay un step que reciba una respuesta. El job publicar no depende de validar. Se deben identificar 4 defectos, considerando que te estoy enviando el .yml correspondiente, encuentras algún cuarto defecto?"

Posterior a eso se utilizó la IA para consultar información sobre versionamiento y poder documentar el motivo del cambio de 1.2.0 a 1.3.0 
