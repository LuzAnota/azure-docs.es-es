---
title: 'API de HTTP en Durable Functions: Azure'
description: Aprenda a implementar API de HTTP en la extensión Durable Functions para Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: c2ffa623ad7a6c6da5b799d2c7d5f35c9f65e503
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215412"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API de HTTP en Durable Functions (Azure Functions)

La extensión Durable Task expone un conjunto de API de HTTP que sirve para realizar las siguientes tareas:

* Capturar el estado de una instancia de orquestación.
* Enviar un evento a una instancia de orquestación en espera.
* Terminar una instancia de orquestación en ejecución.

Cada una de estas API HTTP es una operación de webhook que se administra directamente mediante la extensión Durable Task. No son específicas de ninguna función de la aplicación de función.

> [!NOTE]
> Estas operaciones también se pueden invocar directamente mediante las API de administración de instancia de la clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Para más información, consulte el artículo sobre la [administración de instancias](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Detección de la dirección URL de la API de HTTP

La clase [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) clase expone una API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) que sirve para generar una carga de respuesta HTTP con vínculos a todas las operaciones compatibles. Este ejemplo de función que se desencadena mediante HTTP muestra cómo utilizar la API:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Estas funciones de ejemplo generan los siguientes datos de respuesta JSON. El tipo de datos de todos los campos es `string`.

| Campo             |DESCRIPCIÓN                           |
|-------------------|--------------------------------------|
| id                |Identificador de la instancia de orquestación. |
| statusQueryGetUri |Dirección URL del estado de la instancia de orquestación. |
| sendEventPostUri  |Dirección URL de generación del evento de la instancia de orquestación. |
| terminatePostUri  |Dirección URL de finalización de la instancia de orquestación. |
| rewindPostUri     |Dirección URL de rebobinado de la instancia de orquestación. |

Este es un ejemplo de respuesta:

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> El formato de las direcciones URL del webhook puede diferir en función de la versión del host de Azure Functions en ejecución. El ejemplo anterior es para el host de Azure Functions 2.x.

## <a name="async-operation-tracking"></a>Seguimiento de operaciones asincrónicas

La respuesta HTTP que se ha mencionado anteriormente está diseñada para ayudar a implementar API de HTTP asincrónicas de ejecución prolongada con Durable Functions. Esto se conoce a veces como *sondeo del patrón de consumo*. El flujo cliente/servidor funciona del siguiente modo:

1. El cliente emite una solicitud HTTP para iniciar un proceso de ejecución prolongada, como una función de orquestador.
2. El desencadenador HTTP de destino devuelve una respuesta HTTP 202 con un encabezado `Location` con el valor `statusQueryGetUri`.
3. El cliente sondea la dirección URL en el encabezado `Location`. Continúa viendo las respuestas HTTP 202 con encabezado `Location`.
4. Cuando la instancia termina (o se produce un error), el punto de conexión en el encabezado `Location` devuelve HTTP 200.

Este protocolo permite la coordinación de procesos de ejecución prolongada con clientes o servicios externos que admitan el sondeo de puntos de conexión HTTP y sigan al encabezado `Location`. Las partes fundamentales ya están integradas en la API de HTTP de Durable Functions.

