---
title: Автоматическое резервное копирование значений ключей из хранилища конфигураций приложений Azure
description: Узнайте, как настроить автоматическую архивацию значений ключей между хранилищами конфигурации приложений.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: ef777f235c85c423ea126fb7249f8e6a11492d3a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528554"
---
# <a name="backup-app-configuration-stores-automatically"></a>Автоматическое резервное копирование конфигураций приложений

В этой статье вы узнаете, как настроить автоматическую архивацию значений ключей из основного хранилища конфигурации приложений в дополнительное хранилище. Она использует интеграцию службы "Сетка событий Azure" с конфигурацией приложения. После настройки приложение будет публиковать события в сетке событий для любых изменений, внесенных в значения ключа в хранилище конфигураций. Служба "Сетка событий" поддерживает различные службы Azure, от которых пользователи могут подписываться на события, генерируемые при создании, обновлении или удалении значений ключей.

## <a name="overview"></a>Обзор

В этом руководстве вы будете использовать очередь службы хранилища Azure для получения событий из службы "Сетка событий" и использования триггера таймера для функций Azure для обработки событий в очереди хранилища в пакетах. При активации на основе событий функция выберет последние значения ключей, которые были изменены из хранилища конфигурации основного приложения, и соответствующим образом обновите вторичное хранилище. Эта программа установки позволяет объединить несколько изменений, происходящих за короткий период, в одной операции резервного копирования и избежать чрезмерного количества запросов к хранилищам конфигурации приложений.

![Архитектура резервного копирования хранилища конфигурации приложений](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Подготовка ресурсов

Цель резервного копирования хранилищ конфигураций приложений заключается в использовании нескольких хранилищ конфигураций в разных регионах Azure для повышения географической устойчивости приложения. Для этого первичное и вторичное хранилища должны находиться в разных регионах Azure. Все остальные ресурсы, созданные в этом учебнике, можно подготовить в любом регионе по своему усмотрению. Это связано с тем, что если основной регион не работает, создание резервных копий будет недоступно, пока основной регион не будет доступен снова.

В этом руководстве вы создадите вторичное хранилище в `centralus` регионе и все остальные ресурсы в `westus` регионе.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/). При желании вы также можете использовать Azure Cloud Shell.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) с рабочей нагрузкой разработки Azure.
- Скачайте и установите [пакет SDK для .NET Core](https://dotnet.microsoft.com/download).
- Последняя версия Azure CLI (2.3.1 или более поздней версии). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli). При использовании Azure CLI необходимо сначала войти с помощью `az login` . При желании вы также можете использовать Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая коллекция, в которой выполняется развертывание и администрирование ресурсов Azure.

Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group).

В следующем примере создается группа ресурсов с именем `<resource_group_name>` в расположении именем `westus`.  Замените `<resource_group_name>` уникальным именем для группы ресурсов.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Создание хранилищ конфигурации приложений

Создайте хранилище конфигурации основного и дополнительного приложений в разных регионах.
Замените  `<primary_appconfig_name>` и `<secondary_appconfig_name>` уникальными именами для хранилищ конфигураций. Имя хранилища должно быть уникальным, так как оно используется в качестве DNS-имени.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Создание очереди службы хранилища Azure

Создайте учетную запись хранения и очередь хранилища для получения событий, опубликованных службой "Сетка событий".

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Подписка на события хранилища конфигурации приложения

Вы подписываетесь на эти два события из основного хранилища конфигурации приложений:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Следующая команда создает подписку на сетку событий для двух событий, отправляемых в очередь хранилища, где для параметра Тип конечной точки задано значение `storagequeue` , а для конечной точки — идентификатор очереди. Замените на `<event_subscription_name>` имя вашего выбора для подписки на события.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Создание функций Azure для обработки событий из очереди хранилища

### <a name="setup-with-ready-to-use-azure-functions"></a>Программа установки с готовыми к использованию функциями Azure

В этом руководстве вы будете работать с функциями Azure на C# со следующими свойствами:
- Стек среды выполнения .NET Core 3,1
- Среда выполнения функций Azure версии 3. x
- Функция, активируемая таймером каждые 10 минут

