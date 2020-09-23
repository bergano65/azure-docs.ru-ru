---
title: Отправка сведений об использовании, метриках и журналах ресурсов для Azure Monitor
description: Отправка сведений об использовании, метриках и журналах ресурсов для Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ac6ffd2b5bf48079db6a0cd261dbe2535e1821ac
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939108"
---
# <a name="upload-resource-inventory-usage-data-metrics-and-logs-to-azure-monitor"></a>Отправка сведений об использовании, метриках и журналах ресурсов для Azure Monitor

С помощью служб данных Arc Azure можно *Дополнительно* передать метрики и журналы в Azure Monitor, чтобы можно было выполнять статистическую обработку и анализ метрик, журналов, создавать оповещения, отправлять уведомления или запускать автоматические действия. Отправляя данные в Azure Monitor, вы также можете хранить данные мониторинга и журналов на уровне сайта и в огромном масштабе, обеспечивая долгосрочное хранение данных для расширенной аналитики.  Если у вас есть несколько сайтов со службами данных Arc Azure, вы можете использовать Azure Monitor в качестве центрального расположения для сбора всех журналов и метрик на сайтах.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="before-you-begin"></a>Перед началом

Чтобы включить сценарии отправки журналов и метрик, необходимо выполнить несколько однократных действий по настройке.

1) Создание субъекта-службы/Azure Active Directory приложения, включая создание секрета клиентского доступа и назначение субъекта-службы роли "издатель метрик мониторинга" в подписках, где находятся ресурсы экземпляра базы данных.
2) Создайте рабочую область log Analytics и получите ключи и задайте сведения в переменных среды.

Первый элемент необходим для передачи метрик, а второй — для отправки журналов.

Выполните следующие команды, чтобы создать субъект-службу передачи метрик и назначить его ролям "издатель метрик мониторинга" и "участник", чтобы субъект-служба мог передать метрики и выполнить операции создания и передачи.

## <a name="create-service-principal-and-assign-roles"></a>Создание субъекта-службы и назначение ролей

Выполните следующие команды, чтобы создать субъект-службу передачи метрик и назначить его роли "издатель метрик мониторинга":

Чтобы создать субъект-службу, выполните следующую команду:

