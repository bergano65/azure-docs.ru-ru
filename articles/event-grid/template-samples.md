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
ms.date: 09/18/2018
ms.author: tomfitz
ms.openlocfilehash: c462334597b41b914b6a0a0e3c8a67ad97e2bd7a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295587"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Шаблоны Azure Resource Manager для службы "Сетка событий"

В следующей таблице представлены ссылки на шаблоны Azure Resource Manager для службы "Сетка событий".

| | |
|-|-|
|**Подписки на службу "Сетка событий"**||
| [Использование настраиваемых разделов и подписок с конечной точкой веб-перехватчика](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Развертывает пользовательский раздел службы "Сетка событий". Создает подписку на этот пользовательский раздел, который использует конечную точку веб-перехватчика. |
| [Использование настраиваемых разделов и подписок с конечной точкой концентратора событий](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Создает подписку службы "Сетка событий" на пользовательский раздел. Подписка использует концентратор событий для конечной точки. |
| [Create Event Grid subscription for resource events](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) (Создание подписки службы "Сетка событий" на события ресурсов)| Подписывается на события для группы ресурсов или подписки Azure. Группа ресурсов, указанная как целевой объект во время развертывания, является источником событий. Подписка использует веб-перехватчик для конечной точки. |
| [Учетная запись хранения BLOB-объектов и подписка](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Развертывает учетную запись службы хранилища больших двоичных объектов Azure и создает подписку на события для этой учетной записи хранения. |
| | |
