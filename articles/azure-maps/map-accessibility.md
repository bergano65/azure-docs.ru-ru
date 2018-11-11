---
title: Создание доступного приложения с помощью Azure Maps | Документация Майкрософт
description: Сведения о создании доступного приложения с помощью Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 09/17/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: ef948b4dca3d3800a81ac52f3a73beee2558d21c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51247234"
---
# <a name="building-an-accessible-application"></a>Создание доступного приложения

В этой статье описывается, как создать приложение карты, которое может использовать устройство чтения с экрана.

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Создание доступного приложения' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Создание доступного приложения</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Созданная карта имеет встроенные функции специальных возможностей. Пользователи могут перемещаться по карте с помощью клавиатуры. Если запущено устройство чтения с экрана, карта будет уведомлять пользователя об изменениях в ее состоянии.
Например, пользователи получают уведомления об изменениях карты при панорамировании или изменении масштаба. Дополнительные сведения, размещаемые на базовой карте, должны содержать соответствующую текстовую информацию для пользователей устройства чтения с экрана.

Как вариант, для этого можно использовать класс [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest). В примере поиска выше всплывающее окно с текстовой информацией добавляется на карту для каждой размещенной на ней метки. При использовании метода [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) [всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) устройство чтения с экрана может распознавать это окно без его визуального отображения на карте.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классе Popup и методах работы со всплывающим окном, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Изучите дополнительные примеры кода:

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
