---
title: Compilación de API web con ASP.NET Core
author: scottaddie
description: Obtenga información sobre las características disponibles para la compilación de una API web en ASP.NET Core y los casos en los que se recomienda usar cada una.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/15/2018
uid: web-api/index
ms.openlocfilehash: e4615e5d416ba2433d55309b25ee3643c6c636ac
ms.sourcegitcommit: 375e9a67f5e1f7b0faaa056b4b46294cc70f55b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2018
ms.locfileid: "50207009"
---
# <a name="build-web-apis-with-aspnet-core"></a>Compilación de API web con ASP.NET Core

Por [Scott Addie](https://github.com/scottaddie)

[Vea o descargue el código de ejemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/define-controller/samples) ([cómo descargarlo](xref:index#how-to-download-a-sample))

En este documento se explica cómo crear una API web en ASP.NET Core y los casos en los que se recomienda usar cada una.

## <a name="derive-class-from-controllerbase"></a>Derivación de una clase desde ControllerBase

Herede desde la clase <xref:Microsoft.AspNetCore.Mvc.ControllerBase> en un controlador diseñado para funcionar como API web. Por ejemplo:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_PetsController&highlight=3)]

::: moniker-end

La clase `ControllerBase` proporciona acceso a varios métodos y propiedades. En el código anterior, algunos ejemplos son <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest(Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary)> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction(System.String,System.Object,System.Object)>. Se llama a estos métodos en los métodos de acción para devolver los códigos de estado HTTP 400 y 201, respectivamente. La propiedad <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState>, que también se proporciona con `ControllerBase`, se usa para controlar la validación del modelo de solicitud.

::: moniker range=">= aspnetcore-2.1"

## <a name="annotate-class-with-apicontrollerattribute"></a>Anotación de una clase con ApiControllerAttribute

ASP.NET Core 2.1 incorpora el atributo [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) para designar una clase de controlador de API web. Por ejemplo:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=2)]

Para usar este atributo se requiere una versión de compatibilidad de 2.1 o posterior, establecida mediante <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>. Por ejemplo, el código resaltado en *Startup.ConfigureServices* establece la marca de compatibilidad 2.2:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=2)]

Para obtener más información, vea <xref:mvc/compatibility-version>.

El atributo `[ApiController]` se suele emparejar con `ControllerBase` para habilitar el comportamiento específico de REST para los controladores. `ControllerBase` proporciona acceso a métodos como <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> y <xref:Microsoft.AspNetCore.Mvc.ControllerBase.File*>.

Otro enfoque consiste en crear una clase de controlador base personalizada anotada con el atributo `[ApiController]`:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/MyBaseController.cs?name=snippet_ControllerSignature)]

En las secciones siguientes se describen las ventajas de las características que aporta el atributo.

### <a name="problem-details-responses-for-error-status-codes"></a>Respuestas de los detalles de problemas para los códigos de estado de error

ASP.NET Core 2.1 y las versiones posteriores incluyen [ProblemDetails](xref:Microsoft.AspNetCore.Mvc.ProblemDetails), un tipo basado en la [especificación RFC 7807](https://tools.ietf.org/html/rfc7807). El tipo `ProblemDetails` proporciona un formato estandarizado para tratar los detalles de los errores legibles por una máquina en una respuesta HTTP.

En ASP.NET Core 2.2 y las versiones posteriores, MVC transforma los resultados del código de estado de error (código de estado 400 y superiores) en un resultado con `ProblemDetails`. Observe el código siguiente:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/PetsController.cs?name=snippet_ProblemDetails_StatusCode&highlight=4)]

La respuesta HTTP del resultado `NotFound` tiene un código de estado 404 con un cuerpo `ProblemDetails` similar al siguiente:

```js
{
    type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
    title: "Not Found",
    status: 404,
    traceId: "0HLHLV31KRN83:00000001"
}
```

La característica de los detalles del problema requiere una marca de compatibilidad 2.2 o posterior. El comportamiento predeterminado se deshabilita cuando la propiedad [SuppressMapClientErrors](/dotnet/api/microsoft.aspnetcore.Mvc.ApiBehaviorOptions) <!--  Until these resolve, link to the parent class <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors> --> se establece en `true`. El siguiente código de resaltado `Startup.ConfigureServices` deshabilita los detalles del problema:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=8)]

Use la propiedad [ClientErrorMapping](/dotnet/api/microsoft.aspnetcore.Mvc.ApiBehaviorOptions) <!--  Until these resolve, link to the parent class <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping> --> para configurar el contenido de la respuesta `ProblemDetails`. Por ejemplo, el código siguiente permite actualizar la propiedad `type` para respuestas 404:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_SetCompatibilityVersion&highlight=10)]

### <a name="automatic-http-400-responses"></a>Respuestas HTTP 400 automáticas

Los errores de validación desencadenan automáticamente una respuesta HTTP 400. El código siguiente deja de ser necesario en las acciones:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api.Pre21/Controllers/PetsController.cs?name=snippet_ModelStateIsValidCheck)]

