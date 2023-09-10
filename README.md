# CeSE-Arquitectura-de-Micros
Repositorio de la materia de arquitectura de microprocesadores correspondiente a la Especialización en Sistemas Embebidos

### 1) Describa brevemente los diferentes perfiles de familias de microprocesadores/microcontroladores de ARM. Explique alguna de sus diferencias características.

Siendo que ARM es una empresa que no fabrica semiconductores, sino que diseña las arquitecturas de los diferentes microcontroladores, posee una familia variada para adaptarse a las distintas situaciones. Entre sus mas destacadas, se encuentran los procesadores de la familia A, familia R, y familia M. Los de familia A (aplication) son procesadores de alto rendimiento, orientados a grandes niveles de performance con altos niveles de paralelismo. Suelen estar orientados a sistemas operativos embebidos, y suelen poseer varios nucleos.

Los  de la familia R (Realtime) estan orientados a sistemas de tiempo real donde es necesario implementar soluciones de baja latencia, alta predictibilidad y alta capacidad de cómputo, como lo suelen ser sistemas críticos.

Los de la familia M (Microcontroller) Son procesadores orientados a dispositivos de consumo masivo y sistemas embebidos compactos. Están diseñados para alta densidad de código y ser programados en C.

## Cortex M

### 1. Describa brevemente las diferencias entre las familias de procesadores Cortex M0, M3 y M4.

Los cortex M0 son microcontroladores pequeños, diseñados para implementaciones de bajo consumo, bajo costo y baja performance. Suelen tener un set de instrucciones reducida, una menor capacidad de computo y perifericos limitados.

Los M3 y M4 comparten muchas funcionalidades entre si. Son micros mas eficientes y performantes de filosofía RISC,sus instrucciones son de 32 bits y son de una arquitectuar harvard Load-Store. No obstante su gran diferencia radica en que los M4 cuentan con unidad de punto flotante y DSP.


### 2. ¿Por qué se dice que el set de instrucciones Thumb permite mayor densidad de código?

El set de instrucciones Thumb esta orientado a ser un set de instrucciones mas intuitivas. Esto permite no solo una mejor lectura del código, sino que tambine la longitud del mismo se ve acortada. Para ponerlo en ejemplos: supongamos que tengo que realizar una multiplicacion 5x5. Con la filosofía thumb, yo tendria una simple instruccion que multiplicaría ambos valores, pero para otro conjunto, quizas debería de concatenar sumas para lograr el objetivo. 

No obstante, este tipo de instrucciones suele acarrear un tiempo de ejecucion mas lento, ya que las intstrucciones mas complejas no suelen ser eficientes.

### 3. ¿Qué entiende por arquitectura load-store? ¿Qué tipo de instrucciones no posee este tipo de arquitectura?

Se entiendo como arquitectura load-store a todas aquellas que no son capaces de modificar un valor directamente en memoria, sino que para ello deben traer el valor a un registro, editarlo y volverlo a guardar. Todos los integrados de este estilo carecen del set de instrucciones de modificacion propia en memoria.

### 4. ¿Cómo es el mapa de memoria de la familia?

