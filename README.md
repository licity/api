# Manual para el uso de la plataforma para empresas de Licity
Esta documentación muestra el uso de la plataforma para la firma de contratos de Licity. La plataforma permite la firma de contratos mediante el uso de un token de acceso proporcionado por Licity.

Esta documentación y código de ejemplo se encuentra disponible en la URL https://www.licity.co/api/

Todas las peticiones a la plataforma deben de realizarse vía POST y responderá en formato HTML y/o JSON.

## Instalación
Obtén la última versión de esta plataforma:

    git clone https://github.com/licity/api

## Uso

A continuación se describen las distintas funcionalidades que proporciona la plataforma.

### A) Muestra preliminar del contrato y solicitud de firma y foto
Esta primera etapa de la plataforma es una aplicación web que puede ser mostrada de forma directa en un navegador de escritorio o dispositivo móvil, así como puede ser llamada desde una webview dentro de una aplicación móvil.

Al momento de contratar el servicio con Licity, la empresa deberá de proporcionar la plantilla de su contrato que deberá de contener etiquetas del tipo `[ETIQUETA]` que serán reemplazadas por los valores enviados vía POST.

- **URL:** http://www.licity.co/api/contrato/muestra/[ID_Plantilla] (El ID de la plantilla será proporcionado por Licity)
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **urlRespuesta:** URL a desplegar una vez que el cliente / usuario a completado la firma del contrato de manera exitosa.
  - **emailCliente:** Correo electrónico del cliente / usuario a donde se enviará original del contrato firmado.
  - **nombreCliente:** Nombre completo del cliente / usuario que firma el contrato.
  - **datos:** datos para reemplazar las etiquetas presentes en el contrato. Los datos deben de venir en formato JSON ej:
      
      ```json
      {
      	"NOMBRE":"Luis Cárdenas",
      	"FECHAREGISTRO":"12 de enero de 2017"
      }
      ```
- **Respuesta:**
  - **Muestra exitosa:**
    - **Formato:** HTML.
    - **Contenido:** Se muestra vista previa del contrato con canvas para firma, botón para capturar fotografía y botón de continuar.
  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **La plantilla solicitada no existe**

      Se genera cuando el ID de la plantilla solicitada no existe o no pertenece a la empresa.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "La plantilla seleccionada no existe"
      }
      ```

      **Los datos enviados son incorrectos**

      Se genera cuando el token enviado no es correcto o no se encuentra vigente. Es necesario validar que el token de acceso sea correcto.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "Los datos enviados son incorrectos"
      }
      ```
- **Funcionalidad:**
Una vez que el usuario firme el contrato y se tome la fotografía, la aplicación mostrará la urlRespuesta y enviará por correo electrónico el PDF del original del contrato al cliente / usuario y otro a la dirección de correo electrónico que tengan configurada en nuestra plataforma.

