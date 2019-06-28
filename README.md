# flow.cl-aspnetcore
Guía para implementar los servicios de Rest Api a tu aplicación en ASP.NET

## Comenzando 🚀
_Estas instrucciones te permitirán implementar los servicos rest de flow.cl a tu aplicación asp.net para hacer pagos._

### Requisitos e Instalación 📋


_Requisitos_
* Crear una cuenta en [Flow.cl](https://sandbox.flow.cl) (Esto es una cuenta de pruebas)
* **NO COMPARTAS TU API KEY Y SECRET KEY CON NADIE**

_Instalación_
* Abrir visual studio.
* Nuevo -> Proyecto.
* Web -> Aplicación web ASP.NET Core
* Selecciona la plantilla API y Listo.

### Manos a la obra ⌨️
_Toda documentación puedes encontrarla en [Flow.cl](https://www.flow.cl/docs/api.html/)_

Repasando los conceptos de rest y sus métodos HTTP
* **POST**: Crea un recurso
* **PUT**: Edita un recurso
* **GET**: Obtiene un recurso
* **DELETE**: Elimina un recurso

**Toda petición enviada llegará como respuesta un formato JSON**

### Ambientes ##
Flow nos propociona 2 ambientes para trabajar una de **PRODUCCIÓN** y otro de **DESARROLLO**, asi que usamos la de desarrollo.

### Implementación 🔧 ###

#### Controlador ####
* En la carpeta Controllers de tu proyecto crea un nuevo directorio y llamala FlowManagement.
* Dentro de la carpeta que creaste, crea un controlador.
* Selecciona **Controlador de API con acciones de lectura y escritura**
* Por ultimo, llamala CustomersController

Según la documentación de FLOW antes de enviar una petición, debes ordenar los parámetros de forma alfábetica yy ir firmarlo con
el método **hmac** pero menos la **S**

**Ejemplo**
