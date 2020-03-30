---
title: Ограничения запросов и регулирование
description: В данной статье описывается использование регулирования запросов Azure Resource Manager при достижении ограничений подписки.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239360"
---
# <a name="throttling-resource-manager-requests"></a>Регулирование запросов Resource Manager

В этой статье описывается, как запросы запрашивает менеджер ресурсов Azure. Он показывает, как отслеживать количество запросов, которые остаются до достижения предела, и как реагировать, когда вы достигли предела.

Дроссевание происходит на двух уровнях. Менеджер ресурсов Azure задушил запросы на подписку и арендатора. Если запрос находится под ограничениями регулирования для подписки и арендатора, менеджер ресурсов направляет запрос поставщику ресурсов. Поставщик ресурсов применяет ограничения регулирования, адаптированные к его деятельности. На следующем изображении показано, как регулирование применяется при запросе от пользователя к менеджеру ресурсов Azure и поставщику ресурсов.

![Запрос регулирования](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Ограничения подписки и арендатора

Каждая операция уровня подписки и уровня арендатора регулируется ограничениями. Запросы на подписку включают передачу идентификатора подписки, например, получение групп ресурсов в подписке. Запросы клиента не включают идентификатор подписки (например, извлечение допустимых расположений Azure).

Ограничения регулирования по умолчанию в час отображаются в следующей таблице.

| Область | Операции | Ограничение |
| ----- | ---------- | ------- |
| Подписка | reads | 12000 |
| Подписка | deletes | 15 000 |
| Подписка | writes | 1200 |
| Клиент | reads | 12000 |
| Клиент | writes | 1200 |

Эти ограничения касаются субъекта безопасности (пользователь или приложение), выполняющего запросы, а также идентификатора подписки или идентификатора клиента. Если запросы поступают от нескольких субъектов безопасности, ваше ограничение для подписки или клиента превышает 12 000 и 1200 в час.

Эти ограничения применяются к каждому экземпляру Azure Resource Manager. В каждом регионе Azure существует несколько экземпляров, а Azure Resource Manager развертывается во всех регионах Azure.  Таким образом, на практике пределы выше этих пределов. Запросы от пользователя обычно обрабатываются различными экземплярами управления ресурсами Azure.

## <a name="resource-provider-limits"></a>Ограничения поставщика ресурсов

Поставщики ресурсов применяют свои собственные ограничения на регулирование. Поскольку менеджер ресурсов задушит основным идентификатором и экземпляром ресурсного менеджера, поставщик ресурсов может получать больше запросов, чем лимиты по умолчанию в предыдущем разделе.

В этом разделе рассматриваются пределы регулирования некоторых широко используемых поставщиков ресурсов.

### <a name="storage-throttling"></a>Регулирование хранилища

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Регулирование сети

Поставщик ресурсов Microsoft.Network применяет следующие ограничения на дроссельной заслонки:

| Операция | Ограничение |
| --------- | ----- |
| написать / удалить (PUT) | 1000 за 5 минут |
| чтение (GET) | 10000 за 5 минут |

### <a name="compute-throttling"></a>Вычислительное регулирование

Для получения информации о ограничениях регулирования [Troubleshooting API throttling errors - Compute](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)для вычислительных операций см.

Для проверки виртуальных экземпляров машины в виртуальном наборе масштабов машины используйте [операции наборов виртуальной шкалы машин.](/rest/api/compute/virtualmachinescalesetvms) Например, используйте [виртуальный набор машин VMs - Список](/rest/api/compute/virtualmachinescalesetvms/list) с параметрами, чтобы проверить состояние питания виртуальных экземпляров машины. Этот API уменьшает количество запросов.

### <a name="azure-resource-graph-throttling"></a>Регулирование графиков ресурсов Azure

[График ресурсов Azure](../../governance/resource-graph/overview.md) ограничивает количество запросов для своих операций. Шаги в этой статье для определения оставшихся запросов и ответных мер при достигелимите также применяются к графику ресурсов. Тем не менее, Resource Graph устанавливает свой собственный предел и скорость сброса. Для получения дополнительной [Resource Graph throttling headers](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)информации см.

## <a name="error-code"></a>Код ошибки

По достижении ограничения отображается код состояния HTTP **429 — слишком много запросов**. Ответ включает значение **Retry-After,** которое определяет количество секунд, которые приложение должно ждать (или спать) перед отправкой следующего запроса. Если отправить запрос до истечения значения Retry-After, этот запрос не будет обработан, и будет возвращено новое значение Retry-After.

После ожидания указанного времени можно также закрыть и возобновить подключение к Azure. Сбросив соединение, можно подключиться к другому экземпляру менеджера ресурсов Azure.

Если вы используете SDK Azure, SDK может иметь конфигурацию автоматической повторной попытки. Для получения дополнительной информации смотрите [руководство Retry для служб Azure.](/azure/architecture/best-practices/retry-service-specific)

Некоторые поставщики ресурсов возвращают 429, чтобы сообщить о временной проблеме. Проблема может быть условием перегрузки, которое напрямую не вызвано вашим запросом. Или это может представлять собой временную ошибку о состоянии целевого ресурса или зависимого ресурса. Например, поставщик сетевых ресурсов возвращает 429 с кодом ошибки **RetryableErrorToAnotherOperation,** когда целевой ресурс заблокирован другой операцией. Чтобы определить, произошла ли ошибка в результате регулирования или временного состояния, просмотрите сведения об ошибке в ответе.

## <a name="remaining-requests"></a>Количество оставшихся запросов

Количество оставшихся запросов можно определить, проверяя заголовки ответов. Прочитайте запросы, верните значение в заголовке для количества оставшихся прочитаных запросов. Запросы на запись включают значение для числа оставшихся запросов на запись. В приведенной ниже таблице описаны заголовки ответов, в которых можно проверить эти значения.

| Заголовок ответа | Описание |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Оставшееся число запросов на чтение для подписки. Это значение возвращается при операциях чтения. |
| x-ms-ratelimit-remaining-subscription-writes |Оставшееся число запросов на запись для подписки. Это значение возвращается при операциях записи. |
| x-ms-ratelimit-remaining-tenant-reads |Оставшееся количество запросов на чтение для клиента. |
| x-ms-ratelimit-remaining-tenant-writes |Оставшееся количество запросов на запись для клиента. |
| x-ms-ratelimit-remaining-subscription-resource-requests |Оставшееся количество запросов для типа ресурса для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения подписки на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для подписки.<br /><br />Это значение заголовка возвращается только в том случае, если служба переопределила ограничение по умолчанию. Это значение содержит число оставшихся запросов коллекции (вывод ресурсов). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Оставшееся количество запросов для типа ресурса для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. Resource Manager добавляет это значение вместо ограничения клиента на запросы на чтение или запись. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Оставшееся количество запросов коллекции типов ресурсов для клиента.<br /><br />Этот заголовок добавляется только для запросов уровня клиента и только в том случае, если служба переопределила ограничение по умолчанию. |

Поставщик ресурсов также может возвращать заголовки ответов с информацией о оставшихся запросах. Для получения информации о заголовках ответов, [Call rate informational response headers](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)возвращенных поставщиком ресурсов Compute, см.

## <a name="retrieving-the-header-values"></a>Получение значений заголовков

Получение этих значений заголовков в коде или сценарии ничем не отличается от извлечения любого другого значения заголовка. 

Например, приведенный ниже код **C#** извлекает значение заголовка из объекта **HttpWebResponse** с именем **response**.

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

В **PowerShell** извлечь значение заголовка можно с помощью операции Invoke-WebRequest.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Полный пример для PowerShell см. в разделе [Проверьте ограничения Resource Manager для подписки](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Если же требуется узнать количество оставшихся запросов для отладки, можно указать параметр **-Debug** в командлете **PowerShell**.

```powershell
Get-AzResourceGroup -Debug
```

Он возвращает массу значений, включая следующее значение ответа.

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Чтобы получить ограничение на запись, используйте операцию записи: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Будет возвращено множество значений, включая следующие:

```output
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

```output
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
az group create -n myresourcegroup --location westus --verbose --debug
```

Будет возвращено множество значений, включая следующие:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Дальнейшие действия

* Полный пример для PowerShell см. в разделе [Проверьте ограничения Resource Manager для подписки](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Дополнительные сведения об ограничениях и квотах см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Сведения об обработке асинхронных запросов REST см. в статье [Track asynchronous Azure operations](async-operations.md) (Отслеживание асинхронных операций Azure).
