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

- monto: monto a pagar
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