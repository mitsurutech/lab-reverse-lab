# Laboratorio de ingeniería inversa para clase: lee programas con gdb, radare2, strace y ltrace

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml)

Un **laboratorio de ingeniería inversa** listo para usar, para enseñar a leer un programa del que no se tiene el código fuente, de la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning), donde los centros educativos dan a cada estudiante sus propias máquinas y su terminal en el navegador. Cada estudiante recibe una **VM** Ubuntu, gratis de probar, con un compilador, **gdb**, **radare2**, binutils, **strace** y **ltrace**, y cinco programas pequeños escritos para el laboratorio, cada uno con una clave escondida. Las encuentra examinando los programas, como lo haría un defensor o un estudiante de compiladores, y las escribe en un archivo. No se instala nada en el ordenador del estudiante.

Es solo leer y entender, con programas que el propio laboratorio compila. No enseña malware, ni saltarse la licencia de software real, ni generadores de claves, ni esconder un programa de un depurador, ni exploits, ni examinar ningún programa que no sea el del laboratorio.

## Qué hace el estudiante

El laboratorio deja cinco programas en `~/revlab` (`hello`, que no tiene clave y sirve para practicar, y luego `crack1` a `crack5`), un `answers.txt` y un `README.md` en la carpeta personal que explica qué hacer sin dar ninguna respuesta. Cada programa solo imprime `Access granted.` o `Access denied.`:

1. `crack1` recibe una contraseña en la línea de comandos: mirar sin ejecutarlo (`file`, `strings`, `objdump`, `r2`).
2. `crack2` recibe una contraseña en la línea de comandos: detenerlo mientras se ejecuta en `gdb` y mirar la memoria.
3. `crack3` recibe un número: leer la aritmética en el desensamblado y deshacer el cálculo.
4. `crack4` pide una contraseña al arrancar: observar sus llamadas a la biblioteca con `ltrace`.
5. `crack5` busca un archivo: observar sus llamadas al sistema con `strace`.
6. Escribir cada clave en `answers.txt`, una línea cada una, con la forma `crack1=la-clave`.

Detrás está la lección que necesita un defensor: todo lo que un programa debe saber para funcionar, un lector puede encontrarlo, así que un secreto guardado en un programa no es un secreto.

## Una clave distinta en cada copia

El repositorio es público, así que no contiene ninguna clave. Cuando arranca por primera vez la VM de un estudiante, `setup` crea claves aleatorias nuevas, las guarda en una carpeta a la que solo entra root, construye los programas con ellas y borra los fuentes. Las claves son aleatorias, no valores plantados. Las respuestas de un estudiante se comparan con las claves de su propia copia, así que las respuestas de un compañero o los archivos del laboratorio no revelan nada.

## Cómo se califica

El laboratorio tiene una **comprobación**, un script que califica una VM entregada sin que el profesor tenga que leerla. Se ejecuta sobre una copia de la máquina guardada tras un arranque limpio y lee `answers.txt` en la carpeta personal del estudiante. Cada una de las cinco claves debe ser la creada para esa copia de ese programa: una conjetura, una línea vacía, la clave de otra copia o una clave puesta junto al programa equivocado no pasa. También ejecuta cada programa con su propia clave, así que se nota un programa modificado. La primera línea que imprime dice qué no está bien.

Lo que no hace: califica las respuestas y no cómo se encontraron, y un estudiante que use `sudo` en su propia VM podría leer las claves guardadas. El laboratorio confía en que cada estudiante use su máquina. Es una comprobación para clase, no una prueba.

## Qué contiene el archivo

`reverse-lab.lab.yaml` es una **definición de laboratorio VM** completa, exactamente lo que ejecuta la plataforma:

| Clave | Qué es |
| --- | --- |
| `cpus`, `memory`, `disk` | el tamaño de la VM de cada estudiante: 1 CPU, 1024 MB y 10 GB |
| `open` | los puertos que se abren en el navegador: ninguno, el laboratorio es la terminal |
| `setup` | un script que se ejecuta una vez, como root, la primera vez que arranca la VM de cada estudiante: instala el compilador y las herramientas desde el archivo de Ubuntu, crea las claves, construye los cinco programas y escribe `answers.txt` y el README |
| `check` | el script de calificación, ejecutado como root sobre una copia del disco guardado tras un arranque limpio; su estado de salida es el resultado y `0` significa aprobado |

## Usarlo en la plataforma

**Para ponerlo como tarea a una clase:**

1. En tu módulo, añade una tarea y elige la tarjeta **Laboratorio VM Ubuntu**.
2. Pulsa el ejemplo de ingeniería inversa, o pega el contenido de `reverse-lab.lab.yaml`. Usar un ejemplo reemplaza el texto, y Deshacer recupera el tuyo.
3. Publica y prueba primero tu propia copia. Cada estudiante tiene su propia VM, que se conserva entre sesiones, con sus propias claves.
4. Asígnalo como cualquier tarea. Cuando se entregue, ejecuta la comprobación en la fila de un estudiante en la lista de la clase: arranca una copia de su máquina guardada que no se vuelve a guardar, y muestra si pasó y qué imprimió.

Una lección de Fundamentos de la plataforma se trabaja en este laboratorio. La [página de este laboratorio](https://mitsurutech.co.uk/es/help/lab-reverse-lab) en el manual, su [guía de laboratorios VM Ubuntu](https://mitsurutech.co.uk/es/help/labs) y su [página de laboratorios de ejemplo](https://mitsurutech.co.uk/es/help/sample-labs) explican el resto.

¿Aún no tienes cuenta para tu centro? [El primer año es gratis](https://mitsurutech.co.uk/es/pricing) hasta 50 estudiantes, y [registrarse](https://mitsurutech.co.uk/es/signup) solo pide un correo electrónico.

## Comprobar el archivo

En cada push, `.github/workflows/check.yml` comprueba que el archivo es YAML válido con las claves de arriba y que `setup` y `check` caben cada uno en el límite de 16 KB de la plataforma. No arranca ninguna VM: eso se hace en las máquinas de la plataforma.

## Lo que no funciona en un laboratorio

La VM llega al archivo de paquetes de Ubuntu, PyPI, npm y GitHub, y a nada más. Este laboratorio solo necesita el archivo, y la copia de un estudiante no puede salir a internet a por otro programa. La preparación tarda unos minutos la primera vez, mientras se instalan las herramientas.

## Acerca de

Creado por [Mitsuru Technologies](https://mitsurutech.co.uk/es) para la [plataforma de aprendizaje MitsuruTech](https://mitsurutech.co.uk/es/learning): Python, Java, Go, Node, SQL y Linux en el navegador para centros educativos, con laboratorios de contenedores, laboratorios VM, aplicaciones web desplegadas desde GitHub y cuestionarios que se corrigen solos. Prueba código sin cuenta en el [área de pruebas](https://mitsurutech.co.uk/es/playground).

Más laboratorios del mismo sitio: [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) y [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licencia

Esta obra está bajo licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Puedes compartirla y adaptarla para aprender y enseñar, si citas a Mitsuru Technologies Ltd, no la usas comercialmente y compartes lo que hagas con ella bajo la misma licencia. El texto completo está en el archivo `LICENSE`.

El compilador, gdb, radare2, binutils, strace y ltrace se instalan desde el archivo de Ubuntu cuando arranca una VM; aquí no se distribuye nada de ellos.