- **Código muestra:** 
Incluimos un formulario HTML de muestra en que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/prueba.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de creación de contrato</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/contrato/muestra/[ID PLANTILLA CONTRATO]" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="text" name="urlRespuesta" id="urlRespuesta" value="" style="width:300px; height: 30px; padding:5px;" placeholder="URL Respuesta (ej. https://www.licity.co)" /><br /><br />
			<textarea name="datos" id="datos" style="width:400px; height:400px;">{ "ETIQUETA":"Valor reemplazo 1","ETIQUETA2":"Valor reemplazo 2" }</textarea><br /> <br />
			<input type="email" name="emailCliente" id="emailCliente" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Email Cliente" /><br /><br />
			<input type="text" name="nombreCliente" id="nombreCliente" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Nombre Cliente" /><br /><br />
			<input type="SUBMIT" value="Firmar Contrato" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```

### B) Descarga de contrato
Esta sección de la plataforma permite la descarga de un contato a partir de su ID.

- **URL:** http://www.licity.co/api/contrato/[ID_Contrato] (El ID del contrato se obtiene utilizando el listado de contratos descrito porsteriormente)
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
- **Respuesta:**
  - **Muestra exitosa:**
    - **Formato:** PDF.
    - **Contenido:** Se muestra el PDF del contrato firmado para su descarga.
  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **El contrato solicitado no existe o no pertenece a la empresa**

      Se genera cuando el ID de la plantilla solicitada no existe o no pertenece a la empresa.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "El contrato no existe"
      }
      ```

      **Los datos enviados son incorrectos**

      Se genera cuando el token enviado no es correcto o no se encuentra vigente. Es necesario validar que el token de acceso sea correcto.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "Los datos enviados son incorrectos"
      }
      ```
- **Código muestra:** 
Incluimos un formulario HTML de muestra en que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/contrato.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de descarga de contrato</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/contrato/[ID CONTRATO]" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="SUBMIT" value="Descarga Contrato" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

### C) Lista de contratos
Esta sección de la plataforma permite listar los contratos que pertenecen al TOKEN enviado.

- **URL:** http://www.licity.co/api/contrato 
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **fechaInicio:** AAAA-MM-DD (La fecha a partir de la cuál se quiren obtener contratos, la fecha corresponde a la fecha de firma del contrato).
  - **fechaFin:** AAAA-MM-DD (La fecha límite para obtener contratos, la fecha corresponde a la fecha de firma del contrato).
- **Respuesta:**
  - **Lista de contratos exitosa:**
    - **Formato:** JSON.
    - **Contenido:** Se muestra el estatus, el número de resultados y un array con los ID's de contratos junto con su fecha de firma, nombre y correo electrónico del cliente a partir del rango seleccionado.
	```JSON
	{
		"estatus" : "OK",
		"resultados" : 2,
		"contratos" : [
						{
							"ID" : 123,
							"fechaFirma" : "2017-01-12",
							"nombre" : "Luis Cárdenas",
							"email" : "luis@licity.co"
						},
						{
							"ID":124,
							"fechaFirma" : "2017-01-13",
							"nombre" : "Víctor Castañeda",
							"email" : "victor@licity.co"
						}
					  ]
	}
	```
  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **El rango de fecha ingresado no es correcto**

      Se genera cuando las fechas ingresadas no son correctas.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "Las fechas ingresadas no son correctas"
      }
      ```

      **Los datos enviados son incorrectos**

      Se genera cuando el token enviado no es correcto o no se encuentra vigente. Es necesario validar que el token de acceso sea correcto.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "Los datos enviados son incorrectos"
      }
      ```
- **Código muestra:** 
Incluimos un formulario HTML de muestra en que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/contratos.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de listado de contratos</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/contrato/[ID CONTRATO]" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="text" name="fechaInicio" id="fechaInicio" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa la fecha de inicio" /><br /><br />
			<input type="text" name="fechaFin" id="fechaFin" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa la fecha fin" /><br /><br />
			<input type="SUBMIT" value="Lista Contratos" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

### D) Validación de contrato
Esta sección de la plataforma permite validar cualquier contrato generado desde nuestra plataforma.

- **URL:** http://www.licity.co/api/contrato/valida
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **documentId:** ID del contrato, se localiza al pie de página de cada una de las hojas del contrato.
  - **pdf:** Archivo binario del contrato en formato PDF.
- **Respuesta:**
  - **Lista de contratos exitosa:**
    - **Formato:** JSON.
    - **Contenido:** Se muestra el estatus de la solicitud y la validez del contrato.

      Dos posibles mensajes:
	  
      **El contrato es válido**

	  ```JSON
	  {
	  	"estatus" : "OK",
	  	"valido" : 1,
	  }
	  ```

      **El contrato no es válido**

	  ```JSON
	  {
	  	"estatus" : "OK",
	  	"valido" : 0,
	  }
	  ```

  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **El documentId ingresado no existe**

      Se genera cuando el Document ID ingresado no existe o no pertenece al Token enviado

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "El documentId ingresado no existe"
      }
      ```

      **Los datos enviados son incorrectos**

      Se genera cuando el token enviado no es correcto o no se encuentra vigente. Es necesario validar que el token de acceso sea correcto.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "Los datos enviados son incorrectos"
      }
      ```
- **Código muestra:** 
Incluimos un formulario HTML de muestra en que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/valida.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de listado de contratos</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/contrato/valida" method="post" enctype="multipart/form-data">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="file" name="pdf" id="pdf" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Selecciona el PDF del contrato" /><br /><br />
			<input type="SUBMIT" value="Validar Contrato" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

## Licencia
Desarrollado por [Licity S.C.](https://www.licity.co) Disponible en [Licencia MIT](licencia).
