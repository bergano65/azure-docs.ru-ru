---
title: Устранение неполадок подключения агента ARC для серверов Azure
description: В этой статье рассказывается, как устранять неполадки и устранять проблемы с агентом подключенных компьютеров, которые возникают при попытке подключения к службе с помощью Azure ARC для серверов (Предварительная версия).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/10/2020
ms.topic: conceptual
ms.openlocfilehash: 37f99ade366a73cb96caf55a562a92476223eb6b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86262207"
---
# <a name="troubleshoot-the-connected-machine-agent-connection-issues"></a>Устранение проблем с подключением агента подключенных компьютеров

В этой статье содержатся сведения об устранении неполадок и способах устранения проблем, которые могут возникнуть при попытке настроить агент машинного соединения Azure для серверов (предварительной версии) для Windows или Linux. При настройке подключения к службе включаются как интерактивные, так и масштабируемые методы установки. Общие сведения см. в разделе [Обзор ARC для серверов](./overview.md).

## <a name="agent-verbose-log"></a>Подробный журнал агента

Прежде чем выполнять действия по устранению неполадок, описанные ниже в этой статье, необходимо получить подробный журнал. Он содержит выходные данные команд инструмента **азкмажент** при использовании аргумента Verbose (-v). Файлы журнала записываются в `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` для Windows, а Linux — в `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Ниже приведен пример команды для включения подробного ведения журнала с помощью агента подключенного компьютера для Windows при выполнении интерактивной установки.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Ниже приведен пример команды для включения подробного ведения журнала с помощью агента подключенного компьютера для Windows при выполнении масштабируемой установки с использованием субъекта-службы.

```
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Ниже приведен пример команды для включения подробного ведения журнала с помощью агента подключенного компьютера для Linux при выполнении интерактивной установки.

```
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Ниже приведен пример команды для включения подробного ведения журнала с помощью агента подключенного компьютера для Linux при выполнении масштабируемой установки с использованием субъекта-службы.

```
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Проблемы с подключением агента к службе

В следующей таблице перечислены некоторые известные ошибки и рекомендации по их устранению.

|Message (Сообщение) |Ошибка |Возможные причины |Решение |
|--------|------|---------------|---------|
|Не удалось получить поток устройства маркера авторизации |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Не удается получить доступ к `login.windows.net` конечной точке | Проверьте подключение к конечной точке. |
|Не удалось получить поток устройства маркера авторизации |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Прокси-сервер или брандмауэр блокирует доступ к `login.windows.net` конечной точке. | Проверьте подключение к конечной точке, которое не блокируется брандмауэром или прокси-сервером. |
|Не удалось получить маркер авторизации из имени субъекта-службы |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Прокси-сервер или брандмауэр блокирует доступ к `login.windows.net` конечной точке. |Проверьте подключение к конечной точке, которое не блокируется брандмауэром или прокси-сервером. |
|Не удалось получить маркер авторизации из имени субъекта-службы |`Invalid client secret is provided` |Неправильный или недопустимый секрет субъекта-службы. |Проверьте секрет субъекта-службы. |
| Не удалось получить маркер авторизации из имени субъекта-службы |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Неверный субъект-служба или идентификатор клиента. |Проверьте субъект-службу и/или идентификатор клиента.|
|Получение ответа на ресурс ARM |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Неверные учетные данные и разрешения |Убедитесь, что вы или субъект-служба является членом роли **адаптации компьютера, подключенного к Azure** . |
|Не удалось Азкмажентконнект ресурс ARM |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |Поставщики ресурсов Azure не зарегистрированы. |Зарегистрируйте [поставщиков ресурсов](./agent-overview.md#register-azure-resource-providers). |
|Не удалось Азкмажентконнект ресурс ARM |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Прокси-сервер или брандмауэр блокирует доступ к `management.azure.com` конечной точке. |Проверьте подключение к конечной точке, которое не блокируется брандмауэром или прокси-сервером. |

## <a name="next-steps"></a>Дальнейшие действия

Если вы не нашли здесь свою проблему или рекомендации не позволили ее решить, попробуйте использовать один из следующих каналов для получения дополнительной поддержки.

* Получите ответы от экспертов Azure через [Microsoft Q&а](https://docs.microsoft.com/answers/topics/azure-arc.html).

* Подпишитесь на [@AzureSupport](https://twitter.com/azuresupport) — официальный канал Microsoft Azure для работы с клиентами. Служба поддержки Azure взаимодействует с сообществом Azure, предоставляя ответы, поддержку и советы экспертов.

* Отправьте запрос в службу поддержки Azure Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите **Получить поддержку**.