Чтобы упростить резервное копирование данных, мы протестировали и опубликовали [функции Azure](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , которые можно использовать без внесения каких бы то ни было изменений в код. Скачайте файлы проекта и [опубликуйте их в собственном приложение-функция Azure из Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Не вносите изменения в переменные среды в скачанном коде. Необходимые параметры приложения будут создаваться в следующем разделе.
>

### <a name="build-your-own-azure-functions"></a>Создание собственных функций Azure

Если приведенный выше пример кода не соответствует вашим требованиям, можно также создать собственные функции Azure. Для завершения резервного копирования функция должна иметь возможность выполнять следующие задачи:
- Периодически Читайте содержимое очереди хранилища, чтобы проверить, содержит ли она уведомления из службы "Сетка событий". Сведения о реализации см. в [пакете SDK очереди хранилища](/azure/storage/queues/storage-quickstart-queues-dotnet) .
- Если очередь хранилища содержит [уведомления о событиях из службы "Сетка событий](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)", извлеките все уникальные <ключа, на> метки из сообщений о событиях. Сочетание клавиш и меток — это уникальный идентификатор для изменений "ключ-значение" в основном хранилище.
- Чтение всех параметров из основного хранилища. Обновите только те параметры в вторичном хранилище, которые имеют соответствующее событие в очереди хранилища. Удалите все параметры из вторичного хранилища, которые присутствовали в очереди хранилища, но не в основном хранилище. Вы можете использовать [пакет SDK для настройки приложений](https://github.com/Azure/AppConfiguration#sdks) для программного доступа к хранилищам конфигурации.
- Удаление сообщений из очереди хранилища, если во время обработки не возникало исключений.
- Не забудьте реализовать обработку ошибок в соответствии с вашими потребностями. Приведенный выше пример кода можно использовать, чтобы увидеть некоторые распространенные исключения, которые может потребоваться решить.

Дополнительные сведения о создании функций Azure см. в статье [Создание функции в Azure, которая активируется таймером](/azure/azure-functions/functions-create-scheduled-function) и [разрабатывает функции Azure с помощью Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Используйте оптимальное значение для выбора расписания таймера в зависимости от частоты внесения изменений в основное хранилище конфигураций. Помните, что слишком частое выполнение функции может привести к нехватке запросов на регулирование хранилища.
>


## <a name="create-azure-function-app-settings"></a>Создание параметров приложение-функция Azure

Если вы используете предоставляемые вами функции Azure, в приложение-функция Azure потребуются следующие параметры приложения:
- `PrimaryStoreEndpoint`: Конечная точка для основного хранилища конфигурации приложений. Например `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Конечная точка для дополнительного хранилища конфигурации приложения. Например `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: URI очереди хранилища. Например `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Следующая команда создает необходимые параметры приложения в приложение-функция Azure. Замените `<function_app_name>` именем приложение-функция Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Предоставление доступа к управляемому удостоверению Azure приложение-функция

Используйте следующую команду или [портал Azure](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) , чтобы добавить управляемое системой удостоверение для приложение-функция Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Для выполнения требуемого создания ресурсов и управления ролями учетной записи требуются `'Owner'` разрешения в соответствующей области (ваша подписка или группа ресурсов). Если вам нужна помощь с назначением ролей, Узнайте, [как добавлять или удалять назначения ролей Azure с помощью портал Azure](/azure/role-based-access-control/role-assignments-portal).

Используйте следующие команды или [портал Azure](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) , чтобы предоставить управляемому удостоверению Azure приложение-функция доступ к хранилищам конфигурации приложений.
- Назначьте `App Configuration Data Reader` роль в основном хранилище конфигураций приложений.
- Назначьте `App Configuration Data Owner` роль в хранилище конфигурации дополнительного приложения.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Используйте следующую команду или [портал Azure](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) , чтобы предоставить управляемому удостоверению Azure приложение-функция доступ к вашей очереди хранилища. 
- Назначение `Storage Queue Data Contributor` роли в очереди хранилища.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Активация события Конфигурации приложений

Чтобы убедиться, что все работает, можно создать, обновить или удалить значение ключа из основного хранилища. Это изменение должно автоматически отображаться в дополнительном хранилище через несколько секунд после запуска службы "функции Azure" с помощью таймера.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Вы активировали событие, и в сетке событий в течение нескольких секунд уведомление о событии будет отправлено в очередь службы хранилища Azure. ***После следующего запланированного выполнения функций Azure***просмотрите параметры конфигурации в дополнительном хранилище, чтобы узнать, содержит ли оно обновленное значение ключа из основного хранилища.

> [!NOTE]
> Вы можете [активировать функции Azure вручную](/azure/azure-functions/functions-manually-run-non-http) во время тестирования и устранения неполадок, не дожидаясь запланированного триггера таймера.

Убедившись в успешном выполнении функции резервного копирования, вы увидите, что ключ теперь имеется в дополнительном хранилище.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Устранение неполадок

Если вы не видите новый параметр в дополнительном хранилище:

- Убедитесь, что функция резервного копирования была активирована ***после*** создания параметра в основном хранилище.
- Возможно, сетка событий не смогла отправить уведомление о событии в очередь хранилища вовремя. Убедитесь, что ваша очередь хранилища по-прежнему содержит уведомление о событии из основного хранилища. Если это так, снова запустите функцию резервного копирования.
- Проверьте [журналы функций Azure](/azure/azure-functions/functions-create-scheduled-function#test-the-function) на наличие ошибок или предупреждений.
- Используйте [портал Azure](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) , чтобы убедиться, что приложение-функция Azure содержит правильные значения параметров приложения, которые ваши функции Azure пытаются считать.
- Вы также можете настроить мониторинг и оповещения для функций Azure с помощью [azure Application Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Очистка ресурсов
Если вы планируете продолжать работу с этой Конфигурацией приложений и подпиской на события, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, удалите все созданные ресурсы в этой статье.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как настроить автоматическое резервное копирование значений ключей, Узнайте больше о том, как можно повысить географическую устойчивость приложения:

- [Устойчивость и аварийное восстановление](concept-disaster-recovery.md)
