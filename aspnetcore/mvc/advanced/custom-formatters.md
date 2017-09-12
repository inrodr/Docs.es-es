---
title: Formateadores personalizados en las API web de MVC de ASP.NET Core
author: tdykstra
description: "Obtenga información acerca de cómo crear y utilizar a formateadores personalizados para las API web de ASP.NET Core."
keywords: "Núcleo de ASP.NET, web api, formateadores personalizados"
ms.author: tdykstra
manager: wpickett
ms.date: 02/08/2017
ms.topic: article
ms.assetid: 1fb6fdc2-e199-4469-9012-b909d1913422
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/models/custom-formatters
ms.openlocfilehash: 0285b40cfacb79745d3a6488401677130f55a95b
ms.sourcegitcommit: 6ece943781d8a56784bb6160f14da85210d3fcea
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2017
---
# <a name="custom-formatters-in-aspnet-core-mvc-web-apis"></a><span data-ttu-id="50257-104">Formateadores personalizados en las API web de MVC de ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50257-104">Custom formatters in ASP.NET Core MVC web APIs</span></span>

<span data-ttu-id="50257-105">Por [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="50257-105">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="50257-106">Núcleo de ASP.NET MVC tiene compatibilidad integrada para el intercambio de datos en las API web mediante el uso de formatos de texto sin formato, JSON o XML.</span><span class="sxs-lookup"><span data-stu-id="50257-106">ASP.NET Core MVC has built-in support for data exchange in web APIs by using JSON, XML, or plain text formats.</span></span> <span data-ttu-id="50257-107">Este artículo muestra cómo agregar compatibilidad con formatos adicionales mediante la creación de formateadores personalizados.</span><span class="sxs-lookup"><span data-stu-id="50257-107">This article shows how to add support for additional formats by creating custom formatters.</span></span>

<span data-ttu-id="50257-108">[Ver o descargar el ejemplo desde GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span><span class="sxs-lookup"><span data-stu-id="50257-108">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample).</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="50257-109">Cuándo utilizar a formateadores personalizados</span><span class="sxs-lookup"><span data-stu-id="50257-109">When to use custom formatters</span></span>

<span data-ttu-id="50257-110">Utilizar un formateador personalizado cuando desee que el [negociación de contenido](xref:mvc/models/formatting) proceso para admitir un tipo de contenido que no es compatible con los formateadores integrados (JSON, XML y texto sin formato).</span><span class="sxs-lookup"><span data-stu-id="50257-110">Use a custom formatter when you want the [content negotiation](xref:mvc/models/formatting) process to support a content type that isn't supported by the built-in formatters (JSON, XML, and plain text).</span></span>

<span data-ttu-id="50257-111">Por ejemplo, si algunos de los clientes para la API web pueden controlar la [Protobuf](https://github.com/google/protobuf) formato, puede usar Protobuf con dichos clientes porque es más eficaz.</span><span class="sxs-lookup"><span data-stu-id="50257-111">For example, if some of the clients for your web API can handle the [Protobuf](https://github.com/google/protobuf) format, you might want to use Protobuf with those clients because it's more efficient.</span></span>  <span data-ttu-id="50257-112">También puede enviar contacto nombres y direcciones en la API de web [vCard](https://en.wikipedia.org/wiki/VCard) formato, un formato comúnmente utilizado para intercambiar datos de contacto.</span><span class="sxs-lookup"><span data-stu-id="50257-112">Or you might want your web API to send contact names and addresses in [vCard](https://en.wikipedia.org/wiki/VCard) format, a commonly used format for exchanging contact data.</span></span> <span data-ttu-id="50257-113">La aplicación de ejemplo proporcionada con este artículo implementa a un formateador de vCard simple.</span><span class="sxs-lookup"><span data-stu-id="50257-113">The sample app provided with this article implements a simple vCard formatter.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="50257-114">Información general sobre cómo utilizar a un formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="50257-114">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="50257-115">Estos son los pasos para crear y utilizar a un formateador personalizado:</span><span class="sxs-lookup"><span data-stu-id="50257-115">Here are the steps to create and use a custom formatter:</span></span>

* <span data-ttu-id="50257-116">Crear una clase de formateador de salida si desea serializar los datos que se va a enviar al cliente.</span><span class="sxs-lookup"><span data-stu-id="50257-116">Create an output formatter class if you want to serialize data to send to the client.</span></span>
* <span data-ttu-id="50257-117">Crear una clase de formateador de entrada si desea deserializar los datos recibidos del cliente.</span><span class="sxs-lookup"><span data-stu-id="50257-117">Create an input formatter class if you want to deserialize data received from the client.</span></span> 
* <span data-ttu-id="50257-118">Agregar instancias de los formateadores para la `InputFormatters` y `OutputFormatters` colecciones en [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span><span class="sxs-lookup"><span data-stu-id="50257-118">Add instances of your formatters to the `InputFormatters` and `OutputFormatters` collections in [MvcOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.mvcoptions).</span></span>

<span data-ttu-id="50257-119">Las secciones siguientes proporcionan instrucciones y ejemplos de código para cada uno de estos pasos.</span><span class="sxs-lookup"><span data-stu-id="50257-119">The following sections provide guidance and code examples for each of these steps.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="50257-120">Cómo crear una clase de formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="50257-120">How to create a custom formatter class</span></span>

<span data-ttu-id="50257-121">Para crear a un formateador de:</span><span class="sxs-lookup"><span data-stu-id="50257-121">To create a formatter:</span></span>

* <span data-ttu-id="50257-122">Derivar la clase de la clase base correspondiente.</span><span class="sxs-lookup"><span data-stu-id="50257-122">Derive the class from the appropriate base class.</span></span>
* <span data-ttu-id="50257-123">Especificar tipos de medios válido y codificaciones en el constructor.</span><span class="sxs-lookup"><span data-stu-id="50257-123">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="50257-124">Invalidar `CanReadType` / `CanWriteType` métodos</span><span class="sxs-lookup"><span data-stu-id="50257-124">Override `CanReadType`/`CanWriteType` methods</span></span>
* <span data-ttu-id="50257-125">Invalidar `ReadRequestBodyAsync` / `WriteResponseBodyAsync` métodos</span><span class="sxs-lookup"><span data-stu-id="50257-125">Override `ReadRequestBodyAsync`/`WriteResponseBodyAsync` methods</span></span>
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="50257-126">Derivar de la clase base adecuada</span><span class="sxs-lookup"><span data-stu-id="50257-126">Derive from the appropriate base class</span></span>

<span data-ttu-id="50257-127">Para los tipos de medios de texto (por ejemplo, vCard), que se derivan de la [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) o [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) clase base.</span><span class="sxs-lookup"><span data-stu-id="50257-127">For text media types (for example, vCard), derive from the [TextInputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textinputformatter) or [TextOutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.textoutputformatter) base class.</span></span>

<span data-ttu-id="50257-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span><span class="sxs-lookup"><span data-stu-id="50257-128">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=classdef)]</span></span>

<span data-ttu-id="50257-129">Para los tipos binarios, que se derivan de la [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) o [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) clase base.</span><span class="sxs-lookup"><span data-stu-id="50257-129">For binary types, derive from the [InputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.inputformatter) or [OutputFormatter](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformatter) base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="50257-130">Especificar las codificaciones y tipos de medios válido</span><span class="sxs-lookup"><span data-stu-id="50257-130">Specify valid media types and encodings</span></span>

<span data-ttu-id="50257-131">En el constructor, especificar tipos de medios válido y codificaciones, agregando a la `SupportedMediaTypes` y `SupportedEncodings` colecciones.</span><span class="sxs-lookup"><span data-stu-id="50257-131">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

<span data-ttu-id="50257-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span><span class="sxs-lookup"><span data-stu-id="50257-132">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=ctor&highlight=3,5-6)]</span></span>

> [!NOTE]  
> <span data-ttu-id="50257-133">No puede realizar la inserción de dependencias de constructor en una clase de formateador.</span><span class="sxs-lookup"><span data-stu-id="50257-133">You can't do constructor dependency injection in a formatter class.</span></span> <span data-ttu-id="50257-134">Por ejemplo, no se puede obtener un registrador mediante la adición de un parámetro de registrador al constructor.</span><span class="sxs-lookup"><span data-stu-id="50257-134">For example, you can't get a logger by adding a logger parameter to the constructor.</span></span> <span data-ttu-id="50257-135">Para obtener acceso a servicios, tendrá que usar el objeto de contexto que se pasa a los métodos.</span><span class="sxs-lookup"><span data-stu-id="50257-135">To access services, you have to use the context object that gets passed in to your methods.</span></span> <span data-ttu-id="50257-136">Un ejemplo de código [a continuación](#read-write) muestra cómo hacerlo.</span><span class="sxs-lookup"><span data-stu-id="50257-136">A code example [below](#read-write) shows how to do this.</span></span>

### <a name="override-canreadtypecanwritetype"></a><span data-ttu-id="50257-137">Invalidar CanReadType/CanWriteType</span><span class="sxs-lookup"><span data-stu-id="50257-137">Override CanReadType/CanWriteType</span></span> 

<span data-ttu-id="50257-138">Especificar el tipo se puede deserializar en o serializar desde invalidando el `CanReadType` o `CanWriteType` métodos.</span><span class="sxs-lookup"><span data-stu-id="50257-138">Specify the type you can deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="50257-139">Por ejemplo, solo puede crear texto de vCard desde un `Contact` tipo, y viceversa.</span><span class="sxs-lookup"><span data-stu-id="50257-139">For example, you might only be able to create vCard text from a `Contact` type and vice versa.</span></span>

<span data-ttu-id="50257-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span><span class="sxs-lookup"><span data-stu-id="50257-140">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=canwritetype)]</span></span>

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="50257-141">El método CanWriteResult</span><span class="sxs-lookup"><span data-stu-id="50257-141">The CanWriteResult method</span></span>

<span data-ttu-id="50257-142">En algunos casos tendrá que invalidar `CanWriteResult` en lugar de `CanWriteType`.</span><span class="sxs-lookup"><span data-stu-id="50257-142">In some scenarios you have to override `CanWriteResult` instead of `CanWriteType`.</span></span> <span data-ttu-id="50257-143">Use `CanWriteResult` si se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="50257-143">Use `CanWriteResult` if the following conditions are true:</span></span>

  * <span data-ttu-id="50257-144">El método de acción devuelve una clase de modelo.</span><span class="sxs-lookup"><span data-stu-id="50257-144">Your action method returns a model class.</span></span>
  * <span data-ttu-id="50257-145">Hay clases derivadas que pueden obtenerse en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="50257-145">There are derived classes which might be returned at runtime.</span></span>
  * <span data-ttu-id="50257-146">Debe conocer en tiempo de ejecución que deriva la clase devuelto por la acción.</span><span class="sxs-lookup"><span data-stu-id="50257-146">You need to know at runtime which derived class was returned by the action.</span></span>  

<span data-ttu-id="50257-147">Por ejemplo, suponga que la firma del método de acción devuelve un `Person` tipo, pero se puede devolver un `Student` o `Instructor` tipo que deriva de `Person`.</span><span class="sxs-lookup"><span data-stu-id="50257-147">For example, suppose your action method signature returns a `Person` type, but it may return a `Student` or `Instructor` type that derives from `Person`.</span></span> <span data-ttu-id="50257-148">Si desea que el formateador para controlar sólo `Student` objetos, comprobar el tipo de [objeto](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) en el objeto de contexto proporcionado para el `CanWriteResult` método.</span><span class="sxs-lookup"><span data-stu-id="50257-148">If you want your formatter to handle only `Student` objects, check the type of [Object](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.mvc.formatters.outputformattercanwritecontext#Microsoft_AspNetCore_Mvc_Formatters_OutputFormatterCanWriteContext_Object) in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="50257-149">Tenga en cuenta que no es necesario utilizar `CanWriteResult` cuando se devuelve el método de acción `IActionResult`; en ese caso, el `CanWriteType` método recibe el tipo en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="50257-149">Note that it's not necessary to use `CanWriteResult` when the action method returns `IActionResult`; in that case, the `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>
### <a name="override-readrequestbodyasyncwriteresponsebodyasync"></a><span data-ttu-id="50257-150">Invalidar ReadRequestBodyAsync/WriteResponseBodyAsync</span><span class="sxs-lookup"><span data-stu-id="50257-150">Override ReadRequestBodyAsync/WriteResponseBodyAsync</span></span> 

<span data-ttu-id="50257-151">Hace que el trabajo real de deserializar o serializar en `ReadRequestBodyAsync` o `WriteResponseBodyAsync`.</span><span class="sxs-lookup"><span data-stu-id="50257-151">You do the actual work of deserializing or serializing in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span>  <span data-ttu-id="50257-152">Las líneas resaltadas en el ejemplo siguiente muestran cómo obtener los servicios desde el contenedor de inyección de dependencia (no se puede obtener a partir de los parámetros del constructor).</span><span class="sxs-lookup"><span data-stu-id="50257-152">The highlighted lines in the following example show how to get services from the dependency injection container (you can't get them from constructor parameters).</span></span>

<span data-ttu-id="50257-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="50257-153">[!code-csharp[Main](custom-formatters/sample/Formatters/VcardOutputFormatter.cs?name=writeresponse&highlight=3-4)]</span></span>

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="50257-154">Cómo configurar MVC para utilizar a un formateador personalizado</span><span class="sxs-lookup"><span data-stu-id="50257-154">How to configure MVC to use a custom formatter</span></span>
 
<span data-ttu-id="50257-155">Para utilizar un formateador personalizado, debe agregar una instancia de la clase de formateador para la `InputFormatters` o `OutputFormatters` colección.</span><span class="sxs-lookup"><span data-stu-id="50257-155">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

<span data-ttu-id="50257-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span><span class="sxs-lookup"><span data-stu-id="50257-156">[!code-csharp[Main](custom-formatters/sample/Startup.cs?name=mvcoptions&highlight=3-4)]</span></span>

<span data-ttu-id="50257-157">Formateadores se evalúan en el orden en que se insertaron.</span><span class="sxs-lookup"><span data-stu-id="50257-157">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="50257-158">La primera de ellas tiene prioridad.</span><span class="sxs-lookup"><span data-stu-id="50257-158">The first one takes precedence.</span></span> 

## <a name="next-steps"></a><span data-ttu-id="50257-159">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="50257-159">Next steps</span></span>

<span data-ttu-id="50257-160">Consulte la [aplicación de ejemplo](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), que implementa vCard simple entrada y salida formateadores.</span><span class="sxs-lookup"><span data-stu-id="50257-160">See the [sample application](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/advanced/custom-formatters/sample), which implements simple vCard input and output formatters.</span></span>  <span data-ttu-id="50257-161">La aplicación lee y escribe vCards que tengan un aspecto similar al ejemplo siguiente:</span><span class="sxs-lookup"><span data-stu-id="50257-161">The application reads and writes vCards that look like the following example:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
UID:20293482-9240-4d68-b475-325df4a83728
END:VCARD
```

<span data-ttu-id="50257-162">Para ver vCard de salida, ejecute la aplicación y enviar una solicitud Get con Accept encabezado "texto/vcard" a `http://localhost:63313/api/contacts/` (cuando se ejecuta desde Visual Studio) o `http://localhost:5000/api/contacts/` (cuando se ejecuta desde la línea de comandos).</span><span class="sxs-lookup"><span data-stu-id="50257-162">To see vCard output, run the application and send a Get request with Accept header "text/vcard" to `http://localhost:63313/api/contacts/` (when running from Visual Studio) or `http://localhost:5000/api/contacts/` (when running from the command line).</span></span>

<span data-ttu-id="50257-163">Para agregar una tarjeta vCard a la colección en memoria de contactos, enviar una solicitud Post a la misma dirección URL, con el encabezado Content-Type "texto/vcard" y con texto de vCard en el cuerpo, con formato similar al ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="50257-163">To add a vCard to the in-memory collection of contacts, send a Post request to the same URL, with Content-Type header "text/vcard" and with vCard text in the body, formatted like the example above.</span></span>