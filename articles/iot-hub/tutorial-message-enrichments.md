---
title: Руководство. Использование функции усовершенствования сообщений центра Интернета вещей Azure
description: Узнать, как использовать усовершенствования сообщений для сообщений центра Интернета вещей Azure
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: e4906bf9f2aead69c315ddb7b2e3b10489378d87
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259079"
---
# <a name="tutorial-using-azure-iot-hub-message-enrichments-preview"></a>Руководство по С помощью усовершенствования сообщений центра Интернета вещей Azure (Предварительная версия)

*Усовершенствования сообщений* — это способность в центре Интернета вещей *метка* сообщения с дополнительной информацией, прежде чем сообщения отправляются к указанной конечной точке. Одна из причин для использования усовершенствования сообщение — для включения данных, который может использоваться для упрощения последующей обработки. Например обогащение сообщений телеметрии устройств с тегом двойника устройства можно уменьшить нагрузку на клиентов совершить двойника устройства, вызовы API для получения этих сведений. Дополнительные сведения см. в разделе [обзором усовершенствования сообщение](iot-hub-message-enrichments-overview.md).

В этом руководстве используется Azure CLI для настройки ресурсов, включая две конечные точки, которые указывают на двух разных хранилища контейнеры: **насыщенные** и **исходного**. После этого использовать [портала Azure](https://portal.azure.com) для настройки сообщений усовершенствования для применения только к сообщениям, отправленным в конечную точку с **насыщенные** контейнер хранилища. Вы отправите сообщения в центр Интернета вещей, которые направляются в контейнеры хранилища. Только сообщения, отправленные в конечную точку для **насыщенные** будет добавить контейнер хранилища.

Ниже приведены задачи, необходимо выполнить для завершения этого учебника.

**С помощью центра Интернета вещей сообщения усовершенствования**
> [!div class="checklist"]
> * С помощью Azure CLI, создайте ресурсы — центр Интернета вещей, учетную запись хранения с двумя конечными точками и конфигурации маршрутизации.
> * С помощью портала Azure настроить усовершенствования сообщения.
> * Запустите приложение, которое имитирует устройство отправки сообщений в центр Интернета вещей.
> * Просмотрите результаты и убедитесь, что сообщение усовершенствования работают надлежащим образом.

## <a name="prerequisites"></a>Технические условия

* У вас должна быть подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

* Установить [Visual Studio](https://www.visualstudio.com/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-sample-code"></a>Получить пример кода

Скачайте [симулятор устройств Интернета вещей](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) и распакуйте его. Этот репозиторий содержит несколько приложений, включая то, которое будет использоваться для отправки сообщений в центр Интернета вещей.

Загружаемый файл также содержит скрипт для создания ресурсов, используемых для тестирования усовершенствования сообщения. Сценарий находится в /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli. Сейчас можно взгляде на сценарий и использовать его. Также можно скопировать сценарий прямо из соответствующей статьи.

Когда будете готовы к тестированию, будет использовать приложение имитации устройства из этой загрузки для отправки сообщения в центр Интернета вещей.

## <a name="set-up-and-configure-resources"></a>Установка и настройка ресурсов

В дополнение к созданию необходимых ресурсов, этот скрипт Azure CLI также настраивает два маршрута к конечным точкам, которые являются контейнерами отдельное хранилище. Дополнительные сведения о настройке маршрутизации см. в разделе [маршрутизации руководстве](tutorial-routing.md). После настройки ресурсов, использовании [портала Azure](https://portal.azure.com) настраивать усовершенствования сообщения для каждой конечной точки и затем перейдите к шагу тестирования.

> [!NOTE]
> Все сообщения направляются в обе конечные точки, но будет полезно только сообщения, отправляемые в конечную точку с усовершенствования настроенного сообщения.
>

Можно использовать приведенный ниже сценарий или открыть сценарий в папке /resources Скачанный репозитория. Ниже приведены шаги, которые будет выполнять сценарий.

* Создайте Центр Интернета вещей.
* Создайте учетную запись хранения.
* Создайте два контейнера в учетной записи хранения — один для Обогащенные сообщений и один для сообщений, которые не добавляются.
* Настройка маршрутизации для двух разных учетных записей хранения.
    * Создайте конечную точку для каждого контейнера учетной записи хранения.
    * Создайте маршрут к каждой из конечных точек контейнера учетной записи хранения.

Некоторым ресурсам необходимо присвоить уникальные имена, включая имя Центра Интернета вещей и имя учетной записи хранения. Чтобы сделать работу с скрипт, имена этих ресурсов добавляются со случайным значением буквенно-цифровые вызывается *randomValue*. Значение randomValue создается один раз в верхней части скрипта и повсеместно добавляется к соответствующим именам ресурсов в пределах скрипта. Если вы не хотите, чтобы это значение было случайным, можно заменить его пустой строкой или определенным значением.

Если вы еще не сделали, откройте [окно Cloud Shell для Bash.](https://shell.azure.com). Откройте скрипт в распакованной репозитории, используйте Ctrl + A, чтобы выбрать все его, а затем сочетание клавиш Ctrl + C, чтобы скопировать его. Кроме того можно скопировать следующий сценарий CLI или открыть его непосредственно в cloud shell. Вставьте скрипт в окне оболочки облака Azure, щелкнув правой кнопкой мыши, в командной строке и выбрав **вставить**. Сценарий выполняется одной инструкции одновременно. После завершения скрипта, выберите **ввод** чтобы убедиться в том, он выполняет последнюю команду. Следующий блок кода приведен скрипт, который используется с комментариями о том, что он делает.

Ниже приведены ресурсы, созданные с помощью скрипта. **Насыщенные** означает, что ресурс для сообщения с усовершенствования. **Исходное** означает, что ресурс для сообщений, которые не добавляются.

| ИМЯ | Value |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| Имя контейнера | Исходный текст  |
| Имя контейнера | в состав входит  |
| Имя устройства Интернета вещей | Contoso теста Device |
| Имя Центра Интернета вещей | ContosoTestHubMsgEn |
| Имя учетной записи хранения | contosostorage |
| Конечная точка 1 имя | ContosoStorageEndpointOriginal |
| Конечная точка 2 имя | ContosoStorageEndpointEnriched|
| 1 имя маршрута | ContosoStorageRouteOriginal |
| маршрут 2 имя | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing rules.
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

На этом этапе ресурсы не будут все готово, и настроена маршрутизация. Можно просмотреть конфигурацию маршрутизации сообщений на портале и настроить усовершенствования сообщения для сообщения, отправляемые **насыщенные** контейнер хранилища.

### <a name="view-routing-and-configure-the-message-enrichments"></a>Просмотреть маршрутизации и настроить усовершенствования сообщения

1. Перейдите в центр Интернета вещей, выбрав **групп ресурсов**, затем выберите группу ресурсов, Настройка в этом руководстве (**ContosoResources_MsgEn**). Найти центр Интернета вещей в списке и выберите его. Выберите *маршрутизации сообщений** для центра Интернета вещей.

   ![Выберите маршрутизации сообщений](./media/tutorial-message-enrichments/select-iot-hub.png)

   Сообщения маршрутизации панель содержит три вкладки — **маршруты**, **настраиваемых конечных точек**, и **дополнять сообщения**. Вы можете просмотреть первые две вкладки для просмотра конфигурации, настройка с помощью скрипта. Третья вкладка используется для добавления сообщения усовершенствования. Давайте обогащения сообщения, отправляемые в конечную точку для контейнера хранилища с именем **насыщенные**. Введите имя и значение, а затем выберите конечную точку **ContosoStorageEndpointEnriched** из раскрывающегося списка. Вот пример того, обогащение, который добавляет имя центра Интернета вещей в сообщение:

   ![Добавление первого обогащение](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Добавьте в список для конечной точки ContosoStorageEndpointEnriched эти значения.

   | ИМЯ | Value | Конечная точка (раскрывающийся список) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | Расположение устройства | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |Идентификатор клиента | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Если устройство не имеет двойника, значение, которое необходимо поместить в здесь, будут помечены как строку для значения в усовершенствования сообщения. Чтобы просмотреть устройства двойников, сведения, перейдите к центру на портале, затем выберите **устройств Интернета вещей**, выберите устройство, а затем выберите **двойника устройства** в верхней части страницы.
   >
   > Можно изменить сведения о двойниках, добавление тегов (например, расположение) и присвоить ему конкретное значение, если вы хотите. См. [общие сведения о двойниках устройств и их использовании в Центре Интернета вещей](iot-hub-devguide-device-twins.md).

3. Когда вы закончите, на панели должен выглядеть этот образ:

   ![Таблица с все усовершенствования добавлен](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Выберите **применить** для сохранения изменений.

## <a name="send-messages-to-the-iot-hub"></a>Отправлять сообщения в центр Интернета вещей

Теперь, когда сообщение усовершенствования настроены для конечной точки, запуск приложения Simulated Device для отправки сообщений в центр Интернета вещей. Концентратор настроен с правилами, которые выполняют следующее:

* Сообщения, отправляемые в конечную точку хранилища ContosoStorageEndpointOriginal не быть дополнены пользователем и будут храниться в контейнере хранилища `original`.

* Сообщения, отправляемые в конечную точку хранилища ContosoStorageEndpointEnriched будет дополненные и хранятся в контейнере хранилища `enriched`.

Приложение имитации устройства является одним из приложений в распакованной загрузки. Приложение отправляет сообщения для каждого из методов маршрутизации сообщений в [маршрутизации руководстве](tutorial-routing.md); Сюда входят службы хранилища Azure.

Дважды щелкните файл решения (IoT_SimulatedDevice.sln), чтобы открыть код в Visual Studio, а затем откройте файл Program.cs. Замените `{your hub name}` имя центра Интернета вещей. Формат имени узла центра Интернета вещей **{имя вашего центра} .azure-devices.net**. Для этого учебника, — на имя узла центра **ContosoTestHubMsgEn.azure-devices.net**. Затем замените `{device key}` ключом устройства, сохраненный ранее при выполнении сценарий для создания ресурсов.

Если у вас нет ключа устройства, его можно получить на портале. После входа в систему, перейдите к **групп ресурсов**, выберите свою группу ресурсов, а затем выберите центр Интернета вещей. Перейдите в раздел **устройств Интернета вещей** тестового устройства и выберите нужное устройство. Щелкните значок копирования рядом с полем **первичный ключ** чтобы скопировать его в буфер обмена.

   ```csharp
        static string myDeviceId = "contoso-test-device";
        static string iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        static string deviceKey = "{your device key here}";
   ```

## <a name="run-and-test"></a>Запуск и тестирование

Запустите консольное приложение. Подождите несколько минут. Сообщения, которые отправляются отображаются на экране консоли приложения.

Приложение отправляет новое сообщение с устройства в облако к Центру IoT каждую секунду. Сообщение содержит JSON-сериализованный объект с идентификатором устройства, температурой, влажностью и уровнем сообщений, значение которого по умолчанию `normal`. Он случайным образом назначает уровень `critical` или `storage`, вызывающем это сообщение будет направляться в учетную запись хранения или конечной точке по умолчанию. Сообщения, отправленные **насыщенные** будет добавить контейнер в учетной записи хранения.

После отправки нескольких сообщений хранилища, просмотрите данные.

1. Выберите **групп ресурсов**, а затем найдите группу ресурсов (ContosoResourcesMsgEn) и выберите его.

2. Выберите свою учетную запись хранения (contosostorage). Затем выберите **обозревателя хранилищ (Предварительная версия)** из панели выбора слева.

   ![Выберите обозреватель службы хранилища](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Выберите **КОНТЕЙНЕРЫ больших двоичных ОБЪЕКТОВ** Чтобы просмотреть два контейнеры, которые могут использоваться.

   ![Контейнеры в учетной записи хранения см. в разделе](./media/tutorial-message-enrichments/show-blob-containers.png)

Вызывается для сообщения в контейнере **насыщенные** имеют усовершенствования сообщения, включается в сообщения. Вызывается для сообщения в контейнере **исходного** необработанные сообщения с помощью нет усовершенствования. Детализации в один из контейнеров, пока не вы занятием откройте последний файл сообщений, а затем используйте его для контейнера, чтобы убедиться в отсутствии, усовершенствования не добавлены к сообщениям в этом контейнере.

Если взглянуть на сообщения, которые были обогатили, вы должны увидеть «My центра Интернета вещей» с имя центра, а также расположение и идентификатор клиента следующим образом:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","device location":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

А вот unenriched сообщение. ««My "вещей», «Местоположение устройства» и «customerID» не отображаются здесь, так как эта конечная точка имеет не усовершенствования.

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

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы настроены и протестированы Добавление усовершенствования сообщений для сообщений центра Интернета вещей, выполнив следующие действия:

**С помощью центра Интернета вещей сообщения усовершенствования**
> [!div class="checklist"]
> * С помощью Azure CLI, создайте ресурсы — центр Интернета вещей, учетную запись хранения с двумя enendpoints и конфигурации маршрутизации.
> * С помощью портала Azure настроить усовершенствования сообщения.
> * Запустите приложение, которое имитирует сообщение отправляющего устройства Интернета вещей к центру.
> * Просмотрите результаты и убедитесь, что сообщение усовершенствования работают надлежащим образом.

Дополнительные сведения о усовершенствования сообщения, см. в разделе [обзором усовершенствования сообщение](iot-hub-message-enrichments-overview.md).

Дополнительные сведения о маршрутизации сообщений см. в статьях:

* [Используйте маршрутизацию сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](iot-hub-devguide-messages-d2c.md)

* [Учебник. Маршрутизацией центра Интернета вещей](tutorial-routing.md)