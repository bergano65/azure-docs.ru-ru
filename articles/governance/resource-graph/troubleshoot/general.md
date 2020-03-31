---
title: Устранение распространенных ошибок
description: Узнайте, как устранить проблемы с различными SDK при запросе ресурсов Azure с помощью графика ресурсов Azure.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74303894"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Ошибки устранения неполадок с помощью графика ресурсов Azure

При запросе ресурсов Azure с помощью графика ресурсов Azure могут возникнуть ошибки. В этой статье описываются различные ошибки, которые могут возникнуть, и пути их решения.

## <a name="finding-error-details"></a>Поиск сведений об ошибке

Большинство ошибок являются результатом проблемы при запуске запроса с графиком ресурсов Azure. При сходе запроса в сбой SDK предоставляет подробную информацию о сбойном запросе. Эта информация указывает на проблему, так что она может быть исправлена и последующий запрос успешно.

## <a name="general-errors"></a>Общие ошибки

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>Сценарий: Слишком много подписок

#### <a name="issue"></a>Проблема

Клиенты, имеющие доступ к более чем 1000 подписок, включая кросс-арендаторы с [Azure Lighthouse,](../../../lighthouse/overview.md)не могут получать данные во всех подписках в рамках одного вызова в График ресурсов Azure.

#### <a name="cause"></a>Причина

Azure CLI и PowerShell перенаправляют только первые 1000 подписок на график ресурсов Azure. API REST для графика ресурсов Azure принимает максимальное количество подписок для выполнения запроса.

#### <a name="resolution"></a>Решение

Пакетные запросы для запроса с подмножеством подписок, чтобы остаться под ограничением подписки 1000. Решение мною использует параметр **подписки** в PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>Сценарий: Неподдерживаемый заголовок REST содержимого типа

#### <a name="issue"></a>Проблема

Клиенты, задающие запрос на API Azure Resource Graph REST, получают ответ _500_ (ошибка внутреннего сервера).

#### <a name="cause"></a>Причина

API Azure Resource Graph REST `Content-Type` поддерживает только **приложение/json.** Некоторые инструменты ИЛИ агенты REST по умолчанию по умолчанию к **тексту / равнине**, который не поддерживается REST API.

#### <a name="resolution"></a>Решение

Проверка того, что инструмент или агент, который вы используете для запроса Azure Resource Graph, настроен аголовок `Content-Type` REST API для **приложения/json.**

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>Сценарий: Нет разрешения на чтение всех подписок в списке

#### <a name="issue"></a>Проблема

Клиенты, которые явно передают список подписок с помощью запроса Azure Resource Graphry, получают ответ _403_ (Запрещенный).

#### <a name="cause"></a>Причина

Если клиент не прочитал разрешение на все предоставленные подписки, запрос отклоняется из-за отсутствия соответствующих прав безопасности.

#### <a name="resolution"></a>Решение

Включите хотя бы одну подписку в список подписки, к которым клиент, запуская запрос, имеет по крайней мере доступ к чтению. Для получения дополнительной информации смотрите [разрешения в графике ресурсов Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Дальнейшие действия

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure через [форумы Azure.](https://azure.microsoft.com/support/forums/)
- Связь [@AzureSupport](https://twitter.com/azuresupport) с официальной учетной записью Microsoft Azure для улучшения обслуживания клиентов путем подключения сообщества Azure к нужным ресурсам: ответам, поддержке и экспертам.
- Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **«Получите поддержку».**