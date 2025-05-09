# Memory API

En este laboratorio ganará algún grado de familiaridad con la asignación de memoria (*memory allocation*). Para el caso, usted escribirá algunos programas con bugs. Luego, usará algunas herramientas para localizar los bugs que usted ha insertado. De este modo se familiarizará con algunas de estas herramientas para un uso futuro. Estas herramientas son: el debugger (**gdb**) y el memory-bug detector (**valgrind**).

<details>
<summary><strong>Actividades previas</strong></summary>

Antes de empezar el laboratorio se recomienda (de manera opcional) realizar las siguientes tareas:

1. Revisar los conceptos de **memoria dinámica** vistos previamente. Si no los recuerda, puede acceder al siguiente [enlace](https://github.com/dannymrock/UdeA-SO-Lab/blob/master/lab0/lab0b/parte5/memoria_dinamica.ipynb) en el cual se encuentra la teoría.
2. En la página del laboratorio se recopiló información sobre las herramientas [gdb](https://github.com/dannymrock/UdeA-SO-Lab/tree/master/lab2/herramientas/gdb) y [valgrind](https://github.com/dannymrock/UdeA-SO-Lab/tree/master/lab2/herramientas/valgrind). Es muy básica, pero mirarla y analizarla antes del laboratorio le puede ser de mucha ayuda.
3. Revise y trate de entender qué hacen los siguientes códigos (teniendo en cuenta la teoría vista en malloc):

**Código 1:**
```c
#define NULL 0

int main() {
  int *p = (int*)malloc(5*sizeof(int));
  int array[] = {1,2,3,4,5};
  printf("sizeof(p): %d\\n",sizeof(p));
  printf("sizeof(p): %d\\n",sizeof(*p));
  printf("sizeof(p): %d\\n",sizeof(array));
  free(p);
  p = NULL;
  return 0;
}
```
Enlace para [simular](http://www.pythontutor.com/c.html#code=%23define%20NULL%200%0A%0Aint%20main%28%29%20%7B%0A%20%20int%20*p%20%3D%20%28int*%29malloc%285*sizeof%28int%29%29%3B%0A%20%20int%20array%5B%5D%20%3D%20%7B1,2,3,4,5%7D%3B%0A%20%20printf%28%22sizeof%28p%29%3A%20%25d%5Cn%22,sizeof%28p%29%29%3B%0A%20%20printf%28%22sizeof%28p%29%3A%20%25d%5Cn%22,sizeof%28*p%29%29%3B%0A%20%20printf%28%22sizeof%28p%29%3A%20%25d%5Cn%22,sizeof%28array%29%29%3B%0A%20%20free%28p%29%3B%0A%20%20p%20%3D%20NULL%3B%0A%20%20return%200%3B%0A%7D&curInstr=0&mode=display&origin=opt-frontend.js&py=c&rawInputLstJSON=%5B%5D).

**Codigo 2**:
 ```C
      #define NULL 0
      
      int main() {  
        int *p[2];
        int **q = p;
        int a[] = {1,2,3};
        int b[] = {4,5,6,7};
        p[0] = &a[0];
        p[1] = b;
        *(p[0] + 2) = -1;
        *(p[1] + 1) = 3;
        *(*p + 1) = 10;
        *(*(p + 1) + 3) = -7;
        *(*q + 1) = **q + *(*q + 2);  
        q = q + 1;
        *(*q + 1) = *(*q + 3) + *(*q + 2);  
        return 0;
      }
 ```
Enlace para [simular](http://www.pythontutor.com/c.html#code=%23define%20NULL%200%0A%0Aint%20main%28%29%20%7B%20%20%0A%20%20int%20*p%5B2%5D%3B%0A%20%20int%20**q%20%3D%20p%3B%0A%20%20int%20a%5B%5D%20%3D%20%7B1,2,3%7D%3B%0A%20%20int%20b%5B%5D%20%3D%20%7B4,5,6,7%7D%3B%0A%20%20p%5B0%5D%20%3D%20%26a%5B0%5D%3B%0A%20%20p%5B1%5D%20%3D%20b%3B%0A%20%20*%28p%5B0%5D%20%2B%202%29%20%3D%20-1%3B%0A%20%20*%28p%5B1%5D%20%2B%201%29%20%3D%203%3B%0A%20%20*%28*p%20%2B%201%29%20%3D%2010%3B%0A%20%20*%28*%28p%20%2B%201%29%20%2B%203%29%20%3D%20-7%3B%0A%20%20*%28*q%20%2B%201%29%20%3D%20**q%20%2B%20*%28*q%20%2B%202%29%3B%20%20%0A%20%20q%20%3D%20q%20%2B%201%3B%0A%20%20*%28*q%20%2B%201%29%20%3D%20*%28*q%20%2B%203%29%20%2B%20*%28*q%20%2B%202%29%3B%20%20%0A%20%20return%200%3B%0A%7D&curInstr=0&mode=display&origin=opt-frontend.js&py=c&rawInputLstJSON=%5B%5D).
      
**Codigo 3**:
```C
      #define NULL 0
      
      int main() {  
        // Reserva de memoria
        int **p = (int *)malloc(2*sizeof(int *));
        *p = (int)malloc(3*sizeof(int));
        *(p + 1) = (int)malloc(5*sizeof(int));
        
        // Manipulando la memoria reservada
        int i;
        for(i = 0; i < 3; i++) {
          *(*p + i) = 2*i;
        }
        for(i = 0; i < 5; i++) {
          if (i%2 == 0) {
            *(*(p + 1) + i) = -i;
          }
          else {
            *(*(p + 1) + i) = 0;      
          }
        }
        
        
        // Liberacion de memoria
        free(*p);
        free(*(p + 1));
        free(p);
        p = NULL;
        return 0;
      }
```
Enlace para [simular](http://www.pythontutor.com/c.html#code=%23define%20NULL%200%0A%0Aint%20main%28%29%20%7B%20%20%0A%20%20//%20Reserva%20de%20memoria%0A%20%20int%20**p%20%3D%20%28int%20*%29malloc%282*sizeof%28int%20*%29%29%3B%0A%20%20*p%20%3D%20%28int%29malloc%283*sizeof%28int%29%29%3B%0A%20%20*%28p%20%2B%201%29%20%3D%20%28int%29malloc%285*sizeof%28int%29%29%3B%0A%20%20%0A%20%20//%20Manipulando%20la%20memoria%20reservada%0A%20%20int%20i%3B%0A%20%20for%28i%20%3D%200%3B%20i%20%3C%203%3B%20i%2B%2B%29%20%7B%0A%20%20%20%20*%28*p%20%2B%20i%29%20%3D%202*i%3B%0A%20%20%7D%0A%20%20for%28i%20%3D%200%3B%20i%20%3C%205%3B%20i%2B%2B%29%20%7B%0A%20%20%20%20if%20%28i%252%20%3D%3D%200%29%20%7B%0A%20%20%20%20%20%20*%28*%28p%20%2B%201%29%20%2B%20i%29%20%3D%20-i%3B%0A%20%20%20%20%7D%0A%20%20%20%20else%20%7B%0A%20%20%20%20%20%20*%28*%28p%20%2B%201%29%20%2B%20i%29%20%3D%200%3B%20%20%20%20%20%20%0A%20%20%20%20%7D%0A%20%20%20%20%20%20%0A%20%20%7D%0A%20%20%0A%20%20%0A%20%20//%20Liberacion%20de%20memoria%0A%20%20free%28*p%29%3B%0A%20%20free%28*%28p%20%2B%201%29%29%3B%0A%20%20free%28p%29%3B%0A%20%20p%20%3D%20NULL%3B%0A%20%20%0A%20%20return%200%3B%0A%7D&curInstr=0&mode=display&origin=opt-frontend.js&py=c&rawInputLstJSON=%5B%5D)

</details>

<strong>Integrantes</strong>
> Johana Sevillano
> -Angi Sirley Hoyos

## Actividades de laboratorio ##

1. Escriba un programa simple llamado ```null.c``` que cree un puntero a un entero, llevelo a null y entonces intente desreferenciarlo (esto es, asignarle un valor). Compile este programa llamado ```null```. ¿Qué pasa cuando usted ejecuta este programa?

```C
  // Programa null.c 
  #include <stdio.h>
  #include <stdlib.h>
  
  int main() {
      int *p = NULL; //Creamos un puntero que apunte a NULL
      *p = 5;  //Intentamos asignar 5 al lugar donde apunta p
      printf("Valor: %d\n", *p);  
      return 0;
  }
```
```bash
# Nos movemos al directorio null
jsev@jsev-virtualbox:~$ cd null

# Creamos y editamos el archivo null.c que es el código anterior
jsev@jsev-virtualbox:~/null$ nano null.c

# Compilamos el archivo null.c
jsev@jsev-virtualbox:~/null$ gcc -o null null.c

# Listamos los archivos para comprobar que se creo el ejecutable y que tiene permisos de ejecución x
jsev@jsev-virtualbox:~/null$ ls -l
total 20
-rwxrwxr-x 1 jsev jsev 15960 may  7 23:10 null
-rw-rw-r-- 1 jsev jsev   131 may  7 23:09 null.c

# Ejecutamos el programa
jsev@jsev-virtualbox:~/null$ ./null
Violación de segmento (`core' generado)

```
<strong>Respuesta:</strong>  
Al ejecutar el programa null.c, las líneas 3 y 4 se ejecutan de forma normal, pero al intentar ejecutar la línea 5, que intenta desreferenciar el `null` asignado al puntero, se obtiene lo siguiente:
> Violación de segmento (`core' generado)

Esto sucede porque el programa intentó acceder a una zona de momoria que no tiene acceso o no puede modificar.

___
2. Compile el programa del ejercicio anterior usando información de simbolos (con la flag -g). Al hacer esto se esta poniendo mas informacion en el ejecutable para permitir al debugger acceder a informacion util sobre los nombres de las variables y cosas similares. Ejecute el programa bajo el debugger digitando en consola (para el caso) ```gdb null``` y entonces una vez el ```gdb``` este corriendo ejecute ```run```. ¿Qué muestra gdb?

>Compilamos el programa null.c con la flag -g

```bash
#Agregamos información de depuración al ejecutable
jsev@jsev-virtualbox:~/null$ gcc -g -o null null.c

#Abrimos gdb con null
jsev@jsev-virtualbox:~/null$ gdb null

#Muestra la versión del gdb y lee los simbolos de null
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git
Copyright (C) 2024 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from null...

#Una vez dentro de la consola gdb ejecutamos el programa null
(gdb) run
Starting program: /home/jsev/null/null 
[Depuración de hilo usando libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

#Muestra que el programa recibio un segmentation fault
Program received signal SIGSEGV, Segmentation fault.
#Nos dice en que linea del código falló
0x0000555555555161 in main () at null.c:6
6           *p = 5;

#Con esta línea podemos ver donde estaba el puntero
(gdb) print p
$1 = (int *) 0x0

#Con backtrace vemos la traza del fallo en específico
(gdb) backtrace
#0  0x0000555555555161 in main () at null.c:6

#Salimos de gdb
(gdb) quit
Una sesion de depuracion esta activa.

        Inferior 1 [process 3932] will be killed.

¿Salir de cualquier modo? (y or n) y
```
<strong>Respuesta:</strong>  
Al utilzar la bandera -g en la compilación agregamos simboos que será leidos si corremos el programa con gdb, a su vez
gdb nos muestra la señal que recibió el programa en este caso un "_Program received signal SIGSEGV, Segmentation fault_",
también nos muestra en detalle en que parte del programa se ecuentra el fallo y donde se encontraba el puntero cuando sucedió.

___
3. Haga uso de la herramienta ```valgrind``` en el programa empleado en los puntos anteriores. Se usará la herramienta ```memcheck``` que es parte de ```valgrind``` para analizar lo que pasa: ```valgrind --leak-check=yes null```. ¿Qué pasa cuando corre esto?, ¿Puede usted interpretar la salida de la herramienta anterior?

```bash
#Revisamos si tenemos valgrind
jsev@jsev-virtualbox:~/null$ valgrind --version
valgrind-3.22.0

#Ejecutamos el programa null con valgrind
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./null
==4663== Memcheck, a memory error detector
==4663== Copyright (C) 2002-2022, and GNU GPL'd', by Julian Seward et al.
==4663== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==4663== Command: ./null
==4663==

#Ecritura invalida de un int en una dirección que no es válida
==4663== Invalid write of size 4

#Muestra el detalle de donde ocurrió el error
==4663==    at 0x109161: main (null.c:6)

#Que está intentando hacer el programa
==4663==  Address 0x0 is not stack'd, malloc'd or (recently) free'd'
==4663== 
==4663==

#Como resultado nos da un segmentation fault
==4663== Process terminating with default action of signal 11 (SIGSEGV)
==4663==  Access not within mapped region at address 0x0
==4663==    at 0x109161: main (null.c:6)
==4663==  If you believe this happened as a result of a stack
==4663==  overflow in your program's 'main thread (unlikely but
==4663==  possible), you can try to increase the size of the
==4663==  main thread stack using the --main-stacksize= flag.
==4663==  The main thread stack size used in this run was 8388608.
==4663== 
==4663== HEAP SUMMARY:
==4663==     in use at exit: 0 bytes in 0 blocks
==4663==   total heap usage: 0 allocs, 0 frees, 0 bytes allocated
==4663== 
==4663== All heap blocks were freed -- no leaks are possible
==4663== 
==4663== For lists of detected and suppressed errors, rerun with: -s
==4663== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
Violación de segmento ('core' generado)
jsev@jsev-virtualbox:~/null$ 
```
<strong>Respuesta:</strong>  
¿Qué pasa cuando corre esto?
Una vez ejecutado el programa null con valgrind que hace un análisis de memoria, podemos ver 4 puntos claves
1. Invalid write of size 4 → Se intentó escribir 4 bytes (un int) en una dirección no válida.
2. at 0x401136: main (null.c:6) → El error ocurrió en la función main en la línea 6 del archivo null.
3. Address 0x0 is not stack'd, malloc'd or (recently) free'd → Se está accediendo a la dirección 0x0 (NULL), que nunca fue reservada ni está disponible.
4. Signal 11 (SIGSEGV) → El programa terminó con un segmentation fault.

¿Puede usted interpretar la salida de la herramienta anterior?
Si, usamos valgrind que arranca la herramienta de análisis de memoria, pedimos que nos de un chequeo detallado para saber si hay fugas de memoria con --leak-check=yes y con el resultado podemos identificar los 4 puntos anteriores que nso ayudarán a entender donde y porque se genera una fuga de memoria y así poder corregirlo.

___
4. Escriba un programa sencillo que asigne memoria usando ```malloc()``` pero olvide liberarla antes de que el programa termina. ¿Qué pasa cuando este programa se ejecuta?, ¿Puede usted usar gdb para encontrar problemas como este?, ¿Que dice acerca de Valgrind (de nuevo use este con la bandera ```--leak check=yes```)?

```C
//programa leak.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int *)malloc(10 * sizeof(int));  // Reservamos memoria para 10 enteros
    p[0] = 1;
    p[1] = 2;
    printf("Valores: %d, %d\n", p[0], p[1]);
    //  Olvidamos hacer free(p);
    return 0;
}

```
```bash
#Creamos y escribimos el programa
jsev@jsev-virtualbox:~/null$ nano leak.c

#Compilamos el programa
jsev@jsev-virtualbox:~/null$ gcc -g -o leak leak.c

#Ejecutamos
jsev@jsev-virtualbox:~/null$ ./leak
Valores: 1, 2

#Ahora usamos gdb para ejecutar el programa
jsev@jsev-virtualbox:~/null$ gdb leak
GNU gdb (Ubuntu 15.0.50.20240403-0ubuntu1) 15.0.50.20240403-git
Copyright (C) 2024 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from leak...
#Corremos el programa
(gdb) run
Starting program: /home/jsev/null/leak 
[Depuración de hilo usando libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Valores: 1, 2
[Inferior 1 (process 6063) exited normally]
(gdb) 
```
Usamos Valgrind
```bash
#Corremos el programa con valgrind
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./leak

#Resultados
==6287== Memcheck, a memory error detector
==6287== Copyright (C) 2002-2022, and GNU GPL d, by Julian Seward et al.
==6287== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==6287== Command: ./leak
==6287== 
Valores: 1, 2
==6287== 
==6287== HEAP SUMMARY:

#Nos muestra la memoria que quedó ocupada
==6287==     in use at exit: 40 bytes in 1 blocks
==6287==   total heap usage: 2 allocs, 1 frees, 1,064 bytes allocated
==6287==
==6287== 40 bytes in 1 blocks are definitely lost in loss record 1 of 1
==6287==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==6287==    by 0x10917E: main (leak.c:5)
==6287== 
==6287== LEAK SUMMARY:

#Identifica la perdida de referencia a esa memoria
==6287==    definitely lost: 40 bytes in 1 blocks
==6287==    indirectly lost: 0 bytes in 0 blocks
==6287==      possibly lost: 0 bytes in 0 blocks
==6287==    still reachable: 0 bytes in 0 blocks
==6287==         suppressed: 0 bytes in 0 blocks
==6287== 
==6287== For lists of detected and suppressed errors, rerun with: -s
==6287== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
jsev@jsev-virtualbox:~/null$
```
<strong>Respuesta:</strong>  
No se detectan fugas de memoria a simple vista, porque el programa corre e imprime los valores, pero deja memoria al terminar la ejecución que no es detectada por el gdb, sin embargo Valgrind lo reporta claramente al decir por ejemplo 
> ==6287== 40 bytes in 1 blocks are definitely lost in loss record 1 of 1
___
5. Escriba un programa que cree un array de enteros llamado data de un tamaño de 100 usando ```malloc```; entonces, lleve el ```data[100]``` a ```0```. ¿Qué pasa cuando este programa se ejecuta?, ¿Qué pasa cuando se corre el programa usando ```valgrind```?, ¿El programa es correcto?

```C
//programa data.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *data = (int *)malloc(100 * sizeof(int));  // Reservamos espacio para 100 enteros (índices 0–99)
    data[100] = 0;  // ¡Acceso fuera de rango! El último índice válido es 99
    printf("Listo\n");
    free(data);
    return 0;
}

```
```bash
#Creamos y escribimos el programa data.c
jsev@jsev-virtualbox:~/null$ nano data.c

#Compilamos
jsev@jsev-virtualbox:~/null$ gcc -g -o data data.c

#Ejecutamos
jsev@jsev-virtualbox:~/null$ ./data
Listo

#Ejecutamos con valgrind 
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./data
==7403== Memcheck, a memory error detector
==7403== Copyright (C) 2002-2022, and GNU GPL d, by Julian Seward et al.
==7403== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==7403== Command: ./data
==7403==

#Escritura invalida de tamaño 4 bytes (int)
==7403== Invalid write of size 4

#Linea de error
==7403==    at 0x1091AD: main (data.c:6)

#Identifica acceso fuera de rango
==7403==  Address 0x4a821d0 is 0 bytes after a block of size 400 alloc d
==7403==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7403==    by 0x10919E: main (data.c:5)
==7403== 
Listo
==7403== 
==7403== HEAP SUMMARY:
==7403==     in use at exit: 0 bytes in 0 blocks
==7403==   total heap usage: 2 allocs, 2 frees, 1,424 bytes allocated
==7403== 
==7403== All heap blocks were freed -- no leaks are possible
==7403== 
==7403== For lists of detected and suppressed errors, rerun with: -s
==7403== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
jsev@jsev-virtualbox:~/null$ ^C
jsev@jsev-virtualbox:~/null$ 
```
<strong>Respuesta:</strong>  
¿Qué pasa cuando este programa se ejecuta?
Aparemtemente el programa ejecuta sin problemas pero internamente ocurre un acceso fuera de rango lo que puede provocar fallos impredecibles en otras partes del código.

¿Qué pasa cuando se corre el programa usando ```valgrind```?
Valgrind detecta perfectamente los errores de memoria dando una descripción del error y la línea que lo causa, en este caso lo siguiente:
> ==7403== Invalid write of size 4
> ==7403==    at 0x1091AD: main (data.c:6)
> ==7403==  Address 0x4a821d0 is 0 bytes after a block of size 400 alloc'd

¿El programa es correcto?
No, el programa no es correcto porque aunque compila y ejecuta aparentemente bien, comete un error tratando de acceder a data[100] cuando el array solo tiene posiciones válidas de data[0] a data[99].

___
6. Codifique un programa que asigne un array de enteros (como arriba), luego lo libere, y entonces intente imprimir el valor de un elemento del array. ¿El programa corre?, ¿Que pasa cuando hace uso de ```valgrind```?

```C
//programa data_array.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *data = (int *)malloc(100 * sizeof(int));  // Reservamos 100 enteros
    data[0] = 42;                                  // Asignamos un valor
    free(data);                                    // Liberamos la memoria
    printf("Valor: %d\n", data[0]);               // Intentamos acceder después de liberar
    return 0;
}
```
```bash
#Creación y escritura del programa data_array.c
jsev@jsev-virtualbox:~/null$ nano data_array.c

#Compilamos
jsev@jsev-virtualbox:~/null$ gcc -g -o data_array data_array.c

#Ejecutamos
jsev@jsev-virtualbox:~/null$ ./data_array
Valor: 1800726968

#Ejecutamos con valgrind
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./data_array
==7636== Memcheck, a memory error detector
==7636== Copyright (C) 2002-2022, and GNU GPL d, by Julian Seward et al.
==7636== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==7636== Command: ./data_array
==7636==

#Lectura invalida de un bloque de memoria
==7636== Invalid read of size 4

#Línea de error
==7636==    at 0x1091BD: main (data_array.c:8)

#Bloque de memoria fue liberado
==7636==  Address 0x4a82040 is 0 bytes inside a block of size 400 free'd '
==7636==    at 0x484988F: free (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7636==    by 0x1091B8: main (data_array.c:7)
==7636==  Block was alloc'd ' at
==7636==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7636==    by 0x10919E: main (data_array.c:5)
==7636== 
Valor: 42
==7636== 
==7636== HEAP SUMMARY:
==7636==     in use at exit: 0 bytes in 0 blocks
==7636==   total heap usage: 2 allocs, 2 frees, 1,424 bytes allocated
==7636== 
==7636== All heap blocks were freed -- no leaks are possible
==7636== 
==7636== For lists of detected and suppressed errors, rerun with: -s
==7636== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
jsev@jsev-virtualbox:~/null$ 
```
<strong>Respuesta:</strong>  
¿El programa corre?
Sí, pero tiene comportamiento extraño ya que el programa libera la mamoria antes de imprimir el valor, lo que hace que quiera imprimir un valor 
que ya no existe y terminaremos con un valor basura lo que es un error grave.

¿Que pasa cuando hace uso de ```valgrind```?
Valgrind detecta el error y reporta la causa, lo que nos ayuda evidenciar el error para corregirlo de forma eficiente.

___
7. Ahora pase un **funny value** para liberar (e.g. un puntero en la mitad del array que usted ha asignado) ¿Qué pasa?, ¿Ústed necesita herramientas para encontrar este tipo de problemas?

```C
//programa funny_free.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *data = (int *)malloc(100 * sizeof(int));  // Reservamos 100 enteros
    int *middle = data + 50;                      // Puntero a mitad del array
    free(middle);                                 // Intentamos liberar desde la mitad
    return 0;
}
```
```bash
#Creamos y escribimos el programa funny_free.c
jsev@jsev-virtualbox:~/null$ nano funny_free.c

#Compilamos
jsev@jsev-virtualbox:~/null$ gcc -g -o funny_free funny_free.c

#Resultado de la compilación
funny_free.c: In function ‘main’:

#La compilación se crashea y muestra el error, se esper un puntero devuelto por malloc() 
funny_free.c:7:5: warning: ‘free’ called on pointer ‘data’ with nonzero offset 200 [-Wfree-nonheap-object]
    7 |     free(middle);                                 // Intentamos liberar desde la mitad
      |     ^~~~~~~~~~~~
funny_free.c:5:24: note: returned from ‘malloc’
    5 |     int *data = (int *)malloc(100 * sizeof(int));  // Reservamos 100 enteros
      |                        ^~~~~~~~~~~~~~~~~~~~~~~~~

#Ejecutamos con valgrind
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./funny_free
==7675== Memcheck, a memory error detector
==7675== Copyright (C) 2002-2022, and GNU GPL d, by Julian Seward et al.
==7675== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==7675== Command: ./funny_free
==7675==

#Se intenta liberar una dirección dentro de un bloque reservado
==7675== Invalid free() / delete / delete[] / realloc()
==7675==    at 0x484988F: free (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7675==    by 0x10919C: main (funny_free.c:7)
==7675==  Address 0x4a82108 is 200 bytes inside a block of size 400 alloc'd '
==7675==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7675==    by 0x10917E: main (funny_free.c:5)
==7675== 
==7675== 
==7675== HEAP SUMMARY:
==7675==     in use at exit: 400 bytes in 1 blocks
==7675==   total heap usage: 1 allocs, 1 frees, 400 bytes allocated
==7675== 
==7675== 400 bytes in 1 blocks are definitely lost in loss record 1 of 1
==7675==    at 0x4846828: malloc (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==7675==    by 0x10917E: main (funny_free.c:5)
==7675== 
==7675== LEAK SUMMARY:
==7675==    definitely lost: 400 bytes in 1 blocks
==7675==    indirectly lost: 0 bytes in 0 blocks
==7675==      possibly lost: 0 bytes in 0 blocks
==7675==    still reachable: 0 bytes in 0 blocks
==7675==         suppressed: 0 bytes in 0 blocks
==7675== 
==7675== For lists of detected and suppressed errors, rerun with: -s
==7675== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
jsev@jsev-virtualbox:~/null$ 

```
<strong>Respuesta:</strong>  
¿Qué pasa?
Al compilar el programa este crashea de inmediato mostrando claramente que el error surge de la línea 7 cuando free(middle) intenta liberar el puntero que esta en ese momento en la mitad del array, lo cual es un error porque el puntero se deberia llevar a null al principio o final del array.

¿Ústed necesita herramientas para encontrar este tipo de problemas?
Sí. Valgrind detecta este error claramente, mientras que a simple vista es casi imposible de rastrear.

___
8. Intente usar alguna de las otras interfaces para asignacion de memoria. Por ejemplo, cree una estructura de datos simple similar a un vector y que use rutinas que usen realloc para manejar el vector. Use un array para almacenar los elementos del vector; cuando un usuario agregue una entrada al vector, use realloc para asignar un espacio mas a este. ¿Que tan bien funciona el vector asi?, ¿Como se compara con una lista enlazada?, utilice ```valgrind``` para ayudarse en la busqueda de errores.

```C
// programa vector_realloc.c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *vector = NULL;  // Inicialmente vacío
    int size = 0;
    int input, i;

    while (1) {
        printf("Ingrese un número (-1 para salir): ");
        scanf("%d", &input);
        if (input == -1) break;

        // Incrementar tamaño
        size++;

        // Redimensionar el array
        vector = (int *)realloc(vector, size * sizeof(int));
        if (vector == NULL) {
            printf("Error de memoria\n");
            exit(1);
        }

        // Guardar el nuevo elemento
        vector[size - 1] = input;
    }

    // Mostrar los elementos
    printf("Elementos del vector:\n");
    for (i = 0; i < size; i++) {
        printf("%d ", vector[i]);
    }
    printf("\n");

    // Liberar memoria
    free(vector);

    return 0;
}
```
```bash
#Creamos y escribimos el progrma vector_realloc.c
jsev@jsev-virtualbox:~/null$ nano vector_realloc.c

#Compilamos
jsev@jsev-virtualbox:~/null$ gcc -g -o vector_realloc vector_realloc.c

#Ejecutamos
jsev@jsev-virtualbox:~/null$ ./vector_realloc
Ingrese un número (-1 para salir): 10
Ingrese un número (-1 para salir): 20
Ingrese un número (-1 para salir): 30
Ingrese un número (-1 para salir): 40
Ingrese un número (-1 para salir): -1
Elementos del vector:
10 20 30 40 

#Ejecutamos con valgrind
jsev@jsev-virtualbox:~/null$ valgrind --leak-check=yes ./vector_realloc
==7716== Memcheck, a memory error detector
==7716== Copyright (C) 2002-2022, and GNU GPL d, by Julian Seward et al.
==7716== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==7716== Command: ./vector_realloc
==7716== 
Ingrese un número (-1 para salir): 10
Ingrese un número (-1 para salir): 20
Ingrese un número (-1 para salir): 30
Ingrese un número (-1 para salir): 40
Ingrese un número (-1 para salir): -1
Elementos del vector:
10 20 30 40 
==7716== 
==7716== HEAP SUMMARY:
==7716==     in use at exit: 0 bytes in 0 blocks
==7716==   total heap usage: 6 allocs, 6 frees, 2,088 bytes allocated
==7716== 

#No detecta lectura o escritura invalida ni fugas de memoria
==7716== All heap blocks were freed -- no leaks are possible
==7716== 
==7716== For lists of detected and suppressed errors, rerun with: -s
==7716== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
jsev@jsev-virtualbox:~/null$ 

```
 ¿Que tan bien funciona el vector asi?
 Es flexible porque se puede agregar tantos elementos como se quiera. Pero cada inserción hace un realloc, lo que implica copiar todos los elementos a un nuevo bloque o un impacto en rendimiento cuando el vector crece mucho.
 
 ¿Como se compara con una lista enlazada?
 
| Vector (`realloc`)                     | Lista enlazada                      |
| -------------------------------------- | ----------------------------------- |
| Acceso rápido por índice (`vector[i]`) | Acceso secuencial (`O(n)`)          |
| Inserción lenta (`O(n)` por realloc)   | Inserción rápida (`O(1)` en cabeza) |
| Memoria contigua                       | Memoria dispersa (punteros)         |
| Mejor cache performance                | Peor cache performance              |


9. Gaste mas tiempo y lea sobre el uso de gdb y valgrind. Conocer estas herramientas es critico; gaste el tiempo y aprenda como volverse un experto debugger en UNIX y C enviroment.

<details>
<summary><strong>Valgrind</strong></summary>

1. http://valgrind.org/docs/manual/quick-start.html
2. http://www.st.ewi.tudelft.nl/koen/ti2725-c/valgrind.pdf
3. http://pages.cs.wisc.edu/~bart/537/valgrind.html
4. http://web.mit.edu/amcp/drg/valgrind-howto.pdf
</details>

<details>
<summary><strong>GDB</strong></summary>

1. http://www.lsi.us.es/~javierj/ssoo_ficheros/GuiaGDB.htm
2. https://www.gdb-tutorial.net/
3. https://web.eecs.umich.edu/~sugih/pointers/summary.html
4. https://www.cs.umd.edu/~srhuang/teaching/cmsc212/gdb-tutorial-handout.pdf
5. https://lihuen.linti.unlp.edu.ar/index.php/C%C3%B3mo_usar_GDB
6. https://www.cs.cmu.edu/~gilpin/tutorial/
7. http://pages.di.unipi.it/bodei/CORSO_FP_17/FP/Lezioni/gdb-commands.pdf
8. https://cs.brown.edu/courses/cs033/docs/guides/gdb.pdf
9. https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf
10. http://users.ece.utexas.edu/~adnan/gdb-refcard.pdf
</details>