Use <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> para personalizar la salida de la respuesta resultante.

El comportamiento predeterminado se deshabilita al establecer la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> en `true`. Agregue el siguiente código en *Startup.ConfigureServices* después de `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=5)]

Con una marca de compatibilidad de 2.2 o posterior, el tipo de respuesta predeterminada que se devuelve para las respuestas 400 es un elemento <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>. Utilice la propiedad [SuppressUseValidationProblemDetailsForInvalidModelStateResponses](/dotnet/api/microsoft.aspnetcore.Mvc.ApiBehaviorOptions) <!--  <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressUseValidationProblemDetailsForInvalidModelStateResponses> --> para usar el formato de error de ASP.NET Core 2.1.

### <a name="binding-source-parameter-inference"></a>Inferencia de parámetro de origen de enlace

Un atributo de origen de enlace define la ubicación del valor del parámetro de una acción. Existen los atributos de origen de enlace siguientes:

|Atributo|Origen de enlace |
|---------|---------|
|**[[FromBody]](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)**     | Cuerpo de la solicitud |
|**[[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)**     | Datos del formulario en el cuerpo de la solicitud |
|**[[FromHeader]](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)** | Encabezado de la solicitud |
|**[[FromQuery]](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)**   | Parámetro de la cadena de consulta de la solicitud |
|**[[FromRoute]](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)**   | Datos de ruta de la solicitud actual |
|**[[FromServices]](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)** | Servicio de solicitud insertado como parámetro de acción |

> [!WARNING]
> No use `[FromRoute]` si los valores pueden contener `%2f` (es decir, `/`). `%2f` no incluirá el carácter sin escape `/`. Use `[FromQuery]` si el valor puede contener `%2f`.

Sin el `[ApiController]` atributo, los atributos de origen de enlace se definen explícitamente. En el ejemplo siguiente, el atributo `[FromQuery]` indica que el valor del parámetro `discontinuedOnly` se proporciona en la cadena de consulta de la dirección URL de la solicitud:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

Las reglas de inferencia se aplican para los orígenes de datos predeterminados de los parámetros de acción. Estas reglas configuran los orígenes de enlace que aplicaría manualmente a los parámetros de acción. Los atributos de origen de enlace presentan este comportamiento:

* **[FromBody]** se infiere para los parámetros de tipo complejo. La excepción a esta regla es cualquier tipo integrado complejo que tenga un significado especial, como <xref:Microsoft.AspNetCore.Http.IFormCollection> y <xref:System.Threading.CancellationToken>. El código de inferencia del origen de enlace omite esos tipos especiales. En el caso de los tipos simples, como `string` y `int`, `[FromBody]` no se infiere. Así pues, para los tipos simples, en los casos en los que quiera utilizar dicha funcionalidad, conviene usar el atributo `[FromBody]`. Cuando una acción contiene más de un parámetro que se especifica explícitamente (a través de `[FromBody]`) o se infiere como enlazado desde el cuerpo de la solicitud, se produce una excepción. Por ejemplo, las firmas de acción siguientes provocan una excepción:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/TestController.cs?name=snippet_ActionsCausingExceptions)]

* **[FromForm]** se infiere para los parámetros de acción de tipo <xref:Microsoft.AspNetCore.Http.IFormFile> y <xref:Microsoft.AspNetCore.Http.IFormFileCollection>. No se infiere para los tipos simples o definidos por el usuario.
* **[FromRoute]** se infiere para cualquier nombre de parámetro de acción que coincida con un parámetro de la plantilla de ruta. Si varias rutas coinciden con un parámetro de acción, cualquier valor de ruta se considera `[FromRoute]`.
* **[FromQuery]** se infiere para cualquier otro parámetro de acción.

Las reglas de inferencia predeterminadas se deshabilitan al establecer la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> en `true`. Agregue el siguiente código en *Startup.ConfigureServices* después de `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=4)]

### <a name="multipartform-data-request-inference"></a>Inferencia de solicitud de varios elementos o datos de formulario

Si un parámetro de acción se anota con el atributo [[FromForm]](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute), se infiere el tipo de contenido de la solicitud `multipart/form-data`.

El comportamiento predeterminado se deshabilita al establecer la propiedad <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> en `true`. Agregue el siguiente código en *Startup.ConfigureServices* después de `services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);`:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3)]

### <a name="attribute-routing-requirement"></a>Requisito de enrutamiento mediante atributos

El enrutamiento mediante atributos pasa a ser un requisito. Por ejemplo:

[!code-csharp[](../web-api/define-controller/samples/WebApiSample.Api/Controllers/ProductsController.cs?name=snippet_ControllerSignature&highlight=1)]

Las acciones dejan de estar disponibles a través de las [rutas convencionales](xref:mvc/controllers/routing#conventional-routing) definidas en <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> o mediante <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> en *Startup.Configure*.

::: moniker-end

## <a name="additional-resources"></a>Recursos adicionales

* <xref:web-api/action-return-types>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
