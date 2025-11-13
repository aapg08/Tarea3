# Tarea 3 - Redes

## Respuestas de la Simulación

---

### Paquete Simple PDU 
1. **Largo en bits de la Dirección IP de Destino:** **32 bits**
2. **Dirección IP de Destino:** **172.20.0.2**
3. #### **Capa de Entrada (Recepción del Echo Request)**

* **Capa 1:**
    * Puerto FastEthernet recibe el *frame*.
* **Capa 2:**
    * La dirección MAC de destino es igual a: la dirección MAC del puerto que lo recibe, la dirección del *broadcast* o dirección del *multicast*.
    * El dispositivo DNS des-encapsula el PDU desde el *frame* Ethernet.
* **Capa 3:**
    * La dirección IP de destino es igual a: la dirección IP del dispositivo o la dirección del *broadcast*.
    * El dispositivo des-encapsula el paquete.
    * El paquete es tipo **ICMP**. El proceso ICMP lo procesa.
    * El proceso ICMP recibió un mensaje **Echo Request**.

#### **Capa de Salida (Envío del Echo Reply)**

* **Capa 3:**
    * El proceso ICMP responde al *Echo Request* armando un tipo ICMP para un **Echo Reply**.
    * El proceso ICMP envía un *Echo Reply*.
    * La dirección IP de destino **172.20.0.2** no es la misma subred y no es la dirección de *broadcast*.
    * Se asigna el *default gateway*. El dispositivo asigna el siguiente salto al *default gateway*.
* **Capa 2:**
    * La dirección IP del siguiente salto es un *unicast*. El proceso **ARP** lo busca en la tabla ARP.
    * La dirección IP del siguiente salto se encuentra en la tabla ARP. El proceso ARP asigna la dirección MAC de destino del *frame* a la que se encontró en la tabla.
    * El dispositivo encapsula el PDU en un *ethernet frame*.
* **Capa 1:**
    * El puerto **FastEthernet0** envía el *frame*.

---

### Envío y Recepción de Email
### 1. Tipos de Paquetes (Capa de Aplicación)

Los tipos de paquetes que se envían y reciben durante la comunicación son:

* **DNS:** Se usa al inicio para encontrar el nombre de dominio del servidor de correo y obtener su dirección IP necesaria para el envío y la recepción.
* **SMTP:** Se utiliza en la fase de **envío**. Es el protocolo que transporta el mensaje del correo electrónico desde el PC del Profesor.
* **POP3:** Se utiliza en la fase de **recepción**. Es el protocolo que permite al PC en que se conectó el alumno autenticarse y descargar el mensaje desde el Servidor.

### 2. Protocolos de Red Utilizados (Capa de Transporte)

El protocolo de red utilizado en cada etapa de la transmisión es **TCP**.

* **TCP:** Protocolo clave para el servicio de correo, ya que entrega la **confiabilidad** necesaria para SMTP y POP3. Su función comienza con el **establecimiento de la conexión** para garantizar que el cliente y servidor estén listos. Durante la transmisión, TCP divide el correo electrónico en segmentos, además asegura que lleguen en el orden correcto mediante el uso de números de secuencia, y gestiona el control de flujo y la retransmisión en caso de que se pierdan datos. Finalmente, se encarga de **cerrar la conexión de forma ordenada**, garantizando que el mensaje completo se haya enviado o recibido sin errores.

### 3. Flujo de la Comunicación (Dispositivo $\leftrightarrow$ Servidor)

#### A. Desde la Casa del Profesor (Laptop) hacia el Servidor (Envío)

1.  Se genera un paquete **DNS**, pero como no se conoce la traducción MAC de la dirección IP, se trata de enviar un **ARP request**.
2.  El dispositivo envía un paquete **ARP** como *broadcast* dentro de la red local de la casa del profesor para conocer la dirección MAC del dispositivo de destino de salida (*router gateway*), que se propaga por todos los dispositivos de la red local.
3.  Cuando el paquete llegue al *router gateway*, ya que la dirección IP de destino es igual a la dirección IP puerto receptor, se genera un **ARP reply** con la dirección MAC pedida y se envía de vuelta al dispositivo original (*laptop*). Como los demás dispositivos no coinciden en IP con la del ARP, lo botan.
4.  Una vez capturada la **ARP Reply** por el dispositivo, se saca el paquete **DNS** previamente generado del *buffer* y se envía al servidor DNS con una *query* solicitando la traducción a IP de la dirección del dominio de destino (`mail.uc.cl`).
5.  El servidor DNS recibe la **DNS Query**, la procesa localmente y envía de vuelta al dispositivo un **DNS Response** con la IP correspondiente al dominio consultado.
6.  Una vez que el dispositivo recibe la **DNS Response**, genera y envía un paquete **TCP SYN** a la dirección de destino (servidor UC) para solicitar establecer una conexión.
7.  El servidor UC una vez recibido el paquete **TCP SYN**, acepta la conexión y envía de vuelta al dispositivo un **TCP SYN+ACK** con sus parámetros y el número ACK correspondiente a la secuencia en la conexión.
8.  El dispositivo recibe el paquete **TCP SYN+ACK** y confirma que la conexión fue establecida.
9.  El dispositivo envía un paquete **SMTP** encargado de transmitir el correo que se quiere enviar (dirección dispositivo-servidor).
10. El servidor SMTP del servidor UC recibe el paquete y envía de vuelta un paquete del mismo tipo con una confirmación de que el correo fue recibido exitosamente.

