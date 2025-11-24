# Proyecto Análisis Forense

## Objectivo
> Hemos encontrado una página web secreta donde el famoso grupo de hackers llamado “Inc08itos” guarda los datos de sus miembros. El problema es que no sabemos cómo acceder a la página. Sin embargo, el Departamento de Defensa logró capturar algunos paquetes que podrían darnos pistas. Una cosa que sabemos sobre Inc08itos es que cada miembro tiene un nombre en clave único basado en malware famoso.

En cuanto intentamos acceder al sitio, nos aparece el mensaje: ```You do not have the correct key to access this page``` (No tienes la clave correcta para acceder a esta página)


![sharkbite-1](https://user-images.githubusercontent.com/45502375/145147627-d312a648-8ab5-4ba0-b285-da51246c8154.png)


Así que vamos a obtener la clave correcta.


## Obteniendo la clave

Nos dieron dos capturas de paquetes que contienen información que podríamos usar. Veamos la primera.

### Parte I - Conversacion 1:

- Hay *muchísimos* datos que revisar. Afortunadamente, no es necesario. Filtraremos el tráfico HTTP y veremos qué encontramos.
- Hay un archivo llamado ```pages``` y algunos datos de cookies etiquetados como ```PHPSESSID``` y ```_ga```. Podemos robar estas cookies y las guardaremos en un archivo de texto para más adelante.
- También exportamos el archivo HTTP para leer su contenido ahora.

![sharkbite-2](https://user-images.githubusercontent.com/45502375/145147645-5dd3ab7d-5709-433d-9a81-c8349bb0fb85.gif)


- El archivo descargado contiene una larga cadena codificada en base64. Al decodificarla, revela una clave privada RSA que podemos usar.
- Guardamos la clave que encontramos en un archivo con extensión ```.pfx```. Esto nos permite usarla en Wireshark como una clave RSA.


![sharkbite-3](https://user-images.githubusercontent.com/45502375/145147656-9527763f-6925-49d9-a8f7-1da59e320836.png)
![sharkbite-4](https://user-images.githubusercontent.com/45502375/145147662-b3943ded-b688-4892-9843-624b4ed7c4f2.png)


### PARTE II - Conversacion 2:

- Ahora que tenemos una clave privada RSA, podemos descifrar el tráfico TLS de la segunda captura.
- Para hacerlo, vamos a la configuración del protocolo TLS en Wireshark y añadimos el archivo ```sharkbite.pfx``` a la lista de claves RSA.
- Al descifrar con éxito, encontramos que se ha revelado otra “clave”.
- Combinando los datos de las cookies con la clave encontrada, ahora podemos acceder al sitio web.

![sharkbite-5](https://user-images.githubusercontent.com/45502375/145147860-4c5e42df-edcd-4e54-bf04-453dcd987f00.gif)


## Obteniendo acceso

Todo lo que necesitamos para acceder al sitio web es poner toda la información encontrada en nuestras propias cookies.

- Abrimos el Inspector de Elementos del navegador.
- Vamos a la pestaña ```Storage``` (Almacenamiento) y creamos tres cookies.
- Les ponemos los nombres ```PHPSESSID```, ```_ga``` y ```key```, y cambiamos sus valores por los que encontramos.
- Al refrescar la página, nos reciben como si fuésemos uno de ellos.


![sharkbite-6](https://user-images.githubusercontent.com/45502375/145148232-ea87cd25-3147-4444-902d-4d7b2266326c.gif)



## El último tramo

Ahora que tenemos acceso al sitio, debemos encontrar los datos de uno de los miembros de Inc08itos. Pero nos dicen que “No hay miembros registrados…”. Qué raro.

Observemos más de cerca la petición que enviamos al sitio.

- Usando Burp Suite, vemos que se incluye un nuevo parámetro en la petición llamado ```codename```.
- Sabemos que los miembros usan nombres de malware famoso como alias.
- Utilizando esta página (https://attacksimulator.com/blog/10-famous-malware-examples-in-history/), podemos adivinar que la persona que buscamos se llama “cryptolocker” (después de haber probado muchos otros nombres).
- Entonces enviamos la petición al Repeater y asignamos a ```codename``` el valor "cryptolocker", y reenviamos la solicitud.


![sharkbite-8](https://user-images.githubusercontent.com/45502375/145148799-d2aa51b7-625c-4407-8dc5-e0a431bdad08.gif)

