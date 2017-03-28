# Manual de Uso - Plataforma para Empresas Licity
Esta documentación muestra el uso de la plataforma para empresas de Licity (la "Plataforma Licity") para la firma electrónica y gestión de documentos. La plataforma permite (i) la firma electrónica de documentos; (ii) la descarga y consulta de documentos firmados; y (iii) la validación de documentos firmados, mediante el uso de un token de acceso proporcionado por Licity.

Esta documentación y código de ejemplo se encuentra disponible en la URL https://github.com/licity/api/

Todas las peticiones a la Plataforma Licity deben de realizarse vía POST y responderá en formato HTML y/o JSON.

Tabla de Contenidos
===================

  - [Instalación](#instalacion)
  - [Uso](#uso)
    - [A) Muestra preliminar del documento y solicitud de firma y foto](#a-muestra-preliminar-del-documento-y-solicitud-de-firma-y-foto)
	- [B) Descarga de documento](#b-descarga-de-documento) 
	- [C) Lista de documentos](#c-lista-de-documentos)
	- [D) Validación de documento](#d-validacion-de-documento)
  - [Licencia](#licencia)

## Instalación
Obtén la última versión de la Plataforma Licity:

    git clone https://github.com/licity/api

## Uso

A continuación se describen las distintas funcionalidades que proporciona la Plataforma Licity.

### A) Muestra preliminar del documento y solicitud de firma y foto
Esta primera versión de la Plataforma Licity se resuelve en una aplicación web que puede ser mostrada de forma directa en un navegador de escritorio o dispositivo móvil, así como puede ser llamada desde una webview dentro de una aplicación móvil.

Al momento de contratar el servicio con Licity, la empresa deberá de proporcionar la plantilla del documento a ser automatizado y que deberá de contener etiquetas del tipo `[ETIQUETA]` que serán reemplazadas por los valores enviados vía POST.

- **URL:** http://www.licity.co/api/documento/muestra/[ID_Plantilla] (El ID de la plantilla será proporcionado por Licity)
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **urlRespuesta:** URL a desplegar una vez que el usuario del cliente ha completado la firma del documento de manera exitosa.
  - **emailCliente:** Correo electrónico del usuario del cliente a donde se enviará original del documento firmado.
  - **nombreCliente:** Nombre completo del usuario del cliente que firma el documento.
  - **datos:** Datos para reemplazar las etiquetas presentes en el documento. Los datos deben de venir en formato JSON ej:
      
      ```json
      {
      	"NOMBRE":"Luis Cárdenas",
      	"FECHAREGISTRO":"12 de enero de 2017"
      }
      ```
- **Respuesta:**
  - **Muestra exitosa:**
    - **Formato:** HTML.
    - **Contenido:** Se muestra vista previa del documento con canvas para firma, botón para capturar fotografía y botón de continuar.
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
Una vez que el usuario del cliente firme el documento y se tome la fotografía, la aplicación mostrará la urlRespuesta y enviará por correo electrónico el PDF del original del documento al usuario del cliente y a la dirección de correo electrónico que el cliente tenga dada de alta en la Plataforma Licity.

- **Código muestra:** 
Incluimos un formulario HTML de muestra en el que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/prueba.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de creación de documento</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/documento/muestra/[ID PLANTILLA DOCUMENTO]" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="text" name="urlRespuesta" id="urlRespuesta" value="" style="width:300px; height: 30px; padding:5px;" placeholder="URL Respuesta (ej. https://www.licity.co)" /><br /><br />
			<textarea name="datos" id="datos" style="width:400px; height:400px;">{ "ETIQUETA":"Valor reemplazo 1","ETIQUETA2":"Valor reemplazo 2" }</textarea><br /> <br />
			<input type="email" name="emailCliente" id="emailCliente" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Email Cliente" /><br /><br />
			<input type="text" name="nombreCliente" id="nombreCliente" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Nombre Cliente" /><br /><br />
			<input type="SUBMIT" value="Firmar Documento" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```

### B) Descarga de documento
Esta sección de la Plataforma Licity permite la descarga de un documento firmado y definitivo a partir de su ID.

- **URL:** http://www.licity.co/api/documento/[ID_Documento] (El ID del documento se obtiene utilizando el listado de documentos descrito porsteriormente)
- **Variables POST requeridas:**
  - **token:** Proporcionado por Licity.
- **Respuesta:**
  - **Muestra exitosa:**
    - **Formato:** PDF.
    - **Contenido:** Se muestra el PDF del documento firmado para su descarga.
  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **El documento solicitado no existe o no pertenece a la empresa**

      Se genera cuando el ID de la plantilla solicitada no existe o no pertenece a la empresa.

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "El documento no existe"
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
Incluimos un formulario HTML de muestra en que pueden hacer pruebas de uso. Este código se encuentra [aquí](codigo_muestra/documento.html).

  ```HTML
  <!DOCTYPE html>
  <html>
  	<head>
		<meta charset="utf-8" />
		<meta name="viewport" content="width=device-width" />
		<title>Prueba de descarga de documento</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/documento/[ID DOCUMENTO]" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="SUBMIT" value="Descarga Documento" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

### C) Lista de documentos
Esta sección de la plataforma permite listar los documentos que pertenecen al TOKEN enviado.

- **URL:** http://www.licity.co/api/documento 
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **fechaInicio:** AAAA-MM-DD (La fecha a partir de la cuál se quieren obtener documentos, la fecha corresponde a la fecha de firma del documento).
  - **fechaFin:** AAAA-MM-DD (La fecha límite para obtener documentos, la fecha corresponde a la fecha de firma del documento).
- **Respuesta:**
  - **Lista de documentos exitosa:**
    - **Formato:** JSON.
    - **Contenido:** Se muestra el estatus, el número de resultados y un array con los ID's de documentos junto con su fecha de firma, ID del documento, nombre y correo electrónico del cliente a partir del rango seleccionado.
	```JSON
	{
		"estatus" : "OK",
		"resultados" : 2,
		"contratos" : [
						{
							"ID" : 123,
							"fechaFirma" : "2017-01-12",
							"IDdocumento" : "094d8449b5e354251cdab191f3b8611fb8f24875a581367f2728df6b8921495h",
							"nombre" : "Luis Cárdenas",
							"email" : "luis@licity.co"
						},
						{
							"ID":124,
							"fechaFirma" : "2017-01-13",
							"IDdocumento" : "094d8449b5e354251cdab191f3b8611fb8f24875a581367f2728df6b8921495g",
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
		<title>Prueba de listado de documentos</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/documento" method="post">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="date" name="fechaInicio" id="fechaInicio" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa la fecha de inicio" /><br /><br />
			<input type="date" name="fechaFin" id="fechaFin" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa la fecha fin" /><br /><br />
			<input type="SUBMIT" value="Lista Documentos" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

### D) Validación de documento
Esta sección de la plataforma permite validar cualquier documento generado desde nuestra plataforma.

- **URL:** http://www.licity.co/api/documento/valida
- **Varibles POST requeridas:**
  - **token:** Proporcionado por Licity.
  - **IDdocumento:** ID Documento del documento, se localiza al pie de página de cada una de las hojas del documento firmado.
  - **pdf:** Archivo binario del documento firmado en formato PDF.
- **Respuesta:**
  - **Validación de documento exitosa:**
    - **Formato:** JSON.
    - **Contenido:** Se muestra el estatus de la solicitud y la validez del documento.

      Dos posibles mensajes:
	  
      **El documento es válido**

	  ```JSON
	  {
	  	"estatus" : "OK",
	  	"valido" : 1
	  }
	  ```

      **El documento no es válido**

	  ```JSON
	  {
	  	"estatus" : "OK",
	  	"valido" : 0
	  }
	  ```

  - **Error:**
    - **Formato:** JSON.
    - **Contenido:**

      Dos posibles mensajes de error:

      **El IDdocumento ingresado no existe**

      Se genera cuando el ID Documento ingresado no existe o no pertenece al Token enviado

      ```json
      {
      	"estatus" : "ERROR",
      	"mensaje" : "El IDdocumento ingresado no existe"
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
		<title>Prueba de listado de documentos</title>
	</head>
	<body>
		<form action="https://www.licity.co/api/documento/valida" method="post" enctype="multipart/form-data">
			<input type="text" name="token" id="token" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Ingresa el token proporcionado por Licity" /><br /><br />
			<input type="file" name="pdf" id="pdf" value="" style="width:300px; height: 30px; padding:5px;" placeholder="Selecciona el PDF del documento" /><br /><br />
			<input type="SUBMIT" value="Validar Contrato" style="width:200px; height:30px;" />
		</form>
	</body>
  </html>
  ```      

## Licencia
Desarrollado por [Licity S.A. de C.V.](https://www.licity.co) Disponible en [Licencia MIT](licencia).
