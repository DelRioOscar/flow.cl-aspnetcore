# flow.cl-aspnetcore
Guía para implementar los servicios de Rest Api a tu aplicación en ASP.NET

## Comenzando 🚀
_Estas instrucciones te permitirán implementar los servicos rest de flow.cl a tu aplicación asp.net para hacer pagos._

### Requisitos e Instalación 📋

_Requisitos_
* Visual Studio
* Postman o Insominia (Cualquier cliente REST es a tu elección)
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

### Tools ###
* Crea una nueva carpeta llamada TOOLS en la raiz de tu proyecto,
* Crea una clase llamada FlowTool.
* Copia y pega esto en tu clase creada
```
public static class FlowTool
    {
        public static String GetHash(string text, String key)
        {
            UTF8Encoding encoding = new UTF8Encoding();
            Byte[] keyBytes = encoding.GetBytes(key);
            Byte[] hashBytes = encoding.GetBytes(text);
            using (HMACSHA256 hash = new HMACSHA256(keyBytes)) hashBytes = hash.ComputeHash(hashBytes);
            return BitConverter.ToString(hashBytes).Replace("-", "").ToLower();
        }

        public static string QueryString(IDictionary<string, string> dict)
        {
            var list = new List<string>();
            foreach (var item in dict)
            {
                list.Add(item.Key + "=" + item.Value);
            }
            return string.Join("&", list);
        }
    }
```
El método GetHash() nos ayudará a crear el hash para los parámetros y QueryString() para generar una URL, ¿No entiendes? no te estreses
ya véras que más adelante sabrás para que ocupamos esto.

Según la documentación de FLOW antes de enviar una petición, debes ordenar los parámetros de forma alfábetica y firmarlo con
el método **hmac** pero menos la **S**

![Ejemplo](https://i.ibb.co/X712zsp/Ejemplo1.png)

Asi que vamos a crear un cliente en la página en la página de flow usando peticiones http.

* Así debe estar tu appsetting.json, la ventaja de usar este archivo es que en cualquier momento que quieras cambiar tu apiKey 
cambiará hasta en el código fuente sin tener que estresarte y pasar en hacer el cambio.

```
{
  "Flow": {
    "ApiKey": "TU API KEY AQUÍ",
    "SecretKey": "TU SECREY KEY",
    "Server": "https://sandbox.flow.cl/api"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}

```
* Así debe quedar CustomersControllers.cs
```
        [HttpPost]
        public async Task<IActionResult> PostCustomerAsync([FromBody] String clientId)
        {

            // La documentación de flow dice que debemos ordenar los parametros que vamos enviar, así que aquí da lo mismo, SortedDictionary lo hace por ti,
            var values = new SortedDictionary<string, string>
            {
               { "apiKey", _configuration["Flow:ApiKey"] },
               { "email", "juan_perez@onfire.cl" },
               { "externalId", clientId },
               { "name", "Juan" }
            };

            // Intanciar el metodo QueryString hacer los parametros.
            string x = FlowTool.QueryString(values);
            
            // Agregar al diccionario el parametro S.
            values.Add("s", GenerateHash.GetHash(x, _configuration["Flow:SecretKey"]));
            
            // Como ya esta incluido el parametro s en el SortedDictionary se lo pasamos al objeto FormUrlEncodedContent.
            var content = new FormUrlEncodedContent(values);

            using (var http = new HttpClient())
            {
                // El resultado no entregará la respuesta que viene desde el servidor de flow si no otros detalles, para obtenerlo se usa el metodo ReadAsAsync;
                var result = await http.PostAsync(_configuration["Flow:Server"] + "/customer/create", content);
                var response = await result.Content.ReadAsAsync<Object>();
                return Ok(response);
            }
        }
```
