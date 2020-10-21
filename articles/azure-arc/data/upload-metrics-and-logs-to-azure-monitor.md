---
title: Передача данных об использовании, метрик и журналов в Azure Monitor
description: Отправляйте данные инвентаризации ресурсов, сведения об использовании, метрики и журналы в Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 869bfcb87aa4846674db233c4268e9269929cd04
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320169"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure-monitor"></a>Передача данных об использовании, метрик и журналов в Azure Monitor

Периодически можно экспортировать сведения об использовании для выставления счетов, метрики мониторинга и журналы, а затем передать их в Azure.  При экспорте и передаче любого из этих трех типов данных также будут созданы и обновлены ресурсы контроллера данных, управляемого экземпляра SQL и PostgreSQL в Azure.

> [!NOTE] 
> В течение периода действия предварительной версии не взимается плата за использование служб данных, включенных в службу Arc Azure.

## <a name="prerequisites"></a>предварительные требования

Вам потребуется Azure CLI (AZ) и [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] установленный.  [Установите средства](./install-client-tools.md).

Перед отправкой данных в Azure необходимо убедиться, что в подписке Azure зарегистрирован поставщик ресурсов Microsoft. Азуредата.

Это можно проверить, выполнив следующую команду:

```console
az provider show -n Microsoft.AzureData -o table
```

Если поставщик ресурсов в настоящее время не зарегистрирован в вашей подписке, его можно зарегистрировать, выполнив следующую команду.  Выполнение этой команды займет одну или две минуты.

```console
az provider register -n Microsoft.AzureData --wait
```

## <a name="upload-usage-data"></a>Отправка данных об использовании

Сведения об использовании, такие как Инвентаризация и использование ресурсов, можно отправить в Azure следующим образом:

1. Экспортируйте данные об использовании с помощью `azdata export` команды следующим образом:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the export command
   azdata arc dc export --type usage --path usage.json
   ```
   Эта команда создает `usage.json` файл со всеми ресурсами данных, включенными в дугу Azure, такими как управляемые экземпляры SQL и PostgreSQL экземпляров для масштабирования и т. д., которые создаются на контроллере данных.

2. Отправка данных об использовании с помощью `azdata upload` команды

   > [!NOTE]
   > Подождите по крайней мере 24 часа после создания контроллера данных ARC в Azure до запуска отправки.

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #run the upload command
   azdata arc dc upload --path usage.json
   ```

## <a name="upload-metrics-and-logs"></a>Отправка метрик и журналов

С помощью служб данных Arc Azure можно дополнительно передать метрики и журналы в Azure Monitor, чтобы можно было выполнять статистическую обработку и анализ метрик, журналов, создавать оповещения, отправлять уведомления или запускать автоматические действия. 

Отправляя данные в Azure Monitor, вы также можете хранить данные мониторинга и журналы за пределами сайта и в огромном масштабе, обеспечивая долгосрочное хранение данных для расширенной аналитики.

Если у вас есть несколько сайтов со службами данных Arc Azure, вы можете использовать Azure Monitor в качестве центрального расположения для сбора всех журналов и метрик на сайтах.

### <a name="before-you-begin"></a>Перед началом

Чтобы включить сценарии отправки журналов и метрик, необходимо выполнить несколько однократных действий по настройке.

1. Создание субъекта-службы/Azure Active Directory приложения, включая создание секрета клиентского доступа и назначение субъекта-службы роли "издатель метрик мониторинга" в подписках, где находятся ресурсы экземпляра базы данных.
2. Создайте рабочую область log Analytics и получите ключи и задайте сведения в переменных среды.

Первый элемент необходим для передачи метрик, а второй — для отправки журналов.

Выполните следующие команды, чтобы создать субъект-службу передачи метрик и назначить его ролям "издатель метрик мониторинга" и "участник", чтобы субъект-служба мог передать метрики и выполнить операции создания и передачи.

## <a name="create-service-principal-and-assign-roles"></a>Создание субъекта-службы и назначение ролей

Выполните следующие команды, чтобы создать субъект-службу передачи метрик и назначить его роли "издатель метрик мониторинга":

Чтобы создать субъект-службу, выполните следующую команду:

