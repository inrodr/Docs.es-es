---
title: "Creación de aplicaciones auxiliares de etiquetas en el núcleo de ASP.NET"
author: rick-anderson
description: "Obtenga información acerca de cómo crear aplicaciones auxiliares de etiquetas en ASP.NET Core."
keywords: "Núcleo de ASP.NET, aplicaciones auxiliares de etiquetas"
ms.author: riande
manager: wpickett
ms.date: 6/14/2017
ms.topic: article
ms.assetid: 4f16d978-5695-4abf-a785-fdaabf3bbcb9
ms.technology: aspnet
ms.prod: asp.net-core
uid: mvc/views/tag-helpers/authoring
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f16af1184a29b891a9aab0b38ab833836c326c44
ms.sourcegitcommit: e6a8f171f26fab1b2195a2d7f14e7d258a2e690e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2017
---
# <a name="authoring-tag-helpers-in-aspnet-core-a-walkthrough-with-samples"></a><span data-ttu-id="ecacb-104">Aplicaciones auxiliares de etiquetas en el núcleo de ASP.NET, un tutorial con ejemplos de creación</span><span class="sxs-lookup"><span data-stu-id="ecacb-104">Authoring Tag Helpers in ASP.NET Core, a walkthrough with samples</span></span>

<span data-ttu-id="ecacb-105">Por [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ecacb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[<span data-ttu-id="ecacb-106">Ver o descargar el código de ejemplo</span><span class="sxs-lookup"><span data-stu-id="ecacb-106">View or download sample code</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample)

## <a name="getting-started-with-tag-helpers"></a><span data-ttu-id="ecacb-107">Introducción a las aplicaciones auxiliares de etiquetas</span><span class="sxs-lookup"><span data-stu-id="ecacb-107">Getting started with Tag Helpers</span></span>

<span data-ttu-id="ecacb-108">Este tutorial proporciona una introducción a la programación de aplicaciones auxiliares de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="ecacb-108">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="ecacb-109">[Introducción a las aplicaciones auxiliares de etiquetas](intro.md) describe las ventajas que proporciona aplicaciones auxiliares de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="ecacb-109">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="ecacb-110">Una aplicación auxiliar para etiqueta es cualquier clase que implemente la `ITagHelper` interfaz.</span><span class="sxs-lookup"><span data-stu-id="ecacb-110">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="ecacb-111">Sin embargo, al crear una aplicación auxiliar de la etiqueta, derivan normalmente de `TagHelper`, esto le brinda acceso a la `Process` método.</span><span class="sxs-lookup"><span data-stu-id="ecacb-111">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="ecacb-112">Crear un nuevo proyecto de ASP.NET Core denominado **AuthoringTagHelpers**.</span><span class="sxs-lookup"><span data-stu-id="ecacb-112">Create a new ASP.NET Core project called **AuthoringTagHelpers**.</span></span> <span data-ttu-id="ecacb-113">No necesita autenticación para este proyecto.</span><span class="sxs-lookup"><span data-stu-id="ecacb-113">You won't need authentication for this project.</span></span>

2. <span data-ttu-id="ecacb-114">Cree una carpeta para almacenar las aplicaciones auxiliares de etiqueta denominado *TagHelpers*.</span><span class="sxs-lookup"><span data-stu-id="ecacb-114">Create a folder to hold the Tag Helpers called *TagHelpers*.</span></span> <span data-ttu-id="ecacb-115">El *TagHelpers* carpeta es *no* necesario, pero es una convención razonable.</span><span class="sxs-lookup"><span data-stu-id="ecacb-115">The *TagHelpers* folder is *not* required, but it is a reasonable convention.</span></span> <span data-ttu-id="ecacb-116">Ahora vamos a empezar a escribir algunas aplicaciones auxiliares de etiqueta simple.</span><span class="sxs-lookup"><span data-stu-id="ecacb-116">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="ecacb-117">Una aplicación auxiliar de etiquetas mínima</span><span class="sxs-lookup"><span data-stu-id="ecacb-117">A minimal Tag Helper</span></span>

<span data-ttu-id="ecacb-118">En esta sección, se escribe una aplicación auxiliar de etiquetas que actualiza una etiqueta de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="ecacb-118">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="ecacb-119">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ecacb-119">For example:</span></span>

```html
<email>Support</email>
   ```

<span data-ttu-id="ecacb-120">El servidor usará nuestra herramienta de etiqueta de correo electrónico para convertir ese marcado en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-120">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
   ```

<span data-ttu-id="ecacb-121">Es decir, una etiqueta delimitadora lo hace un vínculo de correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="ecacb-121">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="ecacb-122">Puede hacerlo si está escribiendo un motor de blogs y lo necesite para enviar correo electrónico de marketing, soporte técnico y otros contactos, todo ello en el mismo dominio.</span><span class="sxs-lookup"><span data-stu-id="ecacb-122">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1.  <span data-ttu-id="ecacb-123">Agregue el siguiente `EmailTagHelper` clase a la *TagHelpers* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-123">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

    <span data-ttu-id="ecacb-124">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-124">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]</span></span>
    
    <span data-ttu-id="ecacb-125">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="ecacb-125">**Notes:**</span></span>
    
    * <span data-ttu-id="ecacb-126">Aplicaciones auxiliares de etiquetas usan una convención de nomenclatura que tenga como destino de los elementos de la clase raíz (menos la *TagHelper* parte del nombre de clase).</span><span class="sxs-lookup"><span data-stu-id="ecacb-126">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="ecacb-127">En este ejemplo, el nombre de raíz de **correo electrónico**TagHelper es *correo electrónico*, por lo que el `<email>` se destinará la etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-127">In this example, the root name of **Email**TagHelper is *email*, so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="ecacb-128">Esta convención de nomenclatura debería funcionar para la mayoría de aplicaciones auxiliares de etiquetas, más adelante voy a explicar cómo reemplazarlo.</span><span class="sxs-lookup"><span data-stu-id="ecacb-128">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>
    
    * <span data-ttu-id="ecacb-129">La clase `EmailTagHelper` se deriva de `TagHelper`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-129">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="ecacb-130">La `TagHelper` clase proporciona propiedades y métodos para escribir aplicaciones auxiliares de etiquetas.</span><span class="sxs-lookup"><span data-stu-id="ecacb-130">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>
    
    * <span data-ttu-id="ecacb-131">Reemplazados `Process` método controla lo que hace la aplicación auxiliar de la etiqueta cuando se ejecuta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-131">The  overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="ecacb-132">El `TagHelper` clase también proporciona una versión asincrónica (`ProcessAsync`) con los mismos parámetros.</span><span class="sxs-lookup"><span data-stu-id="ecacb-132">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>
    
    * <span data-ttu-id="ecacb-133">El parámetro de contexto a `Process` (y `ProcessAsync`) contiene información relacionada con la ejecución de la etiqueta HTML actual.</span><span class="sxs-lookup"><span data-stu-id="ecacb-133">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>
    
    * <span data-ttu-id="ecacb-134">El parámetro de salida para `Process` (y `ProcessAsync`) contiene un elemento HTML con estado relacionado con el origen original que se usa para generar una etiqueta HTML y contenido.</span><span class="sxs-lookup"><span data-stu-id="ecacb-134">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>
    
    * <span data-ttu-id="ecacb-135">El nombre de la clase tiene un sufijo de **TagHelper**, que es *no* necesario, pero que considera una convención de práctica recomendada.</span><span class="sxs-lookup"><span data-stu-id="ecacb-135">Our class name has a suffix of **TagHelper**, which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="ecacb-136">Podría declarar la clase como:</span><span class="sxs-lookup"><span data-stu-id="ecacb-136">You could declare the class as:</span></span>
    
    ```csharp
    public class Email : TagHelper
    ```

2.  <span data-ttu-id="ecacb-137">Para realizar la `EmailTagHelper` clase disponible para todas las vistas de nuestro Razor, agregue el `addTagHelper` la directiva a la *Views/_ViewImports.cshtml* archivo: [!code-html [principal](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-137">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]</span></span>
    
    <span data-ttu-id="ecacb-138">El código anterior utiliza la sintaxis de comodín para especificar que todas las aplicaciones auxiliares de etiqueta en el ensamblado estará disponibles.</span><span class="sxs-lookup"><span data-stu-id="ecacb-138">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="ecacb-139">La primera cadena después `@addTagHelper` especifica la aplicación auxiliar de etiquetas para cargar (utilice "*" para todas las aplicaciones auxiliares de etiquetas), y la segunda cadena "AuthoringTagHelpers" especifica el ensamblado de la aplicación auxiliar de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-139">The first string after `@addTagHelper` specifies the tag helper to load (Use "*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="ecacb-140">Además, tenga en cuenta que la segunda línea se pondrá en las aplicaciones auxiliares de la etiqueta principal de ASP.NET MVC usando la sintaxis de comodines (esas aplicaciones auxiliares se tratan en [Introducción a las aplicaciones auxiliares de etiquetas](intro.md).) Es el `@addTagHelper` directiva que hace la aplicación auxiliar de etiquetas esté disponible para la vista de Razor.</span><span class="sxs-lookup"><span data-stu-id="ecacb-140">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="ecacb-141">Como alternativa, puede proporcionar el nombre completo (FQN) de una aplicación auxiliar de etiquetas tal y como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="ecacb-141">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>
    
    <span data-ttu-id="ecacb-142">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-142">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]</span></span>
    
    <span data-ttu-id="ecacb-143">Para agregar una aplicación auxiliar de etiqueta a una vista con un FQN, agregue primero la FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) y, a continuación, el nombre del ensamblado (*AuthoringTagHelpers*).</span><span class="sxs-lookup"><span data-stu-id="ecacb-143">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name (*AuthoringTagHelpers*).</span></span> <span data-ttu-id="ecacb-144">La mayoría de los programadores preferirán utilizar la sintaxis de comodines.</span><span class="sxs-lookup"><span data-stu-id="ecacb-144">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="ecacb-145">[Introducción a las aplicaciones auxiliares de etiquetas](intro.md) entra en detalles acerca de la sintaxis de agregar, quitar, jerarquía y caracteres comodín de la aplicación auxiliar de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-145">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>
    
3.  <span data-ttu-id="ecacb-146">Actualizar el marcado en el *Views/Home/Contact.cshtml* archivo con estos cambios:</span><span class="sxs-lookup"><span data-stu-id="ecacb-146">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

    <span data-ttu-id="ecacb-147">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-147">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]</span></span>

4.  <span data-ttu-id="ecacb-148">Ejecutar la aplicación y usar su explorador favorito para ver el código fuente HTML para que pueda comprobar que las etiquetas de correo electrónico se reemplazan con marcado delimitador (por ejemplo, `<a>Support</a>`).</span><span class="sxs-lookup"><span data-stu-id="ecacb-148">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="ecacb-149">*Compatibilidad con* y *Marketing* se representan como vínculos, pero no tienen un `href` atributo para que sean funcionales.</span><span class="sxs-lookup"><span data-stu-id="ecacb-149">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="ecacb-150">Se corregirá en la siguiente sección.</span><span class="sxs-lookup"><span data-stu-id="ecacb-150">We'll fix that in the next section.</span></span>

<span data-ttu-id="ecacb-151">Nota: Al igual que las etiquetas HTML, etiquetas y atributos, nombres de clases y atributos en Razor y C# no son distingue mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="ecacb-151">Note: Like HTML tags and attributes, tags, class names and attributes in Razor, and C# are not case-sensitive.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="ecacb-152">SetAttribute y SetContent</span><span class="sxs-lookup"><span data-stu-id="ecacb-152">SetAttribute and SetContent</span></span>

<span data-ttu-id="ecacb-153">En esta sección, actualizaremos el `EmailTagHelper` para que se va a crear una etiqueta de delimitador válido para correo electrónico.</span><span class="sxs-lookup"><span data-stu-id="ecacb-153">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="ecacb-154">Le mantendremos para disponer de información de una vista Razor (en forma de un `mail-to` atributo) y que utilizar al generar el delimitador.</span><span class="sxs-lookup"><span data-stu-id="ecacb-154">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="ecacb-155">Actualización de la `EmailTagHelper` clase por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-155">Update the `EmailTagHelper` class with the following:</span></span>

<span data-ttu-id="ecacb-156">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-156">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]</span></span>

<span data-ttu-id="ecacb-157">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="ecacb-157">**Notes:**</span></span>

* <span data-ttu-id="ecacb-158">Nombres de propiedad y clase la grafía Pascal para aplicaciones auxiliares de etiquetas se convierten en sus [reducir caso kebab](http://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101#12273101).</span><span class="sxs-lookup"><span data-stu-id="ecacb-158">Pascal-cased class and property names for tag helpers are translated into their [lower kebab case](http://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101#12273101).</span></span> <span data-ttu-id="ecacb-159">Por lo tanto, para usar el `MailTo` atributo, deberá usar `<email mail-to="value"/>` equivalente.</span><span class="sxs-lookup"><span data-stu-id="ecacb-159">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="ecacb-160">La última línea establece el contenido completado para nuestra herramienta etiqueta mínimamente funcional.</span><span class="sxs-lookup"><span data-stu-id="ecacb-160">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="ecacb-161">La línea resaltada muestra la sintaxis para agregar atributos:</span><span class="sxs-lookup"><span data-stu-id="ecacb-161">The highlighted line shows the syntax for adding attributes:</span></span>

<span data-ttu-id="ecacb-162">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-162">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]</span></span>

<span data-ttu-id="ecacb-163">Este enfoque funciona para el atributo "href" siempre y cuando no existe actualmente en la colección de atributos.</span><span class="sxs-lookup"><span data-stu-id="ecacb-163">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="ecacb-164">También puede usar el `output.Attributes.Add` método para agregar un atributo de aplicación auxiliar de etiquetas al final de la colección de atributos de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-164">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1.  <span data-ttu-id="ecacb-165">Actualizar el marcado en el *Views/Home/Contact.cshtml* archivo con estos cambios: [!code-html [principal](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-165">Update the markup in the *Views/Home/Contact.cshtml* file with these changes: [!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]</span></span>

2.  <span data-ttu-id="ecacb-166">Ejecute la aplicación y compruebe que éste genera los enlaces correctos.</span><span class="sxs-lookup"><span data-stu-id="ecacb-166">Run the app and verify that it generates the correct links.</span></span>
    
    > [!NOTE]
    ><span data-ttu-id="ecacb-167">Si fuera a escribir el correo electrónico etiqueta de autocierre (`<email mail-to="Rick" />`), el resultado final también sería autocierre.</span><span class="sxs-lookup"><span data-stu-id="ecacb-167">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="ecacb-168">Para habilitar la capacidad para escribir la etiqueta con una etiqueta de apertura (`<email mail-to="Rick">`) debe decorar la clase con la siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-168">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must decorate the class with the following:</span></span>
    >
    > <span data-ttu-id="ecacb-169">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-169">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]</span></span>
    
    <span data-ttu-id="ecacb-170">Con una aplicación auxiliar autocierre etiqueta de correo electrónico, el resultado sería `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-170">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="ecacb-171">Las etiquetas de cierre automático no son HTML válido, así, no deseará crear uno, pero podría ser conveniente crear una aplicación auxiliar de etiquetas que es de cierre automático.</span><span class="sxs-lookup"><span data-stu-id="ecacb-171">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that is self-closing.</span></span> <span data-ttu-id="ecacb-172">Aplicaciones auxiliares de etiquetas establecer el tipo de la `TagMode` propiedad después de leer una etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-172">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>
    
### <a name="processasync"></a><span data-ttu-id="ecacb-173">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="ecacb-173">ProcessAsync</span></span>

<span data-ttu-id="ecacb-174">En esta sección, se escribirá una aplicación auxiliar de correo electrónico asincrónica.</span><span class="sxs-lookup"><span data-stu-id="ecacb-174">In this section, we'll write an asynchronous email helper.</span></span>

1.  <span data-ttu-id="ecacb-175">Reemplace el `EmailTagHelper` clase con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-175">Replace the `EmailTagHelper` class with the following code:</span></span>

    <span data-ttu-id="ecacb-176">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-176">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]</span></span>

    <span data-ttu-id="ecacb-177">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="ecacb-177">**Notes:**</span></span>

    * <span data-ttu-id="ecacb-178">Esta versión usa asincrónico `ProcessAsync` método.</span><span class="sxs-lookup"><span data-stu-id="ecacb-178">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="ecacb-179">Asincrónico `GetChildContentAsync` devuelve un `Task` que contiene el `TagHelperContent`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-179">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

    * <span data-ttu-id="ecacb-180">Use la `output` para obtener el contenido del elemento HTML.</span><span class="sxs-lookup"><span data-stu-id="ecacb-180">Use the `output` parameter to get contents of the HTML element.</span></span>

2.  <span data-ttu-id="ecacb-181">Realice el siguiente cambio en el *Views/Home/Contact.cshtml* de archivos para la aplicación auxiliar de la etiqueta puede obtener el correo electrónico de destino.</span><span class="sxs-lookup"><span data-stu-id="ecacb-181">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

    <span data-ttu-id="ecacb-182">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-182">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]</span></span>

3.  <span data-ttu-id="ecacb-183">Ejecute la aplicación y compruebe que éste genera vínculos de correo electrónico válida.</span><span class="sxs-lookup"><span data-stu-id="ecacb-183">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="ecacb-184">RemoveAll, PreContent.SetHtmlContent y PostContent.SetHtmlContent</span><span class="sxs-lookup"><span data-stu-id="ecacb-184">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1.  <span data-ttu-id="ecacb-185">Agregue el siguiente `BoldTagHelper` clase a la *TagHelpers* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-185">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

    <span data-ttu-id="ecacb-186">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-186">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]</span></span>

    <span data-ttu-id="ecacb-187">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="ecacb-187">**Notes:**</span></span>
    
    * <span data-ttu-id="ecacb-188">El `[HtmlTargetElement]` atributo pasadas coincidirá con un parámetro de atributo que especifica que cualquier elemento HTML que contiene un atributo HTML denominado "bold", y el `Process` se ejecutará el método de invalidación de la clase.</span><span class="sxs-lookup"><span data-stu-id="ecacb-188">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="ecacb-189">En nuestro ejemplo, el `Process` método quita el atributo "negrita" y rodea el contenedor marcado con `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-189">In our sample, the `Process`  method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>
    
    * <span data-ttu-id="ecacb-190">Dado que no desea reemplazar el contenido de la etiqueta existente, debe escribir la apertura `<strong>` etiquetar con el `PreContent.SetHtmlContent` método y el cierre `</strong>` etiquetar con los `PostContent.SetHtmlContent` método.</span><span class="sxs-lookup"><span data-stu-id="ecacb-190">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>
    
2.  <span data-ttu-id="ecacb-191">Modificar el *About.cshtml* vista para que contenga un `bold` valor de atributo.</span><span class="sxs-lookup"><span data-stu-id="ecacb-191">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="ecacb-192">El código completo se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="ecacb-192">The completed code is shown below.</span></span>

    <span data-ttu-id="ecacb-193">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-193">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]</span></span>

3.  <span data-ttu-id="ecacb-194">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ecacb-194">Run the app.</span></span> <span data-ttu-id="ecacb-195">Puede utilizar el explorador que prefiera para inspeccionar el origen y compruebe el marcado.</span><span class="sxs-lookup"><span data-stu-id="ecacb-195">You can use your favorite browser to inspect the source and verify the markup.</span></span>

    <span data-ttu-id="ecacb-196">El `[HtmlTargetElement]` atributo anterior solo tiene como destino la marca HTML que proporciona un nombre de atributo de "bold".</span><span class="sxs-lookup"><span data-stu-id="ecacb-196">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="ecacb-197">El `<bold>` elemento no fue modificado por la aplicación auxiliar de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-197">The `<bold>` element was not modified by the tag helper.</span></span>

4. <span data-ttu-id="ecacb-198">Convierta en comentario la `[HtmlTargetElement]` la línea de atributo y serán destinos `<bold>` etiquetas, es decir, el formato HTML del formulario `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-198">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="ecacb-199">Recuerde que la convención de nomenclatura predeterminado coincidirá con el nombre de clase **negrita**TagHelper a `<bold>` etiquetas.</span><span class="sxs-lookup"><span data-stu-id="ecacb-199">Remember, the default naming convention will match the class name **Bold**TagHelper to `<bold>` tags.</span></span>

5. <span data-ttu-id="ecacb-200">Ejecute la aplicación y compruebe que la `<bold>` etiqueta es procesada por la aplicación auxiliar de etiqueta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-200">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="ecacb-201">Al decorar una clase con varios `[HtmlTargetElement]` da como resultado una operación lógica OR de los destinos de atributos.</span><span class="sxs-lookup"><span data-stu-id="ecacb-201">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="ecacb-202">Por ejemplo, con el código siguiente, una etiqueta de negrita o un atributo negrita coincidirá.</span><span class="sxs-lookup"><span data-stu-id="ecacb-202">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

<span data-ttu-id="ecacb-203">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-203">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]</span></span>

<span data-ttu-id="ecacb-204">Cuando se agregan varios atributos a la misma instrucción, el tiempo de ejecución los trata como un AND lógico.</span><span class="sxs-lookup"><span data-stu-id="ecacb-204">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="ecacb-205">Por ejemplo, en el código siguiente, un elemento HTML debe denominarse "bold" con un atributo denominado "bold" (`<bold bold />`) para hacer coincidir.</span><span class="sxs-lookup"><span data-stu-id="ecacb-205">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="ecacb-206">También puede usar el `[HtmlTargetElement]` para cambiar el nombre del elemento de destino.</span><span class="sxs-lookup"><span data-stu-id="ecacb-206">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="ecacb-207">Por ejemplo, si deseara la `BoldTagHelper` destino `<MyBold>` etiquetas, usaría el siguiente atributo:</span><span class="sxs-lookup"><span data-stu-id="ecacb-207">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
   ```

## <a name="passing-a-model-to-a-tag-helper"></a><span data-ttu-id="ecacb-208">Pasar un modelo a una aplicación auxiliar de etiqueta</span><span class="sxs-lookup"><span data-stu-id="ecacb-208">Passing a model to a Tag Helper</span></span>

1.  <span data-ttu-id="ecacb-209">Agregar un *modelos* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-209">Add a *Models* folder.</span></span>

2.  <span data-ttu-id="ecacb-210">Agregue el siguiente `WebsiteContext` clase a la *modelos* carpeta:</span><span class="sxs-lookup"><span data-stu-id="ecacb-210">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

    <span data-ttu-id="ecacb-211">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-211">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]</span></span>

3.  <span data-ttu-id="ecacb-212">Agregue el siguiente `WebsiteInformationTagHelper` clase a la *TagHelpers* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-212">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

    <span data-ttu-id="ecacb-213">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-213">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]</span></span>
    
    <span data-ttu-id="ecacb-214">**Notas:**</span><span class="sxs-lookup"><span data-stu-id="ecacb-214">**Notes:**</span></span>
    
    * <span data-ttu-id="ecacb-215">Como se mencionó anteriormente, aplicaciones auxiliares de etiquetas traduce los nombres de clase de C# la grafía Pascal y propiedades para aplicaciones auxiliares de etiquetas en [reducir caso kebab](http://c2.com/cgi/wiki?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="ecacb-215">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [lower kebab case](http://c2.com/cgi/wiki?KebabCase).</span></span> <span data-ttu-id="ecacb-216">Por lo tanto, para usar el `WebsiteInformationTagHelper` en Razor, se escribirá `<website-information />`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-216">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>
    
    * <span data-ttu-id="ecacb-217">No se está identificando explícitamente el elemento de destino con el `[HtmlTargetElement]` atributo, por lo que el valor predeterminado de `website-information` se destinará.</span><span class="sxs-lookup"><span data-stu-id="ecacb-217">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="ecacb-218">Si ha aplicado el atributo siguiente (tenga en cuenta no es el caso de kebab pero coincide con el nombre de clase):</span><span class="sxs-lookup"><span data-stu-id="ecacb-218">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>
    
    ```csharp
    [HtmlTargetElement("WebsiteInformation")]
    ```
    
    <span data-ttu-id="ecacb-219">La etiqueta de caso kebab inferior `<website-information />` no coincidirían.</span><span class="sxs-lookup"><span data-stu-id="ecacb-219">The lower kebab case tag `<website-information />` would not match.</span></span> <span data-ttu-id="ecacb-220">Si desea usar el `[HtmlTargetElement]` atributo, usaría el caso de kebab tal y como se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="ecacb-220">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>
    
    ```csharp
    [HtmlTargetElement("Website-Information")]
    ```
    
    * <span data-ttu-id="ecacb-221">Elementos que se cierre automático no tienen ningún contenido.</span><span class="sxs-lookup"><span data-stu-id="ecacb-221">Elements that are self-closing have no content.</span></span> <span data-ttu-id="ecacb-222">En este ejemplo, el marcado de Razor usará una etiqueta de cierre automático, pero se creará la aplicación auxiliar de etiqueta un [sección](http://www.w3.org/TR/html5/sections.html#the-section-element) elemento (que no se cierre automático y se escribe el contenido dentro de la `section` elemento).</span><span class="sxs-lookup"><span data-stu-id="ecacb-222">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](http://www.w3.org/TR/html5/sections.html#the-section-element) element (which is not self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="ecacb-223">Por lo tanto, debe establecer `TagMode` a `StartTagAndEndTag` para escribir la salida.</span><span class="sxs-lookup"><span data-stu-id="ecacb-223">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="ecacb-224">Como alternativa, puede convertir en comentario la línea donde se establece `TagMode` y escribir marcado con una etiqueta de cierre.</span><span class="sxs-lookup"><span data-stu-id="ecacb-224">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="ecacb-225">(Marcado de ejemplo se proporciona más adelante en este tutorial.)</span><span class="sxs-lookup"><span data-stu-id="ecacb-225">(Example markup is provided later in this tutorial.)</span></span>
    
    * <span data-ttu-id="ecacb-226">El `$` (signo de dólar) en la siguiente línea usa un [interpolan cadena](https://msdn.microsoft.com/library/Dn961160.aspx):</span><span class="sxs-lookup"><span data-stu-id="ecacb-226">The `$` (dollar sign) in the following line uses an [interpolated string](https://msdn.microsoft.com/library/Dn961160.aspx):</span></span>
    
    ```cshtml
    $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
    ```

4.  <span data-ttu-id="ecacb-227">Agregue el siguiente marcado para la *About.cshtml* vista.</span><span class="sxs-lookup"><span data-stu-id="ecacb-227">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="ecacb-228">El marcado resaltado muestra la información del sitio web.</span><span class="sxs-lookup"><span data-stu-id="ecacb-228">The highlighted markup displays the web site information.</span></span>
    
    <span data-ttu-id="ecacb-229">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-229">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,12-)]</span></span>
    
    >[!NOTE]
    > <span data-ttu-id="ecacb-230">En el marcado de Razor que se muestra a continuación:</span><span class="sxs-lookup"><span data-stu-id="ecacb-230">In the Razor markup shown below:</span></span>
    >
    ><span data-ttu-id="ecacb-231">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-231">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=13-17)]</span></span>
    > 
    ><span data-ttu-id="ecacb-232">Razor sepa el `info` atributo es una clase, no una cadena, y desea escribir código C#.</span><span class="sxs-lookup"><span data-stu-id="ecacb-232">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="ecacb-233">Cualquier atributo de aplicación auxiliar de etiqueta no es una cadena debe escribirse sin el `@` caracteres.</span><span class="sxs-lookup"><span data-stu-id="ecacb-233">Any non-string tag helper attribute should be written without the `@` character.</span></span>
    
5.  <span data-ttu-id="ecacb-234">Ejecutar la aplicación y navegue hasta la vista acerca de para ver la información del sitio web.</span><span class="sxs-lookup"><span data-stu-id="ecacb-234">Run the app, and navigate to the About view to see the web site information.</span></span>

    >[!NOTE]
    ><span data-ttu-id="ecacb-235">Puede usar el siguiente marcado con una etiqueta de cierre y quite la línea con `TagMode.StartTagAndEndTag` en la aplicación auxiliar de etiqueta:</span><span class="sxs-lookup"><span data-stu-id="ecacb-235">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
    >
    ><span data-ttu-id="ecacb-236">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-236">[!code-html[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=13-18)]</span></span>

## <a name="condition-tag-helper"></a><span data-ttu-id="ecacb-237">Aplicación auxiliar de etiquetas de condición</span><span class="sxs-lookup"><span data-stu-id="ecacb-237">Condition Tag Helper</span></span>

<span data-ttu-id="ecacb-238">La aplicación auxiliar de etiquetas de condición presenta la salida cuando se pasa un valor true.</span><span class="sxs-lookup"><span data-stu-id="ecacb-238">The condition tag helper renders output when passed a true value.</span></span>

1.  <span data-ttu-id="ecacb-239">Agregue el siguiente `ConditionTagHelper` clase a la *TagHelpers* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-239">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

    <span data-ttu-id="ecacb-240">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-240">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]</span></span>

2.  <span data-ttu-id="ecacb-241">Reemplace el contenido de la *Views/Home/Index.cshtml* archivo con el siguiente marcado:</span><span class="sxs-lookup"><span data-stu-id="ecacb-241">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

    <!-- literal_block {"xml:space": "preserve", "source": "mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml", "ids": [], "linenos": false, "highlight_args": {"linenostart": 1}} -->
    
    ```cshtml
    @using AuthoringTagHelpers.Models
    @model WebsiteContext
    
    @{
        ViewData["Title"] = "Home Page";
    }
    
    <div>
        <h3>Information about our website (outdated):</h3>
        <website-information info=Model />
        <div condition="Model.Approved">
            <p>
                This website has <strong surround="em"> @Model.Approved </strong> been approved yet.
                Visit www.contoso.com for more information.
            </p>
        </div>
    </div>
    ```
    
3.  <span data-ttu-id="ecacb-242">Reemplace el `Index` método en el `Home` controlador con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-242">Replace the `Index` method in the `Home` controller with the following code:</span></span>

    <span data-ttu-id="ecacb-243">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-243">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]</span></span>

4.  <span data-ttu-id="ecacb-244">Ejecutar la aplicación y vaya a la página principal.</span><span class="sxs-lookup"><span data-stu-id="ecacb-244">Run the app and browse to the home page.</span></span> <span data-ttu-id="ecacb-245">El marcado en el atributo conditional `div` no se representará.</span><span class="sxs-lookup"><span data-stu-id="ecacb-245">The markup in the conditional `div` will not be rendered.</span></span> <span data-ttu-id="ecacb-246">Anexar la cadena de consulta `?approved=true` a la dirección URL (por ejemplo, `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="ecacb-246">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="ecacb-247">`approved`se establece en true y el atributo conditional aparecerá marcado.</span><span class="sxs-lookup"><span data-stu-id="ecacb-247">`approved` is set to true and the conditional markup will be displayed.</span></span>

>[!NOTE]
><span data-ttu-id="ecacb-248">Use la [nameof](https://msdn.microsoft.com/library/dn986596.aspx) operador para especificar el atributo de destino en lugar de especificar una cadena como lo hizo con la aplicación auxiliar de etiqueta negrita:</span><span class="sxs-lookup"><span data-stu-id="ecacb-248">Use the [nameof](https://msdn.microsoft.com/library/dn986596.aspx) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
><span data-ttu-id="ecacb-249">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-249">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]</span></span>
>
><span data-ttu-id="ecacb-250">El [nameof](https://msdn.microsoft.com/library/dn986596.aspx) operador protegerá el código nunca se debería refactorizar (que queramos cambie el nombre a `RedCondition`).</span><span class="sxs-lookup"><span data-stu-id="ecacb-250">The [nameof](https://msdn.microsoft.com/library/dn986596.aspx) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoiding-tag-helper-conflicts"></a><span data-ttu-id="ecacb-251">Evitar conflictos de aplicación auxiliar de etiqueta</span><span class="sxs-lookup"><span data-stu-id="ecacb-251">Avoiding Tag Helper conflicts</span></span>

<span data-ttu-id="ecacb-252">En esta sección, se escribe un par de aplicaciones auxiliares de etiquetas de la vinculación automática.</span><span class="sxs-lookup"><span data-stu-id="ecacb-252">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="ecacb-253">La primera reemplazará el marcado que contiene una dirección URL a partir de HTTP a un HTML delimitador etiqueta que contiene la misma dirección URL (y, por tanto, lo que produce un vínculo a la dirección URL).</span><span class="sxs-lookup"><span data-stu-id="ecacb-253">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="ecacb-254">El segundo se haga lo mismo para una dirección URL a partir de World Wide Web.</span><span class="sxs-lookup"><span data-stu-id="ecacb-254">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="ecacb-255">Dado que estas aplicaciones auxiliares de dos están estrechamente relacionados y puede refactorizar en el futuro, se podrá mantener en el mismo archivo.</span><span class="sxs-lookup"><span data-stu-id="ecacb-255">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1.  <span data-ttu-id="ecacb-256">Agregue el siguiente `AutoLinkerHttpTagHelper` clase a la *TagHelpers* carpeta.</span><span class="sxs-lookup"><span data-stu-id="ecacb-256">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

    <span data-ttu-id="ecacb-257">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-257">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]</span></span>

    >[!NOTE]
    ><span data-ttu-id="ecacb-258">El `AutoLinkerHttpTagHelper` clase destinos `p` elementos y se utiliza [Regex](https://msdn.microsoft.com/library/system.text.regularexpressions.regex.aspx) para crear el delimitador.</span><span class="sxs-lookup"><span data-stu-id="ecacb-258">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](https://msdn.microsoft.com/library/system.text.regularexpressions.regex.aspx) to create the anchor.</span></span>

2.  <span data-ttu-id="ecacb-259">Agregue el siguiente marcado al final de la *Views/Home/Contact.cshtml* archivo:</span><span class="sxs-lookup"><span data-stu-id="ecacb-259">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

    <span data-ttu-id="ecacb-260">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-260">[!code-html[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]</span></span>

3.  <span data-ttu-id="ecacb-261">Ejecutar la aplicación y compruebe que la aplicación auxiliar de la etiqueta representa el delimitador correctamente.</span><span class="sxs-lookup"><span data-stu-id="ecacb-261">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

4.  <span data-ttu-id="ecacb-262">Actualización de la `AutoLinker` clase para que incluya la `AutoLinkerWwwTagHelper` que convertirá el texto de World Wide Web en una etiqueta delimitadora que también contiene el texto original de World Wide Web.</span><span class="sxs-lookup"><span data-stu-id="ecacb-262">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="ecacb-263">El código actualizado se resalta a continuación:</span><span class="sxs-lookup"><span data-stu-id="ecacb-263">The updated code is highlighted below:</span></span>

    <span data-ttu-id="ecacb-264">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-264">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]</span></span>

5.  <span data-ttu-id="ecacb-265">Ejecutar la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ecacb-265">Run the app.</span></span> <span data-ttu-id="ecacb-266">Observe el texto de World Wide Web se representa como un vínculo, pero el texto HTTP no está.</span><span class="sxs-lookup"><span data-stu-id="ecacb-266">Notice the www text is rendered as a link but the HTTP text is not.</span></span> <span data-ttu-id="ecacb-267">Si coloca un punto de interrupción en ambas clases, puede ver que la clase de aplicación auxiliar de etiqueta HTTP se ejecuta primera.</span><span class="sxs-lookup"><span data-stu-id="ecacb-267">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="ecacb-268">El problema es que se almacena en caché el resultado de la aplicación auxiliar de etiqueta y, cuando se ejecuta la aplicación auxiliar de etiqueta de World Wide Web, sobrescribe la salida almacenada en caché de la aplicación auxiliar de etiqueta HTTP.</span><span class="sxs-lookup"><span data-stu-id="ecacb-268">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="ecacb-269">Más adelante en el tutorial veremos cómo controlar el orden en que se ejecutan aplicaciones auxiliares de etiquetas en.</span><span class="sxs-lookup"><span data-stu-id="ecacb-269">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="ecacb-270">Se corregirá el código con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-270">We'll fix the code with the following:</span></span>

    <span data-ttu-id="ecacb-271">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-271">[!code-csharp[Main](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]</span></span>

    >[!NOTE]
    ><span data-ttu-id="ecacb-272">En la primera edición de las aplicaciones auxiliares la vinculación automática de etiqueta, que obtuvo el contenido del destino con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-272">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
    >
    ><span data-ttu-id="ecacb-273">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-273">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]</span></span>
    >
    ><span data-ttu-id="ecacb-274">Es decir, se llama a `GetChildContentAsync` mediante la `TagHelperOutput` pasará el `ProcessAsync` método.</span><span class="sxs-lookup"><span data-stu-id="ecacb-274">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="ecacb-275">Como se mencionó anteriormente, porque el resultado se almacena en caché, la última etiqueta auxiliares para ejecutar wins.</span><span class="sxs-lookup"><span data-stu-id="ecacb-275">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="ecacb-276">Había corregido el error con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ecacb-276">You fixed that problem with the following code:</span></span>
    >
    ><span data-ttu-id="ecacb-277">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-277">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]</span></span>
    >
    ><span data-ttu-id="ecacb-278">El código anterior comprueba si se ha modificado el contenido y, si existe, obtiene el contenido del búfer de salida.</span><span class="sxs-lookup"><span data-stu-id="ecacb-278">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

6.  <span data-ttu-id="ecacb-279">Ejecute la aplicación y compruebe que los dos vínculos funcionan según lo esperado.</span><span class="sxs-lookup"><span data-stu-id="ecacb-279">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="ecacb-280">Aunque podría parecer que nuestra herramienta de etiqueta del vinculador automática es correcta y completa, tiene un pequeño problema.</span><span class="sxs-lookup"><span data-stu-id="ecacb-280">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="ecacb-281">Si la aplicación auxiliar de etiqueta de World Wide Web se ejecuta primera, los vínculos de World Wide Web no será correctos.</span><span class="sxs-lookup"><span data-stu-id="ecacb-281">If the WWW tag helper runs first, the www links will not be correct.</span></span> <span data-ttu-id="ecacb-282">Actualice el código y agregue el `Order` sobrecarga para controlar el orden en que la etiqueta se ejecuta en.</span><span class="sxs-lookup"><span data-stu-id="ecacb-282">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="ecacb-283">El `Order` propiedad determina el orden de ejecución en relación con otras aplicaciones auxiliares de etiquetas como destino el mismo elemento.</span><span class="sxs-lookup"><span data-stu-id="ecacb-283">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="ecacb-284">El valor de orden predeterminado es cero y se ejecutan en primer lugar instancias con valores más bajos.</span><span class="sxs-lookup"><span data-stu-id="ecacb-284">The default order value is zero and instances with lower values are executed first.</span></span>

    <span data-ttu-id="ecacb-285">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-285">[!code-csharp[Main](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]</span></span>
    
    <span data-ttu-id="ecacb-286">El código anterior se garantizará que la aplicación auxiliar de etiqueta HTTP se ejecuta antes de la aplicación auxiliar de etiqueta de World Wide Web.</span><span class="sxs-lookup"><span data-stu-id="ecacb-286">The above code will guarantee that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="ecacb-287">Cambio `Order` a `MaxValue` y compruebe que el marcado generado para la etiqueta de World Wide Web es incorrecto.</span><span class="sxs-lookup"><span data-stu-id="ecacb-287">Change `Order` to `MaxValue` and verify that the markup generated for the  WWW tag is incorrect.</span></span>

## <a name="inspecting-and-retrieving-child-content"></a><span data-ttu-id="ecacb-288">Inspeccionar y recuperar el contenido secundario</span><span class="sxs-lookup"><span data-stu-id="ecacb-288">Inspecting and retrieving child content</span></span>

<span data-ttu-id="ecacb-289">Las aplicaciones auxiliares de etiquetas proporcionan varias propiedades para recuperar el contenido.</span><span class="sxs-lookup"><span data-stu-id="ecacb-289">The tag helpers provide several properties to retrieve content.</span></span>

-  <span data-ttu-id="ecacb-290">El resultado de `GetChildContentAsync` se pueden anexar a `output.Content`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-290">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
-  <span data-ttu-id="ecacb-291">Puede inspeccionar el resultado de `GetChildContentAsync` con `GetContent`.</span><span class="sxs-lookup"><span data-stu-id="ecacb-291">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
-  <span data-ttu-id="ecacb-292">Si modifica `output.Content`, el cuerpo de TagHelper no se puede ejecutar o se representa a menos que llame a `GetChildContentAsync` igual que en nuestro ejemplo de vinculador automática:</span><span class="sxs-lookup"><span data-stu-id="ecacb-292">If you modify `output.Content`, the TagHelper body will not be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

<span data-ttu-id="ecacb-293">[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]</span><span class="sxs-lookup"><span data-stu-id="ecacb-293">[!code-csharp[Main](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]</span></span>

-  <span data-ttu-id="ecacb-294">Varias llamadas a `GetChildContentAsync` devolverá el mismo valor y no se ejecute de nuevo el `TagHelper` cuerpo a menos que se sitúe en un parámetro false que indica no usar el resultado almacenado en caché.</span><span class="sxs-lookup"><span data-stu-id="ecacb-294">Multiple calls to `GetChildContentAsync` will return the same value and will not re-execute the `TagHelper` body unless you pass in a false parameter indicating  not use the cached result.</span></span>