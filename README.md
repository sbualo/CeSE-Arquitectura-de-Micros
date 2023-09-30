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


### 9. Describa brevemente las excepciones más prioritarias (reset, NMI, Hardfault).

La excepcion de reset es principalmente un reset del microcontrolador a nivel de software, llevando todos los registros a su estado inicial. Si bien su funcion es similar al reset por desenergizado, este NO reinicia los distintos estados de la memoria.
Por otro lado, NMI (non mascarable interruptions) se genera por un evento externo que no puede ser ignorado por el procesador. Esta excepción se utiliza para manejar eventos críticos, como una falla de hardware o una interrupción de un dispositivo externo.
Por ultimo, Hardfault se genera por una condición de error en el procesador. Esta excepción se utiliza para manejar errores graves, como una violación de acceso a la memoria o un desbordamiento de pila.


### 10.Describa las funciones principales de la pila. ¿Cómo resuelve la arquitectura el llamado a funciones y su retorno?
Las funcion principal de la pila o stack es la siguiente: respaldar el valor de los registros previos a un llamado de una función, para que ésta pueda usarlos sin problema, y al momento de devolver el control al programa, no haber perdido informacion. Como funcion secundaria, tambien es posible enviar variables a las funciones a traves de la pila. La arquitectura por defecto pushea los 4 primeros registros, ya que asume que todos los valores que se le pasan a la funcion se envian por alli. En caso de necesitar mas valores, se deben pushear los registros, y luego al salir hacer el pop.

### 11. Describa la secuencia de reset del microprocesador.

La secuencia de reset del microprocesador Cortex-M4 es la siguiente:

1. **El procesador se reinicia y el PC (Program Counter) se carga con la dirección 0x00000000.**

Cuando se presiona el botón de reinicio (o se desenergiza), el procesador se reinicia. El reinicio borra todos los registros del procesador y establece el PC en la dirección 0x00000000.

2. **El procesador lee el valor de la dirección 0x00000000 en el MSP (Main Stack Pointer).**

La dirección 0x00000000 contiene el valor del MSP. El MSP es un puntero a la pila principal del procesador.

3. **El procesador lee la dirección del manejador de reset de la dirección 0x00000004 en el PC.**

La dirección 0x00000004 contiene la dirección del manejador de reset. El manejador de reset es una función que se ejecuta cuando el procesador se reinicia.

4. **El procesador salta a la dirección del manejador de reset.**

El procesador salta a la dirección del manejador de reset. El manejador de reset se ejecuta y realiza las tareas necesarias para inicializar el procesador y cargar el código de aplicación.

5. **El manejador de reset se ejecuta y realiza las siguientes tareas:**

* **Inicializa el procesador, como la configuración de los registros y el reloj.**
* **Carga el código de aplicación en la memoria RAM.**
* **Llama a la función principal del programa.**

La inicialización del procesador incluye la configuración de los siguientes elementos:

* Los registros del procesador, como el PC, el SP y los registros de estado.
* El reloj del procesador.
* Los periféricos del procesador.

La carga del código de aplicación implica copiar el código de aplicación desde la memoria ROM a la memoria RAM.

La llamada a la función principal del programa implica saltar a la dirección de la función principal. La función principal es el punto de entrada al programa.


### 12. ¿Qué entiende por “core peripherals”? ¿Qué diferencia existe entre estos y el resto de los periféricos?

La diferencia entre los "core peripherals" y el resto de los periféricos es que los "core peripherals" están integrados en el núcleo del procesador, mientras que el resto de los periféricos son dispositivos externos que se conectan al procesador a través de buses.
Los "core peripherals" incluyen los siguientes:
NVIC (Nested Vectored Interrupt Controller): Controla las interrupciones del procesador;
SCB (System Control Block): Contiene registros y funciones que controlan el funcionamiento del procesador;
MPU (Memory Protection Unit): Permite proteger la memoria del procesador de accesos no autorizados;
FPU (Floating-Point Unit): Permite realizar operaciones de coma flotante.


### 13. ¿Cómo se implementan las prioridades de las interrupciones? Dé un ejemplo

Las prioridades de interrupciones se asignan a través del NVIC cuando se configuran, y suelen implementarse mediante una escala del 0 a n (siendo n generalmente 15), para el cual 0 es la prioridad más alta. Como ejemplo, supongamos que yo asigno por interrupcion una comunicacion via UART con prioridad 2, y en el medio ocurre hardfault (con prioridad 0), la comunicacion no va a ocurrir, ya que la prioridad de hardfault es menor.

### 14. ¿Qué es el CMSIS? ¿Qué función cumple? ¿Quién lo provee? ¿Qué ventajas aporta?

El CMSIS (“ARM Cortex Microcontroller Software Interface Standard” segun sus siglas) es un conjunto de librerías en C que forman una capa de abstracción de hardware para los Cortex M independiente del fabricante, con el objetivo de dar mayor portabilidad al código. Brinda funciones para interactuar con los “Core peripherals” así como para el resto de peripherals e interactuar con un RTOS.

