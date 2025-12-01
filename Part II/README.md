# Laboratorio Wireshark Parte 2: Análisis y Desencriptación de Tráfico HTTPS (TLS/SSL) con Wireshark

Este repositorio contiene los recursos necesarios para un ejercicio práctico enfocado en el análisis de tráfico de red cifrado. El objetivo principal es que domineis la técnica de usar un Archivo de Registro de Claves (Key Log File) para desencriptar sesiones TLS y examinar el contenido de la capa HTTP.

## Enunciado

Se proporciona una captura de tráfico (`.pcap`) y el archivo de registro de claves TLS que permite la desencriptación.

El tráfico capturado corresponde a una sesión de navegación que incluye la ejecución de un ataque. La tarea como analista de seguridad es:

1.  **Configurar Wireshark** para que el tráfico HTTPS sea legible.
2.  **Identificar el Vector de Ataque:** Determinar si hubo un intento de descarga de malware o exfiltración de datos.
3.  **Localizar la Carga Útil Maliciosa:** Encontrar el paquete que solicita la descarga de un componente malicioso (un archivo `.exe`, `.bat` o `.dll`).

---

## Prerrequisitos

Para realizar esta práctica, se necesitará tener instalado:

1.  **Wireshark:** La versión más reciente del analizador de protocolos de red.
2.  **Archivos de la Práctica:**
    * `decrypting_HTTPS_TLS_traffic.pcap`: El archivo de captura de tráfico (PCAP) cifrado.
    * `KeysLogFile.txt`: El archivo de registro de claves TLS (\texttt{SSLKEYLOGFILE}).

---

## Configuración: Desencriptación en Wireshark

El tráfico en el archivo \texttt{decrypting\_HTTPS\_TLS\_traffic.pcap} está cifrado (TLS). Para que Wireshark pueda leer el contenido HTTP, debe cargar el archivo de claves proporcionado.

Sigue estos pasos:

1.  Abre **Wireshark**.
2.  Vete a **`Edición`** (Edit) en el menú superior.
3.  Selecciona **`Preferencias`** (Preferences).
4.  En la ventana de Preferencias, navega a **`Protocolos`** (Protocols).
5.  Desliza hacia abajo o haz clic en el primer tipo de protocolo y escribe TLS. Selecciona el protocolo **`TLS`** (o \texttt{SSL/TLS}).
6.  Busca el campo **`(Pre)-Master-Secret log filename`**.
7.  Haz clic en **`Explorar`** (Browse) y selecciona la ruta completa del archivo **`KeysLogFile.txt`** (Asegúrate de que este archivo esté guardado en una ubicación fija).
8.  Haga clic en **`Aceptar`** (OK).
9.  Abra el archivo \texttt{decrypting\_HTTPS\_TLS\_traffic.pcap}. Wireshark ahora debería mostrar el tráfico \texttt{TLS} descifrado como \texttt{HTTP}.


---

## Filtros de Visualización para Actividad Sospechosa
Una vez que el tráfico esté descifrado, se debe crear filtros de visualización para aislar los paquetes de interés.

### 1. Búsqueda de Componentes Maliciosos

El ataque podría implicar la descarga de un componente de malware. Los archivos ejecutables, librerías o scripts de comandos son indicios clave.

Crea un filtro que muestre todas las peticiones \textbf{HTTP} donde el \textbf{URI de la solicitud} contenga extensiones de archivos ejecutables de Windows (`.exe`), bibliotecas dinámicas (`.dll`) o scripts de comandos (`.bat`).


