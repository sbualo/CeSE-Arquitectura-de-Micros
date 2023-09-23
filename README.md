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

A grandes rasgos, los mapas de memoria de la familia tienen las siguentes características : 
suelen contar con un mapa de memoria plano de 4gb
tienen una arquitectura load-store
cuentan con un set de instrucciones mixto, que alterna instrucciones ARM con instrucciones Thumb 1 y Thumb 2
Cuenta con características para trabajar con interrupciones (NVIC), asi como para trabajar con FreeRTOS y bajo consumo
Tiene un sector dedicado a periféricos (0x5FFFFFFF hasta 0x4000000)


### 5. ¿Qué ventajas presenta el uso de los “shadowed pointers” del PSP y el MSP?

La ventaja principal radica en que es posible migrar entre contextos privilegiados y no privilegiados sin tener que realizar un backup del stack comleto: simplemente basta con apuntar el SP hacia la direccion en la que se encuentra el stack de cada zona.


### 6. Describa los diferentes modos de privilegio y operación del Cortex M, sus relaciones y como se conmuta de uno al otro. Describa un ejemplo en el que se pasa del modo privilegiado a no priviligiado y nuevamente a privilegiado.

A grandes rasgos, los cortex M trbajan con 2 grandes modos de operaciones : handler o Thread. Dentro del modo thread, se puede distinguir entre el modo privilegiado y no privilegiado, para el cual la mayor diferencia radica en qué zonas de memoria se puede acceder o trabajar (coordinadas por FreeRTOS).
El microcontrollador por defecto, luego de su secuencia de inicializacion siempre comienza en modo thread privilegiado.
Para el caso de programacion baremetal, el programa siempre esta swicheando entre thread privilegiado y modo handler.
Si se implementa RTOS, es posible migrar al modo no privilegiado escribiendo en el control register. Es importante destacar, que el modo privilegiado puede migrar inmediatamente al modo no privilegiado, pero para darle el control de nuevo al thread privilegiado, solo lo puede hacer el modo handler.


### 7. ¿Qué se entiende por modelo de registros ortogonal? Dé un ejemplo


Se entiende por modelo ortogonal de registros a aquellos que, teniendo asignados diferentes bits para diferentes configuraciones del micro, se pueden utilizar/configurar de forma independiente.
Un ejemplo de esto es el registro CONTROL, el cual tiene los bits asociados a context switching. En teoría, gracias a la ortogonalidad, es posible asignar el nivel thread y su SP correspondiente de forma libre (lo que daría hasta 4 combinaciones diferentes). Sin embargo, por regla definida, el modo no privilegiado no puede hacer uso del main SP.

### 8. ¿Qué ventajas presenta el uso de intrucciones de ejecución condicional (IT)? Dé un ejemplo

La ventaja principal radica en que este tipo de ejecuciones permite mantener la ejecucion de un thread mientras se está esperando que se cumpla la condicion, sin bloquear el programa. Un ejemplo puede ser el de la finalizacion de envio de una información a través de un periférico.


