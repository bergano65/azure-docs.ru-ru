---
title: Ограничения и регулирование запросов Azure Resource Manager
description: В данной статье описывается использование регулирования запросов Azure Resource Manager при достижении ограничений подписки.
services: azure-resource-manager
documentationcenter: na
author: rockboyfor
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 03/05/2019
ms.date: 03/18/2019
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 91a776ba13ffaeeb4f8184371ae45a80d829ae46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389735"
---
# <a name="throttling-resource-manager-requests"></a>Регулирование запросов Resource Manager

Для каждой подписки и клиента Azure Resource Manager ограничивает число запросов на чтение до 12 000 и запросов на запись — до 1200 в час. Эти ограничения относятся к идентификатору участника, используемому для выполнения запросов, и идентификатору подписки или идентификатору клиента. Если запросы поступают от нескольких идентификаторов субъектов, ваш лимит для подписки или клиента превышает 12000 и 1200 в час.

Запросы применяются к вашей подписке или вашему клиенту. Запросы подписки включают в себя передачу идентификатора подписки (например, извлечение групп ресурсов в подписке). Запросы клиента не включают идентификатор подписки (например, извлечение допустимых расположений Azure).

Эти ограничения применяются к каждому экземпляру Azure Resource Manager. В каждом регионе Azure существует несколько экземпляров, а Azure Resource Manager развертывается во всех регионах Azure.  На практике ограничений гораздо больше, чем указано выше, так как запросы пользователя обычно обслуживаются разными экземплярами.

Если приложение или сценарий достигает этих предельных значений, то требуется регулировать запросы. В этой статье показано, как определить, сколько осталось запросов до достижения предела и как реагировать на достигнут предел.

По достижении ограничения отображается код состояния HTTP **429 — слишком много запросов**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="remaining-requests"></a>Количество оставшихся запросов
Количество оставшихся запросов можно определить, проверяя заголовки ответов. Запросы на чтение возвращать значение из заголовка на количество оставшихся запросов на чтение. Написать запросы включают в себя значение для количества оставшихся запросов на запись. В приведенной ниже таблице описаны заголовки ответов, в которых можно проверить эти значения.

| Заголовок ответа | ОПИСАНИЕ |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Оставшееся число запросов на чтение для подписки. Это значение возвращается при операциях чтения. |
| x-ms-ratelimit-remaining-subscription-writes |Оставшееся число запросов на запись для подписки. Это значение возвращается при операциях записи. |
| x-ms-ratelimit-remaining-tenant-reads |Оставшееся количество запросов на чтение для клиента. |
| x-ms-ratelimit-remaining-tenant-writes |Оставшееся количество запросов на запись для клиента. |
| x-ms-ratelimit-remaining-subscription-resource-requests |Оставшееся количество запросов для типа ресурса для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения подписки на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Это значение содержит число оставшихся запросов коллекции (вывод ресурсов). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Оставшееся количество запросов для типа ресурса для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения клиента на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. |

## <a name="retrieving-the-header-values"></a>Получение значений заголовков
Получение этих значений заголовков в коде или сценарии ничем не отличается от извлечения любого другого значения заголовка. 

Например, приведенный ниже код **C#** извлекает значение заголовка из объекта **HttpWebResponse** с именем **response**.

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

В **PowerShell** извлечь значение заголовка можно с помощью операции Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.chinacloudapi.cn/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Полный пример для PowerShell см. в разделе [Проверьте ограничения Resource Manager для подписки](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Если же требуется узнать количество оставшихся запросов для отладки, можно указать параметр **-Debug** в командлете **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Он возвращает массу значений, включая следующее значение ответа.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Чтобы получить ограничение на запись, используйте операцию записи: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location chinanorth -Debug
```

Будет возвращено множество значений, включая следующие:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

В **интерфейсе командной строки Azure** извлечь значение заголовка можно с помощью параметра подробного вывода.

```azurecli
az group list --verbose --debug
```

Будет возвращено множество значений, включая следующие:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Чтобы получить ограничение на запись, используйте операцию записи: 

```azurecli
az group create -n myresourcegroup --location chinanorth --verbose --debug
```

Будет возвращено множество значений, включая следующие:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Ожидание перед отправкой следующего запроса
По достижении предельного количества запросов Resource Manager возвращает в заголовке код состояния HTTP **429** и значение **Retry-After**. Значение **Retry-After** указывает период времени в секундах, в течение которого приложение ожидает (или находится в спящем режиме), прежде чем отправить следующий запрос. Если отправить запрос до истечения значения Retry-After, этот запрос не будет обработан, и будет возвращено новое значение Retry-After.

## <a name="next-steps"></a>Дальнейшие действия

* Полный пример для PowerShell см. в разделе [Проверьте ограничения Resource Manager для подписки](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Дополнительные сведения об ограничениях и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](../azure-subscription-service-limits.md).
* Сведения об обработке асинхронных запросов REST см. в статье [Track asynchronous Azure operations](resource-manager-async-operations.md) (Отслеживание асинхронных операций Azure).

<!--Update_Description: update meta properties, update cmdlet -->