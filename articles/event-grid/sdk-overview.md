---
title: Пакеты SDK для службы "Сетка событий Azure"
description: Описывает пакеты SDK для службы "Сетка событий Azure". Эти пакеты SDK обеспечивают управление, публикацию и использование.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822850"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Пакеты SDK для управления службой "Сетка событий Azure" и публикации в ней

В службе "Сетка событий Azure" доступны пакеты SDK, которые позволяют управлять ресурсами и публиковать события с помощью программных средств.

## <a name="management-sdks"></a>Пакеты SDK для управления

Пакеты SDK для управления позволяют создавать, обновлять и удалять разделы и подписки сетки событий. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Пакеты SDK для плоскости данных

Пакеты SDK для плоскости данных позволяют публиковать события в разделы. Эти пакеты обеспечивают аутентификацию, формирование события и асинхронную публикацию на указанной конечной точке. Они также позволяют использовать события первой стороны. Сейчас доступны следующие пакеты SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [GO](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Дальнейшие действия

* Примеры приложений см. на странице [с примерами кода для Сетки событий](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* См. дополнительные сведения о [службе "Сетка событий Azure"](overview.md).
* Команды Azure CLI для службы "Сетка событий Azure" см. в статье об [Azure CLI](/cli/azure/eventgrid).
* Команды PowerShell для службы "Сетка событий Azure" см. в статье об [PowerShell](/powershell/module/az.eventgrid).
