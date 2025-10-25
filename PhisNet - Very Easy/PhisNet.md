--------------

Empezamos revisando la situación en la que nos deja el sherlock: 

![[PhisNet/PhisNet - Very Easy/2.png]]

***Un equipo de contabilidad recibe una solicitud de pago de un proveedor conocido. El correo parece real pero tiene un enlace extraño y un archivo '.zip' que tiene malware.***

Ya sabemos lo que pasa, ahora, a darle. 

----

Nos descargamos el archivo '.zip': 

![[PhisNet/PhisNet - Very Easy/3.png]]

Al descomprimirlo, nos encontramos con esto: 

![[PhisNet/PhisNet - Very Easy/4.png]]

Hay un archivo llamado **email.eml**, es un tipo de archivo que contiene *correo un electrónico completo* que se guarda en un formato estándar. 

Aquí podemos ver parte del cuerpo del correo: 

![[PhisNet/PhisNet - Very Easy/5.png]]

Podemos ver que hay una dirección IP: **45.67.89.10**, esta IP fue usada por el usuario que envió el correo, y es la *respuesta a la primera pregunta*. 

En la primera línea podemos ver la dirección del correo del remitente (finance@business-finance.com), y es la *respuesta a la tercera pregunta*.

En caso de que el usuario que reciba el correo quiera responder al remitente, debe responder a la dirección (support@business-finance.com), y es la *respuesta a la cuarta pregunta*.

También podemos ver la dirección IP:  **203.0.113.25**, esta IP fue usada por el usuario que recibió el correo, y es la *respuesta a la segunda pregunta*.

![[PhisNet/PhisNet - Very Easy/6.png]]

---

En este apartado quiero añadir un espacio porque la siguiente pregunta trata de confirmar cual es el resultado del **SPF (Server Policy Framework)** en el correo. 

El **SPF (Server Policy Framework)** es un sistema de autenticación de correo electrónico cuya función se basa en comprobar si el servidor que envía el correo tiene permitido enviar el correo al dominio del destino.

En este caso el resultado de dicho **SPF** resulta en *PASS*, es decir, que el servidor de origen puede enviar correos al servidor de destino, pero esto **NO** confirma que el contenido que se envíe sea legítimo. 

![[PhisNet/PhisNet - Very Easy/7.png]]

De hecho hablando de dominios, la siguiente pregunta trata de encontrar el dominio usado para enviar el correo malicioso, en este caso es el siguiente: 

![[PhisNet/PhisNet - Very Easy/8.png]]

También nos piden que encontremos el nombre de la falsa compañía que envió el correo malicioso, su nombre esta cerca del dominio falso:

![[PhisNet/PhisNet - Very Easy/9.png]]

La siguiente pregunta trata de que encontremos el nombre del archivo malicioso que se envió junto con el correo, recordemos que estamos buscando por un archivo con extensión **.zip**, así que solo hay que filtrar por esa extensión para encontrar lo que buscamos: 

```bash
cat email.eml | grep ".zip"
```

![[PhisNet/PhisNet - Very Easy/10.png]]

El siguiente paso es encontrar el **hash SHA-256** que fue adjuntado al correo, pero lo que nos encontramos es estos caracteres en base64,  de los que hay que sacar el hash:

![[PhisNet/PhisNet - Very Easy/11.png]]

Para ello primero, usé este código en Python para decodificar el archivo: 

```python
import base64, hashlib  
  
base64_attachment = base64.decodebytes(b'UEsDBBQAAAAIABh/WloXPY4qcxITALvMGQAYAAAAaW52b2ljZV9kb2N1bWVudC5wZGYuYmF0zL3ZzuzIsR18LQN+h62DPujWX0e7')  
hash = hashlib.sha256(base64_attachment).hexdigest()  
  
print(hash)
```

![[PhisNet/PhisNet - Very Easy/12.png]]

Ya tenemos el hash ahora, nos piden que encontremos el nombre de un archivo malicioso, para ello hacemos esto: 

Copiamos la cadena de caracteres que hay dentro del código de Python en un archivo de texto, para después decodificarlo en base64. De tal forma que el resultado se guardará en un .zip:

![[PhisNet/PhisNet - Very Easy/13.png]]

Ahora que tenemos el archivo, usamos este comando para leer los metadatos y de ahí sacar la información: 

![[PhisNet/PhisNet - Very Easy/14.png]]

Al final encontramos el nombre del archivo malicioso que se adjunto al correo.

----

Ya para terminar, debemos encontrar el ID usado para la técnica de Phising de este sherlock, para ello nos vamos a la web de **Mitre Att&ck** y filtramos por técnicas de phising: 

![[PhisNet/PhisNet - Very Easy/15.png]]

![[PhisNet/PhisNet - Very Easy/16.png]]

![[PhisNet/PhisNet - Very Easy/18.png]]

----

![[PhisNet/PhisNet - Very Easy/19.png]]

