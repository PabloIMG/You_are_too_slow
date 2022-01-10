╔ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═    ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ╗
║  __ __            _____                        ║
║ |  |  |___ _ _   |  _  |___ ___                ║
║ |_   _| . | | |  |     |  _| -_|               ║
║   |_| |___|___|  |__|__|_| |___|               ║
║                                                ║
║                                            __  ║
║      _____            _____ _             |  | ║
║     |_   _|___ ___   |   __| |___ _ _ _   |  | ║
║       | | | . | . |  |__   | | . | | | |  |__| ║
║       |_| |___|___|  |_____|_|___|_____|  |__| ║
║                                                ║
║             ~ Información al Programador ~     ║
║                                                ║
╚ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═    ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ═ ╝

----------------------------------------------------------------------------------------------------------------------

╔═════════════════════════╗ 
    Información General   
╚═════════════════════════╝

- Proyecto Semestral para la asignatura Sistemas Operativos

    Consiste en crear un juego en el que los jugadores deben ser lo más
    rápido para robar el turno y enviar la posición de una matriz para obtener
    como punto el valor que se encuentra en la posición ingresada. Ganará quien
    tenga más puntaje al momento de revelar todas las posiciones o cuando un jugador
    solicite finalizar el juego, en este último caso el juego finaliza solo si el jugador
    que envió la solicitud tiene el valor más alto, en caso contrario el jugador es eliminado
    y el juego sigue su transcurso.

- Para una lectura más legible del código se recomienda utilizar la extensión "Better Comments" en el
    editor de código "Visual Studio Code".

- Para una visualización más cómoda del inicio del juego se recomienda ejecutar el archivo "servidor.c"
    en una terminal aparte o con espacio horizontal.

- El programa se encuentra incompleto en su fase de juego ya que cuenta solo con la opción de robar el turno
    junto a la asignación de puntaje.

- Desarrollado por Alejandro Farías y Pablo Montoya

- Para mayor información sobre versiones y comentarios visite "https://github.com/PabloIMG/You_are_too_slow"

----------------------------------------------------------------------------------------------------------------------

╔════════════════════════════╗ 
    Información Servidor.c   
╚════════════════════════════╝

- Líneas 43 a 49: Creación de sigset el cual se encargará de almacenar las señales que se indican en las instrucciones
                    "sigaddset" para poder manejarlas según sea necesario.

- Líneas 52 a 77: Declaración de variables a utilizar tanto por el proceso Padre como por los procesos Hijos.

- Líneas 103 y 112: Declaración de semáforos encargados de manejar sincronismo y pausar ejecución de procesos para
                        esperar a que los demás procesos los alcancen y continuar ejecución.

                    Luego se inicializan los semáforos utilizando memoria compartida, de esta forma se convierte en
                        una variable "global" que puede ser usada por todos los procesos y sus alteraciones se ven
                        en todos los procesos.

- Líneas 114 a 129: Creación de tubería FIFO destinada a comunicar procesos Servidor - Cliente con Cliente - Jugador.

- Líneas 134 a 158: Creación de Matriz Oculta destinada a ser modificada y leída por procesos Servidor - Cliente con
                        el fin de realizar las validaciones para enviar coordenadas al proceso Servidor - Padre.

                    Se utiliza memoria compartida para que todos los procesos puedan acceder a ella viendo posiciones
                        reveladas tanto por ellos mismos, como por otros procesos. De esta forma se puede trabajar
                        con la matriz en tiempo real.
                    
- Líneas 161 a 178: Creación de Matriz Real destinada a ser utilizada por el proceso Servidor - Padre con el fin de
                        obtener el valor de las coordenadas enviadas por el proceso Servidor - Cliente y asignarles
                        el puntaje de dicha posición.

~~ Líneas 181 a 237 "CREACIÓN de PROCESOS SERVIDOR - CLIENTE" ~~

