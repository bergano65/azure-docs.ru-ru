---
title: Управление версиями пакетов SDK клиента и сервера в мобильных приложениях и мобильных службах | Документация Майкрософт
description: Список пакетов SDK клиента и сведения о совместимости с версиями пакетов SDK сервера для мобильных служб и мобильных приложений Azure
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 7133e8bc7d04b3653b6b788347b7bc5176087f4c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883477"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Управление версиями клиента и сервера в мобильных приложениях и мобильных службах
Последняя версия мобильных служб Azure — компонент **Мобильные приложения** службы приложений Azure.

Пакеты SDK для клиента и сервера мобильных приложений основаны на аналогичных пакетах мобильных служб, но *не совместимы* с ними.
Другими словами, пакет SDK клиента *мобильных приложений* необходимо использовать с пакетом SDK сервера *мобильных приложений* (точно так же и для *мобильных служб*). Этот контракт реализуется посредством специального значения заголовка, используемого пакетами SDK для клиента и сервера, `ZUMO-API-VERSION`.

Примечание. Когда в этом документе упоминается внутренний сервер *мобильных служб*, он не обязательно должен размещаться в мобильных службах. Теперь можно выполнить миграцию мобильной службы для работы в службе приложений без внесения изменений в код, однако служба по-прежнему будет использовать версии пакета SDK для *мобильных служб*.

## <a name="header-specification"></a>Спецификация заголовка
Ключ `ZUMO-API-VERSION` можно указать в заголовке HTTP или в строке запроса. Его значение представляет строку версии в формате **x.y.z**.

Пример:

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>Отказ от проверки версий
От проверки версий можно отказаться, задав для параметра приложения **MS_SkipVersionCheck** значение **true**. Укажите это значение в файле web.config или в разделе параметров приложения портала Azure.

> [!NOTE]
> Работа мобильных приложений отличается от мобильных служб в нескольких аспектах, в частности, в сфере автономной синхронизации, проверки подлинности и push-уведомлений. Вы можете отказаться от проверки версий только после выполнения тщательного тестирования, чтобы изменения в работе не нарушили функциональность приложения.

## <a name="2.0.0"></a>Клиент и сервер мобильных приложений Azure
### <a name="MobileAppsClients"></a> Пакеты SDK для клиента мобильных *приложений*
Проверка версии была добавлена, начиная со следующих версий пакета SDK для клиента **мобильных приложений Azure**:

| Платформа клиента | Version | Значение заголовка версии |
| --- | --- | --- |
| Управляемый клиент (Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Пакеты SDK для сервера мобильных *приложений*
Проверка версий входит в состав следующих версий пакета SDK сервера:

| Платформа сервера | SDK | Принятый заголовок версии |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Поведение внутренних серверов мобильных приложений
| ZUMO-API-VERSION | Значение параметра MS_SkipVersionCheck | Отклик |
| --- | --- | --- |
| x.y.z или значение NULL |True |200 – OK |
| NULL |False/не указан |400 – неверный запрос |
| 1.x.y |False/не указан |400 – неверный запрос |
| 2.0.0-2.x.y |False/не указан |200 – OK |
| 3.0.0-3.x.y |False/не указан |400 – неверный запрос |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
