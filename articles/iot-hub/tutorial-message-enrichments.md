---
title: Руководство. Использование дополнений сообщений в центре Интернета вещей Azure
description: Руководство, в котором показано, как использовать обогащение сообщений для сообщений центра Интернета вещей Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 77d900844705bb86ce4bcfeda31d6ee765cb8d45
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69535011"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Учебник. Использование дополнений сообщений в центре Интернета вещей Azure (Предварительная версия)

*Обогащение сообщений* — это способность центра Интернета вещей отмечать сообщения дополнительными сведениями перед отправкой сообщений в указанную конечную точку. Одной из причин использования обогащений сообщений является включение данных, которые могут быть использованы для упрощения обработки в более низком направлении. Например, дополнение сообщений телеметрии устройства с помощью тега двойникаа устройства может снизить нагрузку на клиентов, чтобы сделать вызовы API двойника для этих данных. Дополнительные сведения см. в обзоре дополнений [сообщений](iot-hub-message-enrichments-overview.md).

В этом руководстве используется Azure CLI для настройки ресурсов, включая две конечные точки, которые указывают на два разных контейнера хранилища — обогащенные и **оригинальные**. Затем используется [портал Azure](https://portal.azure.com) для настройки обогащений сообщений, которые будут применяться только к сообщениям, отправляемым в конечную точку с помощью расширенного контейнера хранилища. Вы отправляете сообщения в центр Интернета вещей, который направляется в оба контейнера хранилища. Только сообщения, отправленные конечной точке для **обогащенного** контейнера хранилища, будут дополнены.

Ниже приведены задачи, которые необходимо выполнить для выполнения этого учебника.

**Использование дополнений сообщений центра Интернета вещей**
> [!div class="checklist"]
> * С помощью Azure CLI создайте ресурсы — центр Интернета вещей, учетную запись хранения с двумя конечными точками и конфигурацию маршрутизации.
> * Используйте портал Azure для настройки обогащений сообщений.
> * Запустите приложение, которое имитирует устройство IoT, отправляющее сообщения в центр.
> * Просмотр результатов и проверка правильности работы сообщений.

## <a name="prerequisites"></a>Предварительные требования

* У вас должна быть подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Установить [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Получение примера кода

Загрузите [моделирование устройства IOT](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) и распакуйте его. Этот репозиторий содержит несколько приложений, включая те, которые вы будете использовать для отправки сообщений в центр Интернета вещей.

Этот файл также содержит скрипт для создания ресурсов, используемых для тестирования дополнений сообщений. Скрипт находится в/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Сейчас можно взглянуть на сценарий и использовать его. Скрипт также можно скопировать непосредственно из статьи.

Когда вы будете готовы начать тестирование, вы будете использовать приложение моделирования устройств из этой загрузки для отправки сообщения в центр Интернета вещей.

## <a name="set-up-and-configure-resources"></a>Настройка и Настройка ресурсов

Помимо создания необходимых ресурсов, сценарий Azure CLI также настраивает два маршрута к конечным точкам, которые являются отдельными контейнерами хранилища. Дополнительные сведения о настройке маршрутизации см. в руководстве по [маршрутизации](tutorial-routing.md). После настройки ресурсов используйте [портал Azure](https://portal.azure.com) , чтобы настроить дополнения сообщений для каждой конечной точки, а затем перейдите к шагу проверки.

> [!NOTE]
> Все сообщения направляются в обе конечные точки, но будут расширены только те сообщения, которые передаются в конечную точку с настроенными дополнениями сообщений.
>

Можно использовать приведенный ниже скрипт или открыть скрипт в папке/ресаурцес скачанного репозитория. Ниже приведены действия, которые будет выполнять сценарий.

* Создайте Центр Интернета вещей.
* Создайте учетную запись хранения.
* Создайте два контейнера в учетной записи хранения — один для обогащенных сообщений, а другой для сообщений, которые не являются расширенными.
* Настройте маршрутизацию для двух разных учетных записей хранения.
    * Создайте конечную точку для каждого контейнера учетной записи хранения.
    * Создайте маршрут к каждой из конечных точек контейнера учетной записи хранения.

Некоторым ресурсам необходимо присвоить уникальные имена, включая имя Центра Интернета вещей и имя учетной записи хранения. Чтобы упростить выполнение сценария, к именам ресурсов добавляется случайное буквенно-цифровое значение с именем *рандомвалуе*. Значение randomValue создается один раз в верхней части скрипта и повсеместно добавляется к соответствующим именам ресурсов в пределах скрипта. Если вы не хотите, чтобы это значение было случайным, можно заменить его пустой строкой или определенным значением.

Если вы еще не сделали этого, откройте [окно Cloud Shell для bash.](https://shell.azure.com).. Откройте скрипт в распакованном репозитории, нажмите сочетание клавиш CTRL-A, чтобы выделить его все, а затем CTRL-C, чтобы скопировать его. Кроме того, можно скопировать следующий скрипт CLI или открыть его непосредственно в Cloud Shell. Вставьте скрипт в окно Azure Cloud Shell, щелкнув правой кнопкой мыши командную строку и выбрав команду **Вставить**. Сценарий выполняется по одному оператору за раз. После прекращения выполнения сценария нажмите клавишу **Ввод** , чтобы убедиться, что он выполняет последнюю команду. В следующем блоке кода показан используемый скрипт, с комментариями, объясняющими, что он делает.

Ниже приведены ресурсы, созданные сценарием. **Обогащенный** — это то, что ресурс предназначен для сообщений с дополнениями. **Оригинал** означает, что ресурс предназначен для нерасширенных сообщений.

| Название | Значение |
|-----|-----|
| resourceGroup | контосоресаурцесмсжен |
| имя контейнера | Исходный текст  |
| имя контейнера | обогащенные  |
| Имя устройства IoT | Contoso-Test-device |
| Имя центра Интернета вещей | контосотессубмсжен |
| Имя учетной записи хранения | контосостораже |
| Имя конечной точки 1 | контососторажеендпоинторигинал |
| Имя конечной точки 2 | контососторажеендпоинтенричед|
| Имя маршрута 1 | контососторажераутеоригинал |
| Имя маршрута 2 | контососторажераутинричед |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-cli-iot-ext

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One points to container1 in the storage account
#   and includes all messages.
# The other points to container2 in the same storage account
#   and only includes enriched messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for container 1, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for container 2, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are not enriched.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

На этом этапе все ресурсы настроены и маршрутизация настроена. Вы можете просмотреть конфигурацию маршрутизации сообщений на портале и настроить сообщения для сообщений, поступающих в более подробные сведения в контейнере хранилища.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Просмотр маршрутизации и настройка обогащений сообщений

1. Перейдите в центр Интернета вещей, выбрав **группы ресурсов**, а затем выберите группу ресурсов, настроенную для этого учебника (**ContosoResources_MsgEn**). Найдите в списке центр Интернета вещей и выберите его. Выберите *маршрутизацию сообщений** для центра Интернета вещей.

   ![Выбор маршрутизации сообщений](./media/tutorial-message-enrichments/select-iot-hub.png)

   Панель маршрутизации сообщений содержит три вкладки: **маршруты**, **пользовательские конечные точки**и **обогащенные сообщения**. На первых двух вкладках можно просмотреть конфигурацию, настроенную сценарием. Используйте третью вкладку для добавления обогащений сообщений. Давайте добавим сообщения в конечную точку для контейнера хранилища с именем **обогащенный**. Введите имя и значение, а затем выберите конечную точку **контососторажеендпоинтенричед** из раскрывающегося списка. Ниже приведен пример настройки обогащения, добавляющего имя центра Интернета вещей в сообщение:

   ![Добавить первое дополнение](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Добавьте эти значения в список для конечной точки Контососторажеендпоинтенричед.

   | Название | Значение | Конечная точка (раскрывающийся список) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |
   | девицелокатион | $twin. Tags. Location | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |
   |Идентификатор | 6ce345b8-1e4a-411e-9398-d34587459a3a | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |

   > [!NOTE]
   > Если устройство не имеет двойника, значение, которое вы поместите здесь, будет помечено как строка для значения в дополнениех сообщения. Чтобы просмотреть сведения о двойникае устройства, перейдите в центр на портале, выберите **устройства IOT**, выберите устройство, а затем в верхней части страницы выберите **двойника устройства** .
   >
   > Вы можете изменить сведения двойника, чтобы добавить теги (например, расположение), и при необходимости задать для них определенное значение. См. [общие сведения о двойниках устройств и их использовании в Центре Интернета вещей](iot-hub-devguide-device-twins.md).

3. По завершении ваша панель должна выглядеть примерно так, как показано на следующем рисунке:

   ![Таблица со всеми дополнениями](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Нажмите кнопку **Применить** , чтобы сохранить изменения.

## <a name="send-messages-to-the-iot-hub"></a>Отправка сообщений в центр Интернета вещей

Теперь, когда для конечной точки настроены дополнения сообщений, запустите приложение имитации устройства, чтобы отправить сообщения в центр Интернета вещей. Для концентратора настроены параметры, которые выполняют следующие действия.

* Сообщения, направляемые в конечную точку хранилища Контососторажеендпоинторигинал, не будут расширены и будут храниться в контейнере `original`хранилища.

* Сообщения, направляемые в конечную точку хранилища Контососторажеендпоинтенричед, будут дополнены и сохранены в контейнере `enriched`хранилища.

Приложение имитации устройства является одним из приложений в распакованном скачивании. Приложение отправляет сообщения для каждого из различных методов маршрутизации сообщений в [руководстве по маршрутизации](tutorial-routing.md). Сюда входит служба хранилища Azure.

Дважды щелкните файл решения (IoT_SimulatedDevice.sln), чтобы открыть код в Visual Studio, а затем откройте файл Program.cs. Замените `{your hub name}` именем центра Интернета вещей. В качестве имени узла центра Интернета вещей используется формат **{имя центра}. Azure-Devices.NET**. В этом руководстве имя узла концентратора — **ContosoTestHubMsgEn.Azure-Devices.NET**. Затем замените `{device key}` ключ устройства, сохраненный ранее при выполнении сценария для создания ресурсов.

Если у вас нет ключа устройства, его можно получить на портале. После входа перейдите в раздел **группы ресурсов**, выберите группу ресурсов, а затем выберите центр Интернета вещей. Просмотрите раздел **устройства IOT** для тестового устройства и выберите устройство. Щелкните значок копирования рядом с полем **первичный ключ** , чтобы скопировать его в буфер обмена.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Запуск и тестирование

Запустите консольное приложение. Подождите несколько минут. Отправляемые сообщения отображаются на экране консоли приложения.

Приложение отправляет новое сообщение с устройства в облако к Центру IoT каждую секунду. Сообщение содержит JSON-сериализованный объект с идентификатором устройства, температурой, влажностью и уровнем сообщений, значение которого по умолчанию `normal`. Он случайным образом назначает уровень `critical` или `storage`, в результате чего сообщение направляется в учетную запись хранения или в конечную точку по умолчанию. Сообщения, отправленные в **обогащенный** контейнер в учетной записи хранения, будут дополнены.

После отправки нескольких сообщений хранилища просмотрите данные.

1. Выберите **группы ресурсов**, затем найдите группу ресурсов (контосоресаурцесмсжен) и выберите ее.

2. Выберите учетную запись хранения (контосостораже). Затем выберите **Обозреватель службы хранилища (Предварительная версия)** на панели выбора слева.

   ![Выбор обозревателя службы хранилища](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Выберите **контейнеры больших двоичных объектов** , чтобы просмотреть два контейнера, которые можно использовать.

   ![Просмотр контейнеров в учетной записи хранения](./media/tutorial-message-enrichments/show-blob-containers.png)

Сообщения в контейнере с именем **обогащенные** имеют обогащенные сообщения, содержащиеся в сообщениях. Сообщения в контейнере с именем **Original** будут иметь необработанные сообщения без дополнений. Выполните детализацию одного из контейнеров до конца и откройте самый последний файл сообщения, а затем сделайте то же самое для другого контейнера, чтобы убедиться в отсутствии дополнений к сообщениям в этом контейнере.

При просмотре сообщений, которые были дополнены, вы увидите "Мой центр Интернета вещей" с именем концентратора, а также расположением и ИДЕНТИФИКАТОРом клиента следующим образом:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

И это необогащенное сообщение. "" Центр Интернета вещей "," девицелокатион "и" customerID "не отображаются здесь, так как эта конечная точка не имеет обогащений.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите удалить все ресурсы, созданные в этом руководстве, удалите группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление центра Интернета вещей, учетной записи хранения и самой группы ресурсов.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Очистка ресурсов с помощью Azure CLI

Чтобы удалить группу ресурсов, используйте команду [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Значение `$resourceGroup` было присвоено **ContosoResources** в начале работы с этим руководством.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы настроили и протестировали Добавление дополнений сообщений в сообщения центра Интернета вещей, выполнив следующие действия.

**Использование дополнений сообщений центра Интернета вещей**
> [!div class="checklist"]
> * С помощью Azure CLI создайте ресурсы — центр Интернета вещей, учетную запись хранения с двумя енендпоинтс и конфигурацию маршрутизации.
> * Используйте портал Azure для настройки обогащений сообщений.
> * Запустите приложение, моделирующее устройство Интернета вещей, отправляющее сообщение в центр.
> * Просмотр результатов и проверка правильности работы сообщений.

Дополнительные сведения о обогащении сообщений см. в обзоре дополнений [сообщений](iot-hub-message-enrichments-overview.md).

Дополнительные сведения о маршрутизации сообщений см. в следующих статьях:

* [Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](iot-hub-devguide-messages-d2c.md)

* [Учебник. Маршрутизация центра Интернета вещей](tutorial-routing.md)