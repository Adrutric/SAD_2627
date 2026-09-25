# Práctica UD1 — Auditoría de vulnerabilidades con Nessus
### Caso: Metasploitable2

# Informe de auditoría de vulnerabilidades 

## 1. Contexto y alcance
(Qué se ha analizado y por qué)

## 2. Metodología
(Herramienta usada, tipo de escaneo, objetivo analizado)
La herramienta que se ha utilizado es Nessus, 
## 3. Resumen de resultados
(Cuántas vulnerabilidades por severidad — puedes usar una tabla o una lista)

## 4. Vulnerabilidades clasificadas
Vulnerabilidad | Severidad / CVSS | Origen | Descripción
------------ | ------------- | ------------ | -------------
Debian OpenSSH/OpenSSL Package Random Number Generator Weakness (SSL check) | Critacial / 10-0 * | Implementación | La clave de host SSH remota se ha generado en un sistema Debian o Ubuntu que contiene un fallo (bug) en el generador de números aleatorios de su librería OpenSSL.El problema se debe a que un empaquetador de Debian eliminó casi todas las fuentes de entropía en la versión remota de OpenSSL.Un atacante puede obtener fácilmente la parte privada de la clave remota y utilizarla para descifrar la sesión remota o llevar a cabo un ataque de "hombre en el medio" (Man-in-the-Middle) 

Content column 1 | Content column 2 | Content cell 1 | Content cell 2

## 5. Análisis en profundidad
(El desarrollo del paso 8: qué es, cómo se explota, cómo se mitiga, referencia)

## 6. Recomendaciones
(2-3 acciones concretas que recomendarías, ordenadas por prioridad)

## 7. Conclusión
(Una valoración breve: ¿firmarías el contrato de mantenimiento sabiendo lo que sabes ahora? ¿Con qué condiciones?)
