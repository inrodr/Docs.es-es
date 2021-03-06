---
uid: web-forms/overview/ajax-control-toolkit/rating/creating-a-rating-control-vb
title: Creación de un Control de clasificación (VB) | Microsoft Docs
author: wenz
description: Muchos sitios Web, de comercio electrónico para sitios de la Comunidad, ofrecen a los usuarios para los artículos de la velocidad o elementos. Esto normalmente requiere algún esfuerzo de codificación, pero tenemos el...
ms.author: riande
ms.date: 06/02/2008
ms.assetid: 6d0d70f4-725e-4258-8ae8-24a6ba1ddbf7
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/rating/creating-a-rating-control-vb
msc.type: authoredcontent
ms.openlocfilehash: b229d0155fbab0437c644b41424164e4c655f5e5
ms.sourcegitcommit: 45ac74e400f9f2b7dbded66297730f6f14a4eb25
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2018
ms.locfileid: "41838786"
---
<a name="creating-a-rating-control-vb"></a>Creación de un Control de clasificación (VB)
====================
por [Christian Wenz](https://github.com/wenz)

[Descargar código](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/rating0.vb.zip) o [descargar PDF](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/rating0VB.pdf)

> Muchos sitios Web, de comercio electrónico para sitios de la Comunidad, ofrecen a los usuarios para los artículos de la velocidad o elementos. Esto normalmente requiere algún esfuerzo de codificación, pero tenemos el Kit de herramientas de Control a nuestra disposición.


## <a name="overview"></a>Información general

Muchos sitios Web, de comercio electrónico para sitios de la Comunidad, ofrecen a los usuarios para los artículos de la velocidad o elementos. Esto normalmente requiere algún esfuerzo de codificación, pero tenemos el Kit de herramientas de Control a nuestra disposición.

## <a name="steps"></a>Pasos

En primer lugar, se necesitan (al menos) dos tipos de imágenes: uno para una forma rellena el elemento clasificación y uno para un elemento vacío de clasificación. Un elemento de clasificación suele ser una estrella o una cara sonriente. En este escenario, encontrará tres archivos, smiley.png y empty.png y sonriente done.png como parte de las descargas de código fuente para este tutorial.

A continuación, cree un nuevo archivo ASP.NET y empezar a agregar un `ScriptManager` control:

[!code-aspx[Main](creating-a-rating-control-vb/samples/sample1.aspx)]

A continuación, agregue el `Rating` control de ASP.NET AJAX Control Toolkit. Los siguientes atributos deben establecerse en este ejemplo:

- `CurrentRating` la clasificación inicial que se usará
- `MaxRating` el valor máximo de clasificación
- `EmptyStarCssClass` la clase CSS que se usará cuando un elemento de clasificación (star) está vacía
- `FilledStarCssClass` la clase CSS que se utilizará al rellena un elemento de clasificación (star)
- `StarCssClass` la clase CSS que se usará para un estado visible
- `WaitingStarCssClass` la clase CSS para usar mientras una clasificación por estrellas se envía al servidor

Y aquí está el marcado que crea un control de clasificación con cinco elementos (emoticones) de los cuales ninguno se rellena inicialmente:

[!code-aspx[Main](creating-a-rating-control-vb/samples/sample2.aspx)]

Las tres clases CSS que se hace referencia ahora deben mostrar los archivos de imagen apropiada, que es fácil de hacer uso de CSS:

[!code-css[Main](creating-a-rating-control-vb/samples/sample3.css)]

Asegúrese de que proporcione el ancho y alto de las tres imágenes, en caso contrario, la visualización puede parecer un poco qué seguridad.

Por último, el resultado de la clasificación debe ser muestra al usuario (o, al menos se guardan en una base de datos). Por lo tanto, agregue una etiqueta para la salida de un mensaje de texto y un botón de envío para devolver el formato de clasificación en el servidor:

[!code-aspx[Main](creating-a-rating-control-vb/samples/sample4.aspx)]

En el código del lado servidor, obtener acceso al control de clasificación a través de su `ID` y, a continuación, obtener acceso a su `CurrentRating` propiedad que es el número de elementos de clasificación seleccionado, en nuestro ejemplo, un valor entre 0 y 5.

[!code-aspx[Main](creating-a-rating-control-vb/samples/sample5.aspx)]

Guarde la página y cargarlos en el explorador. Cuando mantiene el mouse sobre los elementos de clasificación (inicialmente vacía), se produce un efecto de JavaScript: los cambios de clasificación. Al hacer clic en el conjunto de estrellas, se conserva la clasificación actual. Por último, cuando se envía el formulario, el código del lado servidor da como resultado la clasificación seleccionada.


[![Creación de un sistema de clasificación con un código mínimo](creating-a-rating-control-vb/_static/image2.png)](creating-a-rating-control-vb/_static/image1.png)

Creación de un sistema de clasificación con un código mínimo ([haga clic aquí para ver imagen en tamaño completo](creating-a-rating-control-vb/_static/image3.png))

> [!div class="step-by-step"]
> [Anterior](creating-a-rating-control-cs.md)