> [!NOTE]
> Для создания субъекта-службы требуются [определенные разрешения в Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

```console
az ad sp create-for-rbac --name <a name you choose>

#Example:
#az ad sp create-for-rbac --name azure-arc-metrics
```

Выходные данные примера:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Сохраните значения appId и клиента в переменной среды для последующего использования. 

Чтобы сохранить значения appId и клиента с помощью PowerShell, выполните приведенный ниже пример.

```powershell
$Env:SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
$Env:SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'
```

Кроме того, в Linux или macOS можно сохранить значения appId и клиента в этом примере:

   ```console
   export SPN_CLIENT_ID='<the 'appId' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_CLIENT_SECRET='<the 'password' value from the output of the 'az ad sp create-for-rbac' command above>'
   export SPN_TENANT_ID='<the 'tenant' value from the output of the 'az ad sp create-for-rbac' command above>'

   #Example (using Linux):
   export SPN_CLIENT_ID='2e72adbf-de57-4c25-b90d-2f73f126e123'
   export SPN_CLIENT_SECRET='5039d676-23f9-416c-9534-3bd6afc78123'
   export SPN_TENANT_ID='72f988bf-85f1-41af-91ab-2d7cd01ad1234'
   ```

Выполните эту команду, чтобы назначить субъекту-службе роль "издатель метрик мониторинга" в подписке, в которой находятся ресурсы экземпляра базы данных:


> [!NOTE]
> При запуске из среды Windows необходимо использовать двойные кавычки для имен ролей.


```console
az role assignment create --assignee <appId value from output above> --role "Monitoring Metrics Publisher" --scope subscriptions/<sub ID>
az role assignment create --assignee <appId value from output above> --role 'Contributor' --scope subscriptions/<sub ID>

#Example:
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role 'Contributor' --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123

#On Windows environment
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Monitoring Metrics Publisher" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
#az role assignment create --assignee 2e72adbf-de57-4c25-b90d-2f73f126ede5 --role "Contributor" --scope subscriptions/182c901a-129a-4f5d-56e4-cc6b29459123
```

Выходные данные примера:

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="create-a-log-analytics-workspace"></a>Создание рабочей области log Analytics

Затем выполните следующие команды, чтобы создать рабочую область Log Analytics и задать сведения о доступе в переменных среды.

> [!NOTE]
> Пропустите этот шаг, если у вас уже есть рабочая область.

```console
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>

#Example:
#az monitor log-analytics workspace create --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Выходные данные примера:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
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
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
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

Чтобы передать метрики для управляемых экземпляров SQL, включенных в службу "Дуга Azure", и службы "Дуга" PostgreSQL, выполните следующие команды интерфейса командной строки:

1. Экспортировать все метрики в указанный файл:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the metrics
   azdata arc dc export --type metrics --path metrics.json
   ```

2. Отправка метрик в Azure Monitor:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #upload the metrics
   azdata arc dc upload --path metrics.json
   ```

   >[!NOTE]
   >Подождите не менее 30 минут после создания экземпляров данных с поддержкой Arc Azure для первой отправки.
   >
   >Убедитесь `upload` , что метрики сразу после `export` Azure Monitor принимают только метрики за последние 30 минут. [Подробнее](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)


Если при экспорте отображаются ошибки, указывающие на неудачу получения метрик, ```true``` выполните следующую команду:

```console
azdata arc dc config show
```

и в разделе "раздел безопасности"

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Проверьте, `allowNodeMetricsCollection` установлены ли `allowPodMetricsCollection` для свойств и значение `true` .

## <a name="view-the-metrics-in-the-portal"></a>Просмотр метрик на портале

После отправки метрик можно просмотреть в портал Azure.
> [!NOTE]
> Обратите внимание, что перед просмотром метрик на портале может потребоваться несколько минут для обработки отправленных данных.


Чтобы просмотреть метрики на портале, используйте эту ссылку, чтобы открыть портал. <https://portal.azure.com> затем выполните поиск экземпляра базы данных по имени в строке поиска:

Вы можете просмотреть загрузку ЦП на странице "Обзор" или, если вы хотите получить более подробные метрики, щелкнув метрики на левой навигационной панели.

Выберите SQL Server в качестве пространства имен метрики:

Выберите метрику, которую необходимо визуализировать (можно также выбрать несколько):

Измените частоту до 30 минут:

> [!NOTE]
> Только метрики можно передать только за последние 30 минут. Azure Monitor отклоняет метрики старше 30 минут.

## <a name="upload-logs-to-azure-monitor"></a>Отправка журналов в Azure Monitor

 Чтобы отправить журналы для управляемых экземпляров SQL, включенных в службу "Дуга Azure", и Азуреарк Enabled PostgreSQL. группы серверов масштабирования выполняют следующие команды CLI

1. Экспортировать все журналы в указанный файл:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #export the logs
   azdata arc dc export --type logs --path logs.json
   ```

2. Отправка журналов в рабочую область журнала Azure Monitor Analytics:

   ```console
   #login to the data controller and enter the values at the prompt
   azdata login

   #Upload the logs
   azdata arc dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Просмотр журналов в портал Azure

К отправленным журналам можно обращаться с помощью обозревателя запросов журналов следующим образом:

1. Откройте портал Azure и найдите рабочую область по имени на панели поиска вверху, а затем выберите ее.
2. На панели слева щелкните "Журналы".
3. Щелкните "Начало работы" (или щелкните ссылки на странице начало работы, чтобы узнать больше о Log Analytics, если вы еще не знакомы с ней).
4. Следуйте указаниям в этом учебнике, чтобы узнать больше о Log Analytics при первом использовании Log Analytics
5. Разверните раздел "Пользовательские журналы" в нижней части списка таблиц. Вы увидите таблицу с именем sql_instance_logs_CL.
6. Щелкните значок с изображением глаза рядом с именем таблицы.
7. Нажмите кнопку "Просмотреть в редакторе запросов".
8. Теперь у вас есть запрос в редакторе запросов, который будет отображать последние 10 событий в журнале.
9. Здесь можно поэкспериментировать с отправкой запросов к журналам с помощью редактора запросов, настроить оповещения и т. д.

## <a name="automating-uploads-optional"></a>Автоматизация отправки (необязательно)

Если вы хотите отправлять метрики и журналы по расписанию, можно создать сценарий и запустить его через таймер каждые несколько минут. Ниже приведен пример автоматизации отправки с помощью сценария оболочки Linux.

В любом редакторе текста или кода добавьте следующий скрипт в файл и сохраните его как исполняемый файл сценария, например. sh (Linux/Mac) или. cmd,. bat,. ps1.

```console
azdata arc dc export --type metrics --path metrics.json --force
azdata arc dc upload --path metrics.json
```

Сделать исполняемый файл скрипта

```console
chmod +x myuploadscript.sh
```

Запускать скрипт каждые 20 минут:

```console
watch -n 1200 ./myuploadscript.sh
```

Можно также использовать планировщик заданий, например cron или Windows планировщик задач или Orchestrator, например Ansible, Puppet или Chef.

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Общие рекомендации по экспорту и передаче сведений об использовании, метриках

Операции создания, чтения, обновления и удаления (CRUD) в службе данных с включенной службой Arc Azure регистрируются в целях выставления счетов и мониторинга. Существуют фоновые службы, которые отслеживают эти операции CRUD и вычисляют потребление соответствующим образом. Фактическое вычисление использования или потребления происходит по расписанию и выполняется в фоновом режиме. 

Во время действия предварительной версии этот процесс происходит ночью. Общее руководство заключается в передаче сведений об использовании только один раз в день. Если сведения об использовании экспортируются и передаются несколько раз в течение одного 24-часового периода, то в портал Azure, но не на использование ресурсов обновляется только Инвентаризация ресурсов.

Для отправки метрик Azure Monitor принимает только последние 30 минут данных (дополнительные[сведения](../../azure-monitor/platform/metrics-store-custom-rest-api.md#troubleshooting)). Рекомендации по передаче метрик можно передать сразу после создания файла экспорта, чтобы вы могли просмотреть весь набор данных в портал Azure. Например, если вы экспортировали метрики в 2:00 PM и выполнили команду upload в 2:50 РМ. Поскольку Azure Monitor принимает данные только за последние 30 минут, на портале могут не отображаться данные. 

## <a name="next-steps"></a>Дальнейшие действия

[Отправьте данные о выставлении счетов в Azure и просмотрите их в портал Azure](view-billing-data-in-azure.md)

[Просмотр ресурса контроллера данных Arc Azure в портал Azure](view-data-controller-in-azure-portal.md)