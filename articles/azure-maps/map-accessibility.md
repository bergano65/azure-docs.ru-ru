---
title: Создание доступного приложения с помощью Azure Maps | Документация Майкрософт
description: Сведения о создании доступного приложения с помощью Azure Maps
services: azure-maps
author: chgennar
ms.author: chgennar
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
manager: timlt
ms.openlocfilehash: 8865027c895be09150797608e43184f1fdefb267
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638789"
---
# <a name="building-an-accessible-application"></a>Создание доступного приложения

В этой статье описывается, как создать приложение карты, которое может использовать устройство чтения с экрана.

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Создание доступного приложения' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Создание доступного приложения</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Созданная карта имеет встроенные функции специальных возможностей. Пользователи могут перемещаться по карте с помощью клавиатуры. Если запущено устройство чтения с экрана, карта будет уведомлять пользователя об изменениях в ее состоянии.
Например, пользователи получают уведомления об изменениях карты при панорамировании или изменении масштаба. Дополнительные сведения, размещаемые на базовой карте, должны содержать соответствующую текстовую информацию для пользователей устройства чтения с экрана.

Как вариант, для этого можно использовать класс [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest). В примере поиска выше всплывающее окно с текстовой информацией добавляется на карту для каждой размещенной на ней метки. При использовании метода [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach) [всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) устройство чтения с экрана может распознавать это окно без его визуального отображения на карте.

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о классе Popup и методах работы со всплывающим окном, которые используются в этой статье:

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)

Изучите дополнительные примеры кода:

> [!div class="nextstepaction"]
> [Страница с примерами кода](https://aka.ms/AzureMapsSamples)
