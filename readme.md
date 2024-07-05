# APAy Transactor api

Para poder usar la API tu primer paso debe ser registrarse en

<a href=" https://aventupay.com/views/auth/register/">
<img src="https://aventupay.com/assets/img/logo/APay_color.png" width="200px"></a>


 una vez registrado debes tomar el token de tu cuenta que podrás encontrar en tu perfil en el apartado de API



```sh
    Token: "tu token de cuenta"
```

## Webhook 

En el apartado de tu peril en el sector de API tu deberas ingresar el endpoint al que se te dara la respuesta de la transacción, esta puede ser 1 o 2, donde 2 es la transacción exitosa y 1 es la transacción invalida, ten presente que la petición que debes esperar es método put, los campos se mandan por body y son los siguientes:


```sh
{
    "link":"id_transacción",
    "status":"si la transacción fue exitosa 2 o 1 si fue denegada"
}
```

## APay Links

Para crear tus links personalizados desde tu app debes enviar la siguiente información al endpoint que se te compartira al iniciar tu proceso,

- id_business: representa el id del negocio, este puede ser tu porpio id de la cuenta o puede ser un id de tu cliente, recuerda que esto se debe tomar del apartado de tu perfil
- product_api: envia por array los nombres de los productos que deseas mostrarles a tus clientes
- id_client: representa el identificador del cliente en este caso sera 450 como identificar de enlace por api
- name: nombre del enlace
- description: descripción del link de pago
- amount: monto a pagar
- expiration: fecha de expiración del link de pago
- providerApi: tu id como proveedor certificado de APay

```sh

{
    "id_business": "xxxxxx",
    "product_api": "['Demo1','demo2']",
    "id_client": "450",
    "name": "Link demo 2",
    "description": "demo de link",
    "amount": "45.00",
    "expiration": "2023-10-12 16:49:39",
    "providerApi": "xxxxxx"
}

```
Si tu transación es exitosa recibiras el siguiente mensaje:
```sh
{ 
    msg: "success",
    id_publico:"id del link creado"
}
```


## Transactor APay

En el siguiente ejemplo te mostramos una transacción basica hacia el motor de pago donde deberas enviar la siguiente información:

- monto: monto a pagar **Importante el monto debe ir con 2 dígitos máximo por ejemplo 4.50 si envías 4.505 tendrás un error**
- card_number: numero de tarjeta de credito
- cvv: numero de cvv
- exp_date: fecha de expiración que va de año/mes
- cardholderName: nombre del titular de la tarjeta
- token: tu id como proveedor certificado de APay
- ambiente: donde 0 es pruebas y 1 es producción



```sh
        var myHeaders = new Headers();
        myHeaders.append("Content-Type", "application/x-www-form-urlencoded");

        var urlencoded = new URLSearchParams();
        urlencoded.append("monto", "1");
        urlencoded.append("card_number", "5100271000000120");
        urlencoded.append("cvv", "168");
        urlencoded.append("exp_date", "2501");
        urlencoded.append("cardholderName", "Jhon Doe");
        urlencoded.append("token", "xxxx");
        urlencoded.append("ambiente", "0");

        var requestOptions = {
        method: 'POST',
        headers: myHeaders,
        body: urlencoded,
        redirect: 'follow'
        };

        
        fetch("url_apay", requestOptions)
        .then(response => response.text())
        .then(result => {
            let resultado=JSON.parse(result)
            let id_transaction=resultado.id
            document.getElementById("ifra").src=resultado.url;

        })
        .catch(error => console.log('error', error));
       }
```

Es importante que crees un modal para poder cerrar tu transacción de manera exitosa, ya que APay te respondera una url que deberas usar para enviar a tu iframe y mostrar los resultados generados de tu transacción como vemos en el ejemplo de abajo, donde 

```sh
resultado.url
resultado.id
 document.getElementById("ifra").src=resultado.url;
```

nos devuelve la url a colocar en nuestro iframe y el id de la transacción que has inicializado, recuerda que la respuesta de tu transacción es enviada al endpoint que se te solicita en tu perfil para realizar la actualización del estado de tu transacción.

Todas las transacciones quedaran reflejadas en tu cuenta de APay.

## Tokenización
Puedes resguardar la información de tus clientes en APay para reutilizar la tarjeta en tu app con nuestros tokens. Para tokenizar una tarjeta, debes enviar la siguiente información:

- idBusiness: Tu token.
- cardHolderName: Nombre del titular de la tarjeta.
- cardNumber: Número de tarjeta enmascarado (ej. xxxx-xxxx-xxxx-xxxx).
- expirationDate: Fecha de expiración (yymm, ej. 2501).
- cvv: CVV de la tarjeta.
- status: Estado (1 para activo).

```sh
{
    "idBusiness": "tu-token",
    "cardHolderName": "Jhon Doe",
    "cardNumber": "xxxx-xxxx-xxxx-xxxx",
    "expirationDate": "2501",
    "cvv": "123",
    "status": 1
}

```
**IMPORTANTE**: todos los valores debes enviarlos como texto 

**IMPORTANTE**: cardHolderName no puede llevar caracteres especiales

## Suscribción
Puedes crear una suscripción en APay para tener un pago recurrente en nuestra plataforma. Primero, debes crear una suscripción:
![enter image description here](https://aventupay.com/assets/img/elements/suscrip2.png)

Una vez creada, tendrás un identificador único que deberás enviar en la creación de tus tarjetas de crédito tokenizadas. Por ejemplo:
```sh
{
    "idBusiness": "tu-token",
    "idLink": "identificador de suscripción",
    "cardHolderName": "Jhon Doe",
    "cardNumber": "xxxx-xxxx-xxxx-xxxx",
    "expirationDate": "2501",
    "cvv": "123",
    "status": 1
}


```
**idLink** representa el identificador de tu suscripción. Si creas la tarjeta sin este, no tendrá un pago recurrente.

## Errores
 A continuación se detallan algunos de los errores que pueden ocurrir durante el uso de la API:

### 1. Tarjeta Inválida

**Estatus: 400**

**Descripción:** Este error se produce cuando se proporciona un número de tarjeta inválido.

**Causa:** El número de tarjeta proporcionado no cumple con el formato o la validación requerida.

**Ejemplo de respuesta:**
```json
{
  "msg":"Error: Tarjeta Invalida"
}
```

### 2. Fecha de Expiración no Válida
**Estatus: 400**

**Descripción:** Este error ocurre cuando se proporciona una fecha de expiración que no cumple con los criterios de validez.

**Causa:** La fecha de expiración debe ser proporcionada en el siguiente formato yymm donde primer debe ir el año 25 y luego el mes 01 dando un resultado de 2501, recuerda mostrar en la vista del front-end mmyy pero al enviarlo debes pasarlo a yymm para evitar conflictos

**Ejemplo de respuesta:**
```json
{
 "msg":"Fecha de expiración no valida"
}
```

### 3. CVV no Válido
**Estatus: 400**

**Descripción:** Este error se produce cuando se proporciona un CVV inválido.

**Causa:** El CVV proporcionado no cumple con los requisitos de validez de 3 a 4 digitos.

**Ejemplo de respuesta:**
```json
{
 "msg":"CVV no valido"
}
```

### 4. Monto de Tarjeta no Válido
**Estatus: 400**

**Descripción:** Este error se genera cuando se proporciona un monto a transacciones no válido.

**Causa:** El monto proporcionado no cumple con los requisitos establecidos, este monto debe ser mayor a 1.

**Ejemplo de respuesta:**
```json
{
 "msg":"Monto de tarjeta no valido"
}
```

