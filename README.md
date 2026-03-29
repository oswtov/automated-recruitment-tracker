# ⚙️ Sistema Automizado de Reclutamiento y Evaluación (Google Sheets)

## 📌 Contexto y Problema de Negocio
El comité de Recursos Humanos de la ONG Espacio Educa gestionaba el reclutamiento de forma manual y fragmentada. El flujo de trabajo requería que cada evaluador utilizara una plantilla de Excel independiente por cada aspirante. Al finalizar las jornadas, el comité recibía múltiples archivos individuales, por ejemplo, 20 archivos Excel distintos para 20 aspirantes. 

Posteriormente, un responsable de RR.HH. debía consolidar manualmente toda la información para comparar perfiles y tomar decisiones. Esta metodología generaba cuellos de botella, pérdida de trazabilidad y una alta probabilidad de errores en la transcripción de los datos.

## 💡 Solución Implementada
Desarrollé una herramienta automatizada en Google Sheets que centraliza el flujo de datos en un solo origen de verdad. La solución eliminó la dispersión de archivos, permitiendo que múltiples evaluadores alimenten una única base de datos. Además, se implementaron funciones para que el comité de RR.HH. visualice los resultados en tiempo real sin necesidad de consolidación manual. El sistema es altamente escalable, diseñado para procesar grandes volúmenes de voluntarios de manera simultánea y optimizar el tiempo de respuesta.

## 🏗️ Arquitectura Técnica y Flujo de Datos

El sistema está construido sobre cuatro pilares fundamentales:

### 1. Panel Maestro: "BDD APLICANTES"
Destino final de la información procesada, diseñado para garantizar la integridad de los datos.
* **Estandarización de Entradas:** Implementación de listas desplegables dinámicas mediante validación de datos en las columnas de Coordinación, Comisión y Representantes. Esto evita variaciones en la escritura y asegura que las fórmulas de búsqueda funcionen sin errores.
* **Mapeo y Conexión:** Diseño de columnas espejo para Score, Observaciones y Ranking, las cuales no se editan manualmente. Utilizan una función `=QUERY()` que conecta esta hoja con la base de datos principal ("BBDD").
* **Logística Centralizada:** Las columnas de Fecha y Horario ofrecen una vista panorámica, eliminando la revisión de calendarios externos.
* **UX y Toma de Decisiones:** Aplicación de Formato Condicional (mapa de calor) en Score y Ranking para resaltar instantáneamente a los candidatos "Aptos" o sobresalientes.

### 2. Front-End Interactiva: "EVALUACION APLICANTES"
Interfaz segura que permite a los evaluadores cargar datos ágilmente sin alterar la base de datos principal.
* **Carga Inteligente de Perfiles:** Uso de `QUERY` vinculado a `IMPORTRANGE` para extraer en tiempo real las competencias a evaluar desde la hoja "REFERENCIA", adaptando el formulario al cargo de forma instantánea.
* **Control de Calidad (Data Validation):** Reglas estrictas que evitan errores de escritura en aplicantes y coordinaciones, y bloquean calificaciones que no estén en el rango numérico del 1 al 5.
* **Automatización con Google Apps Script:** * **Botón ENVIAR:** Valida que los campos críticos estén llenos, transfiere la evaluación a la "BBDD" y genera una marca de tiempo para auditoría.
  * **Botón LIMPIAR:** Rutina que borra el formulario tras un envío exitoso para evaluar al siguiente candidato rápidamente.
* **Veredicto en Tiempo Real:** Fórmulas que calculan la puntuación total y determinan automáticamente si el aspirante aprobó o no.

### 3. El Motor de Datos: "BBDD"
* Repositorio central donde se almacena históricamente la información.
* **Almacenamiento y Trazabilidad:** El script inyecta cada evaluación con un *timestamp* para garantizar la auditoría del proceso.
* **Ciclo de Información:** Alimenta dinámicamente al Panel Maestro, consolidando los resultados de múltiples evaluadores.

### 4. Cimientos del Sistema: "REFERENCIA"
* Tabla maestra y fuente única de validación que garantiza la integridad absoluta del ecosistema.
* **Normalización:** Centraliza los roles a evaluar, los nombres exactos de los comités y las descripciones de las puntuaciones.
* **Garantía de Calidad:** Base exclusiva para todas las listas desplegables, eliminando inconsistencias en toda la base de datos.
* **Motor Dinámico:** Es el origen de los datos que la función `QUERY` consulta para cargar los criterios correctos en el formulario de evaluación.

## 🚀 Impacto del Proyecto
* **Optimización del tiempo:** El desarrollo de esta herramienta ahorró más de 4 horas de trabajo al comité.

## 🧠 Complejidad Técnica y Fórmulas Avanzadas

Este ecosistema supera el uso tradicional de hojas de cálculo, implementando un nivel avanzado de lógica de datos y programación:

* **Consultas Relacionales (`QUERY`):** Uso intensivo de lenguaje de consultas (similar a SQL) para filtrar, reestructurar y poblar dinámicamente el Panel Maestro con los datos limpios de la BBDD, evitando la duplicidad de información.
* **Extracción Dinámica (`QUERY` + `IMPORTRANGE`):** Combinación de funciones para que la interfaz de evaluación consulte en tiempo real la tabla de "REFERENCIA" y cargue únicamente las competencias asociadas al cargo seleccionado.
* **Lógica Condicional Compleja y UX:** Uso de fórmulas de evaluación lógica para emitir veredictos automáticos (Apto/No Apto) basados en puntajes ponderados, reforzado con Formato Condicional (mapas de calor) para facilitar la lectura visual de los rankings.
* **Integridad de Datos (Data Validation):** Implementación de reglas estrictas y listas desplegables anidadas para restringir los *inputs* del usuario (ej. limitar rangos numéricos del 1 al 5 y estandarizar nombres), blindando la base de datos contra errores de tipeo.
* **Automatización con Google Apps Script:** Desarrollo de código en JavaScript para crear macros complejas asignadas a botones de la interfaz:
  * Validación de campos obligatorios antes de procesar el envío.
  * Inserción de nuevas filas en la BBDD con marcas de tiempo (*timestamps*) inmutables para fines de auditoría.
  * Limpieza del Front-End para reiniciar el ciclo de evaluación.