### 15. Cuando ocurre una interrupción, asumiendo que está habilitada ¿Cómo opera el microprocesador para atender a la subrutina correspondiente? Explique con un ejemplo

Para atender a una subrutina, lo que se realiza es lo siguiente: se pushean al stack todos los recursos del micro que estan en uso, para luego proceder a la tabla de interrupciones para ir a ejecutar el handler correspondiente. Una vez finalizada la interrupcion, se devuelve control a la rutina principal, la cual restaura el estado de sus recursos al previo a antes de la ejecución. Existen 2 métodos para ejectura multiples interrupciones: late arrival y tail chaining.

### 16. Explique las características avanzadas de atención a interrupciones: tail chaining y late arrival.

Tail chaining ocurre cuando en medio de una ejecución de una IRS se provoca otra interrupción de igual o menor prioridad. Para resolver esto, lo que se hace es concatenar las interrupciones, de forma tal de que al momento de terminar la que está en curso, se ejecute inmediatamente la otra que ingresó en el medio, evitando context switching en plena ejecucion de la IRS.

Late arrival ocurre cuando se está haciendo el stacking para atender una excepción y ocurre otra de mayor prioridad. En este caso, al ser de una prioridad mayor, lo que ocurre es que el programa atiende primero a esta útlima que surgió, aprovechando que no se terminaron de respaldar los registros, y luego despachada, se atiende a la original.

### 17a. ¿Cómo cambia la operación de stacking al utilizar la unidad de punto flotante?

La operación de stacking es la misma, la diferencia radica en que simplemente se va a estar utilizando más memoria.

### 17b. ¿Qué es el systick? ¿Por qué puede afirmarse que su implementación favorece la portabilidad de los sistemas operativos embebidos?

El systick timer es un temporizador de 24 bits, que se encuentra integrado en el núcleo del procesador. Suele utilizarse como time-slice para implementaaciones de RTOS. Se suele decir que favorece la portabilidad de los sistemas operativos embebidos, ya que el systick se encuentra en varias arquitecturas.

### 18. ¿Qué funciones cumple la unidad de protección de memoria (MPU)?
La función principal de este módulo es proteger el acceso a memoria (hasta 16 regiones de memoria diferentes), no obstante también provee otras funciones secundarias como: Prevenir que las aplicaciones (tareas) accedan a zonas de memoria de otras aplicaciones o del kernel de un SO; Prevenir que las aplicaciones accedan a periféricos sin los permisos
adecuados; Evitar que se ejecute código desde zonas no permitidas (ejemplo desde la RAM).


### 19. ¿Cuántas regiones pueden configurarse como máximo? ¿Qué ocurre en caso de haber solapamientos de las regiones? ¿Qué ocurre con las zonas de memoria no cubiertas por las regiones definidas?
El número máximo de regiones que se pueden configurar para la MPU depende del modelo de procesador. En el caso del Cortex-M4, el número máximo de regiones es 16.
En caso de haber solapamientos de las regiones, la región con el acceso más restrictivo prevalecerá. Esto significa que, si una región permite el acceso de lectura y escritura, y otra región solo permite el acceso de lectura, la región con el acceso de lectura será la que se aplique.
Las zonas de memoria no cubiertas por las regiones definidas se consideran como zonas de acceso no restringido. Esto significa que el procesador tendrá acceso a estas zonas sin restricciones.


### 20. ¿Para qué se suele utilizar la excepción PendSV? ¿Cómo se relaciona su uso con el resto de las excepciones? Dé un ejemplo


La excepción PendSV suele utilizarse para realizar tareas de sincronización entre tareas en un sistema operativo. Por ejemplo, se puede utilizar para:
Notificar a una tarea que otra tarea ha completado una tarea;
Sincronizar el acceso a un recurso compartido;
Implementar un mecanismo de temporización.
La excepción PendSV es una excepción de sistema nato. Esto significa que se puede utilizar para interrumpir el procesamiento de cualquier tarea, independientemente de su prioridad.

### 21. ¿Para qué se suele utilizar la excepción SVC? Expliquelo dentro de un marco de un sistema operativo embebido.

En un sistema operativo embebido, la excepción SVC se utiliza para proporcionar un mecanismo para que las tareas interactúen con el sistema operativo. Esto permite a las tareas realizar tareas que no pueden realizar por sí mismas, como crear o destruir otras tareas.
La excepción SVC se relaciona con el resto de las excepciones en que es una excepción de sistema. Esto significa que se puede utilizar para interrumpir el procesamiento de cualquier tarea, independientemente de su prioridad.

Ejemplo:

En un sistema operativo embebido con dos tareas, una tarea puede utilizar la excepción SVC para crear otra tarea. Para ello, la primera tarea puede generar la excepción SVC y pasar como parámetro la dirección de la función de inicialización de la nueva tarea.