> [!NOTE]
> Для создания субъекта-службы требуются [определенные разрешения в Azure](/active-directory/develop/howto-create-service-principal-portal#required-permissions).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Выходные данные примера:

```console
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Сохраните значения appId и клиента в переменной среды для последующего использования:

```console
#PowerShell

$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Linux/macOS

export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

#Example (using Linux):
export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
```

Выполните эту команду, чтобы назначить субъекту-службе роль "издатель метрик мониторинга" в подписке, в которой находятся ресурсы экземпляра базы данных:

```console
az role assignment create --assignee <appId value from output above> --role 'Monitoring Metrics Publisher' --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Monitoring Metrics Publisher' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Выходные данные примера:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области log Analytics

Затем выполните следующие команды, чтобы создать рабочую область Log Analytics и задать сведения о доступе в переменных среды.

> [!NOTE]
> Пропустите этот шаг, если у вас уже есть рабочая область.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --name MyLogsWorkpace
```

Выходные данные примера:

```console
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/182c901a-129a-4f5d-86e4-cc6b29459123/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Назначение ИДЕНТИФИКАТОРов и общего ключа переменным среды

Сохраните customerId (идентификатор рабочей области) в качестве переменной среды, которая будет использоваться позже:

```console
#PowerShell
$Env:WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Linux/macOS
export WORKSPACE_ID='<the customerId from the 'log-analytics workspace create' command output above>'

#Example (using Linux)
#export WORKSPACE_ID='d6abb435-2626-4df1-b887-445fe44a4123'
```

Эта команда выведет на печать ключи доступа, необходимые для подключения к рабочей области log Analytics:

```console
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --name MyLogsWorkpace
```

Выходные данные примера:

```console
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Сохраните первичный ключ в переменной среды, которая будет использоваться позже:

```console
#PowerShell:
$Env:WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Linux/macOS:
export WORKSPACE_SHARED_KEY='<the primarySharedKey value from the 'get-shared-keys' command above'

#Example (using Linux):
export WORKSPACE_SHARED_KEY='JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123=='

```

## <a name="set-final-environment-variables-and-confirm"></a>Задание конечных переменных среды и подтверждение

Задайте URL-адрес центра SPN в переменной среды:

```console
#PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'

#Linux/macOS:
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

Убедитесь, что все необходимые переменные среды заданы при необходимости.

```console
#PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY

#Linux/macOS
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

## <a name="upload-metrics-to-azure-monitor"></a>Отправка метрик в Azure Monitor

Чтобы передать метрики для управляемых экземпляров SQL Azure и баз данных Azure для PostgreSQL в группы серверов масштабирования, выполните следующие команды интерфейса командной строки:

Все метрики будут экспортированы в указанный файл:

```console
azdata arc dc export -t metrics --path metrics.json
```

Метрики будут отправлены в Azure Monitor:

```console
azdata arc dc upload --path metrics.json
```

## <a name="view-the-metrics-in-the-portal"></a>Просмотр метрик на портале

После отправки метрик вы сможете визуализировать их на портале Azure.

Чтобы просмотреть метрики на портале, используйте эту специальную ссылку, чтобы открыть портал. <https://portal.azure.com> затем выполните поиск экземпляра базы данных по имени в строке поиска:

Вы можете просмотреть загрузку ЦП на странице "Обзор" или, если вы хотите получить более подробные метрики, щелкнув метрики на левой навигационной панели.

Выберите SQL Server в качестве пространства имен метрики:

Выберите метрику, которую необходимо визуализировать (можно также выбрать несколько):

Измените частоту до 30 минут:

> [!NOTE]
> Только метрики можно передать только за последние 30 минут. Azure Monitor отклоняет метрики старше 30 минут.

## <a name="upload-logs-to-azure-monitor"></a>Отправка журналов в Azure Monitor

 Чтобы отправить журналы для управляемых экземпляров SQL Azure и группы серверов масштабирования базы данных Azure для PostgreSQL, выполните следующие команды интерфейса командной строки:

Все журналы будут экспортированы в указанный файл:

```console
azdata arc dc export -t logs --path logs.json
```

Журналы будут отправлены в рабочую область Azure Monitor Analytics.

```console
azdata arc dc upload --path logs.json
```

## <a name="view-your-logs-in-azure-portal"></a>Просмотр журналов в портал Azure

К отправленным журналам можно обращаться с помощью обозревателя запросов журналов следующим образом:

1. Откройте портал Azure и найдите рабочую область по имени на панели поиска вверху, а затем выберите ее.
2. На панели слева щелкните "Журналы".
3. Щелкните "Начало работы" (или щелкните ссылки на странице начало работы, чтобы узнать больше о Log Analytics, если вы еще не знакомы с ней).
4. Следуйте указаниям в этом учебнике, чтобы получить дополнительные сведения о Log Analytics, если первый раз
5. Разверните раздел "Пользовательские журналы" в нижней части списка таблиц. Вы увидите таблицу с именем sql_instance_logs_CL.
6. Щелкните значок с изображением глаза рядом с именем таблицы.
7. Нажмите кнопку "Просмотреть в редакторе запросов".
8. Вы увидите запрос в редакторе запросов, где отображаются последние 10 событий в журнале.
9. Здесь можно поэкспериментировать с отправкой запросов к журналам с помощью редактора запросов, настроить оповещения и т. д.

## <a name="automating-metrics-and-logs-uploads-optional"></a>Автоматизация отправки метрик и журналов (необязательно)

Если вы хотите постоянно отправлять метрики и журналы, можно создать сценарий и запустить его через таймер каждые несколько минут.  Ниже приведен пример автоматизации отправки с помощью сценария оболочки Linux.

В любом редакторе текста или кода добавьте следующий код в содержимое скрипта в файл и сохраните его как исполняемый файл сценария, например. sh (Linux/Mac) или. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Сделать исполняемый файл скрипта

```console
chmod +x myuploadscript.sh
```

Запускать скрипт каждые 2 минуты:

```console
watch -n 120 ./myuploadscript.sh
```

Можно также использовать планировщик заданий, например cron или Windows планировщик задач или Orchestrator, например Ansible, Puppet или Chef.