- Líneas 185 a 196: Creación de tuberías PIPE destinadas a ser usadas por proceso Servidor - Padre y procesos
                        Servidor - Cliente logrando una comunicación bidireccional mediante el uso de las 2 tuberías
                        creadas. Su declaración realizada en la línea 102 como matriz se realizó para representar
                        el siguiente modelo sugerido por el profesor:

                             Leer     Escribir                   Leer     Escribir
                    Hijo 0  [ NO ]   [   SI   ]         Padre   [ SI ]   [   NO   ]
                    Hijo 0  [ SI ]   [   NO   ]         Padre   [ NO ]   [   SI   ]

                    Hijo 1  [ NO ]   [   SI   ]         Padre   [ SI ]   [   NO   ]
                    Hijo 1  [ SI ]   [   NO   ]         Padre   [ NO ]   [   SI   ]

- Líneas 202 y 203: [Servidor - Cliente] Se cierran extremos que no se utilizarán en las tuberías.

- Líneas 206 a 211: Se establece conexión entre Servidor - Cliente y Cliente - Jugador, recibiendo PID del Jugador
                        y luego retornando PID del Cliente al Jugador. Se siguió el siguiente modelo para realizar
                        la conexión, el cual fue la versión final de las pruebas hechas:

                        "ENVIO DE PID's ENTRE CLIENTE Y JUGADOR"

                        SERVIDOR CLIENTE                               CLIENTE JUGADOR

                            PID Jugador (int)       <------------       PID (int)

                            PID (pid_t)             ------------>       PID Cliente (pid_t)

- Líneas 227 y 228: [Servidor - Padre] Se cierran extremos que no se utilzarán en las tuberías.

- Línea 231: Se pausa ejecución del proceso Servidor - Padre hasta que el proceso Servidor - Hijo tenga una conexión
                exitosa con su jugador asignado evitando que el código del padre se siga ejecutando y creando nuevos
                hijos provocando errores de ejecución. El semáforo se reanuda en la línea 220 por el hijo indicando
                que logró realizar la conexión con el jugador asignado.

- Línea 243: Se pausa ejecuón del proceso Servidor - Cliente una vez que haya realizado su conexión, esto se realiza
                para evitar que siga ejecutando su código logrando esperar a los demás jugadores.

- Líneas 248 a 315: Desarrollo del algoritmo de asignación de valores a la matriz Real, utilizando un vector con los
                        valores aleatorios no repetidos en el rango seleccionado y luego asignándole un valor del
                        vector a la matriz.

- Líneas 322 a 325: Se liberan procesos pausados en la línea 243 ya que el proceso Servidor - Padre es el último en
                        salir del for para creación de procesos. De esta forma se continua la ejcución de todos los
                        procesos de manera sincronizada.

~~ Líneas 336 a 639 "CÓDIGO DEL JUEGO" ~~

- Línea 346: Proceso Servidor - Cliente espera a recibir señal de su jugador asignado.

- Líneas 348 a 511: Manejo de señales procesos Servidor - Cliente, las cuales se modificaron con el sigset creado.

                    Caso 10: Cuando se recibe esta señal el proceso Servidor - Cliente que la recibió informa al
                                proceso Sevidor - Padre que robó el turno y realiza lectura y validación 
                                de coordenadas.

                    Caso 12: Cuando se recibe esta señal el proceso Servidor - Cliente que la recibió envía señal
                                al jugador para bloquear o desbloquear sus controles

                    Caso 20: Función en desarrollo para declarar ganador al jugador que le envió la señal.

- Líneas 525: Proceso Servidor - Padre espera a recibir señal de los procesos Servidor - Cliente.

- Líneas 527 a 637: Manejo de señales para el proceso Servidor - Padre, las cuales se modificaron con el sigset creado.

----------------------------------------------------------------------------------------------------------------------

╔═══════════════════════════╗
    Información Cliente.c   
╚═══════════════════════════╝

- Líneas 46 a 66: Creación de sigset el cual se encargará de almacenar las señales que se indican en las instrucciones
                    "sigaddset" para poder manejarlas según sea necesario.

- Líneas 87 a 95: Validación de conexión al servidor enviando PID y recibiendo PID del proceso que se nos asignó.

- Líneas 106 a 130: Ignorar señales en caso de que nos hayan robado el turno.

- Líneas 142 a 149: Dependiendo del valor de "permisos" se asigna sigset para tratar señales.

- Líneas 158 a 247: Manejo de señales al jugador.

----------------------------------------------------------------------------------------------------------------------