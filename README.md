# Tarea 3 - Redes

## Respuestas de la Simulación

### Paquete Simple PDU

#### Resumen
1. **Tamaño del paquete:** 32 bits
2. **Dirección IP de destino:** 172.20.0.2

#### Detalle por capas
- **Capa de Entrada:**
  - **Capa 1:**
    - Puerto FastEthernet recibe el frame.
  - **Capa 2:**
    - La dirección MAC de destino es igual a: la dirección MAC del puerto que lo recibe, la dirección del broadcast o dirección del multicast.
    - El dispositivo DNS des-encapsula el PDU desde el frame Ethernet.
  - **Capa 3:**
    - La dirección IP de destino es igual a: la dirección IP del dispositivo o la dirección del broadcast.
    - El dispositivo des-encapsula el paquete.
    - El paquete es tipo ICMP. El proceso ICMP lo procesa.
    - El proceso ICMP recibió un mensaje Echo Request.

- **Capa de Salida:**
  - **Capa 3:**
    - El proceso ICMP responde al Echo Request armando un tipo ICMP para un Echo Reply.
    - El proceso ICMP envía un Echo Reply.
    - La dirección IP de destino 172.20.0.2 no es la misma subred y no es la dirección de broadcast.
    - Se asigna el default gateway. El dispositivo asigna el siguiente salto al default gateway.
  - **Capa 2:**
    - La dirección IP del siguiente salto es un unicast. El proceso ARP lo busca en la tabla ARP.
    - La dirección IP del siguiente salto se encuentra en la tabla ARP. El proceso ARP asigna la dirección MAC de destino del frame a la que se encontró en la tabla.
    - El dispositivo encapsula el PDU en un ethernet frame.
  - **Capa 1:**
    - El puerto FastEthernet0 envía el frame.

---

### Envío y Recepción de Email

#### Tipos de paquetes
1. **DNS:** Se usa al inicio para encontrar el nombre de dominio del servidor de correo y obtener su dirección IP necesaria para el envío y la recepción.
2. **SMTP:** Se utiliza en la fase de envío. Es el protocolo que transporta el mensaje del correo electrónico desde el PC del Profesor.
3. **POP3:** Se utiliza en la fase de recepción. Es el protocolo que permite al PC en que se conectó el alumno autenticarse y descargar el mensaje desde el Servidor.

#### Protocolos de red utilizados
- **TCP:**
  - Protocolo clave para el servicio de correo, ya que entrega la confiabilidad necesaria para SMTP y POP3.
  - Su función comienza con el establecimiento de la conexión para garantizar que el cliente y servidor estén listos.
  - Divide el correo electrónico en segmentos, asegura que lleguen en el orden correcto mediante el uso de números de secuencia, gestiona el control de flujo y la retransmisión en caso de que se pierdan datos.
  - Cierra la conexión de forma ordenada, garantizando que el mensaje completo se haya enviado o recibido sin errores.

#### Flujo de comunicación

- **Desde el dispositivo de la casa del profesor (laptop) hacia el servidor:**
  1. Se genera un paquete DNS, pero como no se conoce la traducción MAC de la dirección IP, se envía un ARP request.
  2. El dispositivo envía un paquete ARP como broadcast dentro de la red local para conocer la dirección MAC del router gateway.
  3. El router gateway responde con un ARP reply.
  4. El dispositivo envía el paquete DNS al servidor DNS, que responde con la IP correspondiente al dominio consultado.
  5. Se establece una conexión TCP con el servidor UC mediante un intercambio de paquetes TCP SYN, SYN+ACK y ACK.
  6. El dispositivo envía un paquete SMTP para transmitir el correo.
  7. El servidor SMTP confirma la recepción del correo.

- **Desde el dispositivo del DCCyber Cafe (PC) al servidor:**
  - El proceso es similar, con las siguientes diferencias:
    - No se envía un paquete ARP porque las direcciones MAC son conocidas.
    - En lugar de SMTP, se utiliza POP3 para descargar los correos entrantes.
    - En la casa del profesor, la conexión wireless propaga paquetes a otros dispositivos, pero estos los descartan si no son los destinatarios.

#### Necesidad de los protocolos y paquetes
- **DNS:** Traduce nombres de dominio a direcciones IP.
- **SMTP y POP3:** Definen el idioma de la aplicación para enviar y recibir correos.
- **TCP:** Asegura la integridad de los datos durante la transmisión.

---

### Página Web

#### Resumen
1. **Tamaño del paquete:** 32 bits
2. **Número de paquetes:** 10
3. **Acknowledgement number:** 106

#### Detalle por capas
- **Capa de Entrada:**
  - **Capa 1:**
    - Puerto FastEthernet recibe el frame.
  - **Capa 2:**
    - La dirección MAC de destino es igual a: la dirección MAC del puerto que lo recibe, la dirección de broadcast o la dirección multicast.
    - El dispositivo des-encapsula el PDU del ethernet frame.
  - **Capa 3:**
    - La dirección IP de destino del paquete es igual a la dirección IP del dispositivo o la dirección del broadcast.
    - El dispositivo des-encapsula el paquete.
  - **Capa 4:**
    - El dispositivo recibe un segmento TCP PUSH+ACK en la conexión a 90.120.12.2 en el puerto 80.
    - Información del segmento recibido: número de secuencia 13321, número de ACK 106, data length 159.
    - El segmento TCP tiene el peer sequence number esperado.
    - TCP procesa la data del payload.
    - TCP vuelve a ensamblar todos los segmentos de data y pasa a la capa superior.
  - **Capa 7:**
    - El cliente HTTP recibe una respuesta HTTP desde el servidor.
    - Muestra la página en el navegador web.


