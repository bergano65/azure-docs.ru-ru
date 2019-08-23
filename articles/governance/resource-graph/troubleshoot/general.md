---
title: Устранение распространенных ошибок
description: Узнайте, как устранять проблемы с запросом ресурсов Azure с помощью графа ресурсов Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/21/2019
ms.topic: troubleshooting
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3c59b5c4b580604c65572364d29d4e5d10a26820
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900012"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Устранение ошибок с помощью графа ресурсов Azure

При запросе ресурсов Azure с помощью графа ресурсов Azure вы можете столкнуться с ошибками. В этой статье описываются различные ошибки, которые могут возникнуть, и пути их решения.

## <a name="finding-error-details"></a>Поиск сведений об ошибке

Большинство ошибок являются результатом проблемы при выполнении запроса со графом ресурсов Azure. При сбое запроса пакет SDK предоставляет сведения о неудачном запросе. Эта информация указывает на ошибку, чтобы ее можно было исправить, после чего запрос будет выполнен позже.

## <a name="general-errors"></a>Общие ошибки

### <a name="toomanysubscription"></a>Сценарий. Слишком много подписок

#### <a name="issue"></a>Проблемы

Клиенты, у которых есть доступ к более чем 1000 подпискам, включая межклиентские подписки с помощью [Azure лигхсаусе](../../../lighthouse/overview.md), не могут получать данные по всем подпискам в одном вызове к графу ресурсов Azure.

#### <a name="cause"></a>Причина:

Azure CLI и PowerShell пересылают только первые 1000 подписки на граф ресурсов Azure. REST API для графа ресурсов Azure принимает максимальное число подписок для выполнения запроса.

#### <a name="resolution"></a>Разрешение

Пакетные запросы для запроса с подмножеством подписок должны оставаться в пределах подписки 1000. Решение использует параметр **Subscription** в PowerShell.

```azurepowershell-interactive
# Replace this query with your own
$query = 'project type'

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

### <a name="rest-contenttype"></a>Сценарий. Неподдерживаемый заголовок содержимого типа Content-Type

#### <a name="issue"></a>Проблемы

Клиенты запрашивают граф ресурсов Azure REST API получить возвращенный ответ _500_ (внутренняя ошибка сервера).

#### <a name="cause"></a>Причина:

Граф ресурсов Azure REST API поддерживает `Content-Type` только **приложение/JSON**. Некоторые инструменты или агенты по умолчанию имеют **текст/Plain**, который не поддерживается REST API.

#### <a name="resolution"></a>Разрешение

Убедитесь, что средство или агент, которые вы используете для запроса графа ресурсов Azure, имеют `Content-Type` заголовок REST API, настроенный для **Application/JSON**.
### <a name="rest-403"></a>Сценарий. Нет разрешения на чтение для всех подписок в списке

#### <a name="issue"></a>Проблемы

Клиенты, которые явно передают список подписок с помощью запроса графа ресурсов Azure, получают ответ _403_ (запрещено).

#### <a name="cause"></a>Причина:

Если у клиента нет разрешения на чтение для всех предоставленных подписок, запрос отклоняется из-за отсутствия соответствующих прав доступа.

#### <a name="resolution"></a>Разрешение

Включите по крайней мере одну подписку в список подписок, чтобы у клиента, выполняющего запрос, был по крайней мере доступ для чтения. Дополнительные сведения см. [в статье разрешения в графе ресурсов Azure](../overview.md#permissions-in-azure-resource-graph).

## <a name="next-steps"></a>Следующие шаги

Если вы не видите своего варианта проблемы или вам не удается ее устранить, дополнительные сведения можно получить, посетив один из следующих каналов.

- Получите ответы от экспертов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
- Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
- Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.