> [!NOTE]
> De forma predeterminada, todas las acciones basadas en HTTP proporcionadas por [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) admiten el patrón estándar de operación asincrónica. Esta funcionalidad permite insertar una función durable de ejecución prolongada como parte del flujo de trabajo de Logic Apps. Más detalles sobre la compatibilidad de Logic Apps con los patrones asincrónicos de HTTP en la [documentación de los desencadenadores y las acciones del flujo de trabajo de Azure Logic Apps](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Referencia a las API de HTTP

Todas las API de HTTP que implementa la extensión tienen los siguientes parámetros. El tipo de datos de todos los parámetros es `string`.

| Parámetro  | Tipo de parámetro  | DESCRIPCIÓN |
|------------|-----------------|-------------|
| instanceId | URL             | Identificador de la instancia de orquestación. |
| taskHub    | Cadena de consulta    | Nombre de la [central de tareas](durable-functions-task-hubs.md). Si no se especifica, se toma el nombre de la central de tareas de la aplicación de función actual. |
| connection | Cadena de consulta    | **Nombre** de la cadena de conexión de la cuenta de almacenamiento. Si no se especifica, se toma el de la cadena de conexión predeterminada de la aplicación de función. |
| systemKey  | Cadena de consulta    | Clave de autorización necesaria para invocar la API. |
| showInput  | Cadena de consulta    | Parámetro opcional; solo solicitudes de instancia única. Si se establece en `false`, la entrada de ejecución no se incluirá en la carga de respuesta.|
| showHistory| Cadena de consulta    | Parámetro opcional; solo solicitudes de instancia única. Si se establece en `true`, el historial de ejecución de orquestación se incluirá en la carga de respuesta.|
| showHistoryOutput| Cadena de consulta    | Parámetro opcional; solo solicitudes de instancia única. Si se establece en `true`, las salidas de actividad se incluirán en el historial de ejecución de orquestación.|
| createdTimeFrom  | Cadena de consulta    | Parámetro opcional. Cuando se especifica, se filtra la lista de instancias devueltas que se crearon durante o después de la marca de tiempo ISO8601 especificada.|
| createdTimeTo    | Cadena de consulta    | Parámetro opcional. Cuando se especifica, se filtra la lista de instancias devueltas que se crearon antes o durante la marca de tiempo ISO8601 determinada.|
| runtimeStatus    | Cadena de consulta    | Parámetro opcional. Cuando se especifica, se filtra la lista de instancias devueltas según su estado en tiempo de ejecución. Para ver la lista de valores posibles del estado en tiempo de ejecución, consulte el tema [Consulta de instancias](durable-functions-instance-management.md). |
| top    | Cadena de consulta    | Parámetro opcional. Cuando se especifica, se dividen los resultados de la consulta en las páginas y se limita el número máximo de resultados por página. |

`systemKey`es una clave de autorización que el host de Azure Functions genera automáticamente. En concreto, concede acceso a las API de la extensión Durable Tasks y se administra igual que las [demás claves de autorización](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). La manera más sencilla de detectar el valor `systemKey` consiste en usar la API `CreateCheckStatusResponse` que se ha mencionado anteriormente.

Las siguientes secciones tratan las API de HTTP específicas que admite la extensión y ofrecen ejemplos de uso.

### <a name="get-instance-status"></a>Obtención del estado de la instancia

Para obtener el estado de una instancia de orquestación específica:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 200 (correcto)**: la instancia especificada está en estado completado.
* **HTTP 202 (aceptado)**: la instancia especificada está en curso.
* **HTTP 400 (solicitud incorrecta)**: se produjo un error en la instancia especificada o esta se ha finalizado.
* **HTTP 404 (no se encuentra)**: la instancia especificada no existe o no ha empezado a ejecutarse.
* **HTTP 500 (error interno del servidor)**: error de la instancia especificada con una excepción no controlada.

La carga de respuesta para los casos **HTTP 200** y **HTTP 202** es un objeto JSON con los siguientes campos:

| Campo           | Tipo de datos | DESCRIPCIÓN |
|-----------------|-----------|-------------|
| runtimeStatus   | string    | Estado en tiempo de ejecución de la instancia. Los valores son *En ejecución*, *Pendiente*, *Erróneo*, *Cancelado*, *Finalizado*, *Completado*. |
| input           | JSON      | Datos JSON usados para inicializar la instancia. Este campo es `null` si el parámetro de cadena de consulta `showInput` esté establecido en `false`.|
| customStatus    | JSON      | Datos JSON que se usan en el estado de orquestación personalizada. Este campo es `null` si no se establece. |
| output          | JSON      | Salida JSON de la instancia. Este campo es `null` si el estado de la instancia no es Completado. |
| createdTime     | string    | Hora a la que se creó la instancia. Usa la notación ampliada de ISO 8601. |
| lastUpdatedTime | string    | Hora a la que se almacenó la instancia por última vez. Usa la notación ampliada de ISO 8601. |
| historyEvents   | JSON      | Una matriz JSON que contiene el historial de ejecución de orquestación. Este campo es `null` a menos que el parámetro de cadena de consulta `showHistory` esté establecido en `true`. |

Este es un ejemplo de una carga de respuesta que incluye las salidas de historial de ejecución de orquestación y de actividad (a la que se ha aplicado formato para mejorar la legibilidad):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

La respuesta **HTTP 202** también incluye un encabezado de respuesta **Location** que hace referencia a la misma dirección URL que el campo `statusQueryGetUri` mencionado anteriormente.

### <a name="get-all-instances-status"></a>Obtención de todos los estados de instancias

También puede consultar todos los estados de instancias. Quite `instanceId` de la solicitud "Obtener el estado de la instancia". Los parámetros son los mismos que en "Obtener el estado de la instancia".

Es importante recordar que `connection` y `code` son parámetros opcionales. Si tiene la autenticación anónima en la función, no es necesario usar código.
Puede ignorar el parámetro de la cadena de consulta de conexión si no quiere usar ninguna cadena de conexión de almacenamiento de blob distinta a la definida en la configuración de la aplicación AzureWebJobsStorage.

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
GET /runtime/webhooks/durabletask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}
```

#### <a name="request-with-filters"></a>Solicitud con filtros

Puede filtrar la solicitud.

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&createdTimeFrom={createdTimeFrom}&createdTimeTo={createdTimeTo}&runtimeStatus={runtimeStatus,runtimeStatus,...}&showInput={showInput}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
GET /runtime/webhooks/durableTask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&createdTimeFrom={createdTimeFrom}&createdTimeTo={createdTimeTo}&runtimeStatus={runtimeStatus,runtimeStatus,...}&showInput={showInput}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>Response

Este es un ejemplo de cargas de respuesta, incluido el estado de orquestación (con formato para mejorar la legibilidad):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Esta operación puede ser muy costosa en términos de E/S de Azure Storage si hay muchas filas en la tabla Instancias. Puede encontrar más detalles sobre la tabla Instancias en la documentación [Rendimiento y escalado horizontal en Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table).
>

#### <a name="request-with-paging"></a>Solicitud con paginación

Puede establecer el parámetro `top` para dividir los resultados de la consulta en páginas.

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
GET /admin/extensions/DurableTaskExtension/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&top={top}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
GET /runtime/webhooks/durableTask/instances/?taskHub={taskHub}&connection={connection}&code={systemKey}&top={top}
```

