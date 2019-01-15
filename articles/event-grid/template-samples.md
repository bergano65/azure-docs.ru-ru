---
title: Примеры шаблонов Azure Resource Manager для службы "Сетка событий" | Документация Майкрософт
description: Примеры шаблонов Azure Resource Manager для службы "Сетка событий"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/04/2019
ms.author: tomfitz
ms.openlocfilehash: 788d23c7bddd90c1e12a118742c651eb9759529c
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062967"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Шаблоны Azure Resource Manager для службы "Сетка событий"

Синтаксис и свойства JSON, используемые в шаблоне, см. в статье о [типах ресурсов Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для службы "Сетка событий".

| | |
|-|-|
|**Подписки на службу "Сетка событий"**||
| [Использование настраиваемых разделов и подписок с конечной точкой веб-перехватчика](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Развертывает пользовательский раздел службы "Сетка событий". Создает подписку на этот пользовательский раздел, который использует конечную точку веб-перехватчика. |
| [Использование настраиваемых разделов и подписок с конечной точкой концентратора событий](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Создает подписку службы "Сетка событий" на пользовательский раздел. Подписка использует концентратор событий для конечной точки. |
| [Create Event Grid subscription for resource events](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) (Создание подписки службы "Сетка событий" на события ресурсов)| Подписывается на события для группы ресурсов или подписки Azure. Группа ресурсов, указанная как целевой объект во время развертывания, является источником событий. Подписка использует веб-перехватчик для конечной точки. |
| [Учетная запись хранения BLOB-объектов и подписка](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Развертывает учетную запись службы хранилища больших двоичных объектов Azure и создает подписку на события для этой учетной записи хранения. |
| | |