#### B. Desde el DCCyber Café (PC) al Servidor (Recepción)

El proceso es básicamente el mismo con las siguientes diferencias:

* Ya que las asignaciones IP son estáticas, **no se envía un paquete ARP** porque las direcciones MAC son conocidas
* En el último "tramo" de la comunicación, en vez de enviarse un paquete SMTP, desde el dispositivo al servidor y de vuelta, se transmite un paquete de tipo **POP3** al servidor POP3 que se encarga de descargar los correos entrantes al dispositivo del cliente (PC). Ya que el servidor SMTP maneja los correos que se envían y POP3 los que se reciben.
* Como diferencia adicional y particular a esta simulación, ya que el dispositivo de la casa del profesor fue la *laptop*, como la conexión es *wireless*, cada vez que cualquier paquete pasaba por el *access point* que lo conecta a la red, estos se propagaban también a los otros dispositivos conectados de la misma forma, pero como las IPs de los dispositivos "equivocados" no calzaban con la del destino de los paquetes, simplemente los botaba. Para la comunicación hecha con la subred del DCCyber Cafe, no hubo ningún tipo de "propagación" a otros dispositivos porque la conexión era directa.

### 4. Necesidad de los Protocolos y Paquetes

* **DNS:** Es fundamental porque los usuarios recuerdan nombres, pero las redes operan con direcciones IP. **DNS es el traductor** necesario para ubicar el servidor antes de cualquier envío o recepción de correo.
* **SMTP y POP3:** Definen el **idioma de la aplicación**. **SMTP** permite estructurar y enviar el mensaje de manera estandarizada. **POP3** permite que el cliente sepa cómo autenticarse y cómo solicitar y descargar el correo almacenado en el servidor.
* **TCP:** Es el protocolo más crítico para la **integridad de los datos**. Los protocolos de SMTP y POP3 necesitan asegurarse que el correo se enviará completo y sin error. TCP asegura la entrega, ordena los paquetes y gestiona la recuperación de errores, haciendo que el correo no se pierda ni corrompa durante la transmisión.

---

### Página Web
1.  **Largo en bits del HTTP Request:** **32 bits**
2.  **Número de paquetes divididos (Imagen):** **10 paquetes**
3.  **Acknowledgement Number Final:** **106**
### Descripción de Eventos al Recibir el Último Paquete (ACK: 106)

A continuación, se describe lo que ocurre en cada capa del computador del DCCiberCafé al recibir el último paquete HTTP.

#### **Capa de Entrada (Recepción de Datos)**

* **Capa 1:** Puerto FastEthernet recibe el *frame*.
* **Capa 2:**
    1.  La dirección MAC de destino es igual a: la dirección MAC del puerto que lo recibe, la dirección de *broadcast* o la dirección *multicast*.
    2.  El dispositivo des-encapsula el PDU del *ethernet frame*.
* **Capa 3:**
    1.  La dirección IP de destino del paquete es igual a la dirección IP del dispositivo o la dirección del *broadcast*. El dispositivo des-encapsula el paquete.
* **Capa 4:**
    1.  El dispositivo recibe un segmento **TCP PUSH+ACK** en la conexión a **90.120.12.2** en el puerto **80**.
    2.  Información del segmento recibido: número de secuencia **13321**, número de ACK **106**, *data length* **159**.
    3.  El segmento TCP tiene el *peer sequence number* esperado.
    4.  TCP procesa la data del *payload*.
    5.  TCP vuelve a ensamblar todos los segmentos de data y pasa a la capa superior.
* **Capa 7:**
    1.  El cliente **HTTP** recibe una respuesta HTTP desde el servidor. Muestra la página en el navegador web.