Si no existe la página siguiente, se devuelve un token de continuación en el encabezado de respuesta.  El nombre del encabezado es `x-ms-continuation-token`.

Si establece el valor del token de continuación en el siguiente encabezado de solicitud, puede obtener la página siguiente.  Esta clave en el encabezado de solicitud es `x-ms-continuation-token`.

### <a name="raise-event"></a>Generación de eventos

Para enviar un mensaje de notificación de eventos a una instancia de orquestación en ejecución:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Los parámetros de solicitud de esta API incluyen el conjunto predeterminado mencionado anteriormente, así como los siguientes parámetros únicos:

| Campo       | Tipo de parámetro  | Tipo de datos | DESCRIPCIÓN |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | string    | Nombre del evento al que espera la instancia de orquestación de destino. |
| {content}   | Contenido de la solicitud | JSON      | Carga del evento con formato JSON. |

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 202 (aceptado)**: se aceptó el evento generado para su procesamiento.
* **HTTP 400 (solicitud incorrecta)**: el contenido de la solicitud no era del tipo `application/json` o no tenía un valor JSON válido.
* **HTTP 404 (no se encuentra)**: no se encontró la instancia especificada.
* **HTTP 410 (no existe)**: la instancia especificada ha finalizado o se ha producido un error y no puede procesar los eventos generados.

Esta es una solicitud de ejemplo que envía la cadena JSON `"incr"` a una instancia que espera un evento denominado **operation**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Las respuestas para esta API no tienen contenido.

### <a name="terminate-instance"></a>Finalización de instancias

Para terminar una instancia de orquestación en ejecución:

#### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Los parámetros de solicitud de esta API incluyen el conjunto predeterminado mencionado anteriormente, así como el siguiente parámetro único.

| Campo       | Tipo de parámetro  | Tipo de datos | DESCRIPCIÓN |
|-------------|-----------------|-----------|-------------|
| reason      | Cadena de consulta    | string    | Opcional. Motivo de finalización de la instancia de orquestación. |

#### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 202 (aceptado)**: se aceptó la solicitud de finalización para su procesamiento.
* **HTTP 404 (no se encuentra)**: no se encontró la instancia especificada.
* **HTTP 410 (no existe)**: la instancia especificada se ha completado o ha producido un error.

Esta es una solicitud de ejemplo que finaliza una instancia en ejecución y especifica un motivo **buggy**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Las respuestas para esta API no tienen contenido.

## <a name="rewind-instance-preview"></a>Rebobinado de instancias (versión preliminar)

Restaura una instancia de orquestación errónea a un estado de ejecución mediante la reproducción de las operaciones erróneas más recientes.

### <a name="request"></a>Solicitud

Para Functions 1.0, el formato de solicitud es el siguiente:

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

El formato de Functions 2.0 tiene los mismos parámetros, excepto un prefijo de dirección URL ligeramente distinto:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Los parámetros de solicitud de esta API incluyen el conjunto predeterminado mencionado anteriormente, así como el siguiente parámetro único.

| Campo       | Tipo de parámetro  | Tipo de datos | DESCRIPCIÓN |
|-------------|-----------------|-----------|-------------|
| reason      | Cadena de consulta    | string    | Opcional. Motivo para rebobinar la instancia de orquestación. |

### <a name="response"></a>Response

Se pueden devolver varios valores de código de estado.

* **HTTP 202 (aceptado)**: se aceptó la solicitud de rebobinado para su procesamiento.
* **HTTP 404 (no se encuentra)**: no se encontró la instancia especificada.
* **HTTP 410 (no existe)**: la instancia especificada se completó o se terminó.

Esta es una solicitud de ejemplo que rebobina una instancia errónea y especifica un motivo de **fixed**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Las respuestas para esta API no tienen contenido.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Aprenda a administrar los errores](durable-functions-error-handling.md)
