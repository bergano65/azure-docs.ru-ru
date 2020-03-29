---
title: Учебник - Используйте обогащение сообщений Azure IoT концентратором
description: Учебник, показывающий, как использовать обогащение сообщений для сообщений Azure IoT Концентратор
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: c812e00699cd8f8cfbaf32feea1b43866ffb0990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78674354"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Учебник: Используйте обогащение сообщений Azure IoT концентратором

*Обогащение сообщений* описывает способность Azure IoT Hub *штамповать* сообщения дополнительной информацией перед отправкой сообщений в назначенную конечную точку. Одной из причин использования обогащения сообщений является включение данных, которые могут быть использованы для упрощения обработки ниже по течению. Например, обогащение телеметрических сообщений устройства с помощью тегов-близнецов устройства может снизить нагрузку на клиентов, чтобы сделать устройство twin API вызывает эту информацию. Для получения дополнительной информации смотрите [Обзор обогащения сообщений](iot-hub-message-enrichments-overview.md).

В этом уроке вы увидите два способа создания и настройки ресурсов, необходимых для тестирования обогащения сообщений для концентратора IoT. Ресурсы включают одну учетную запись хранения с двумя контейнерами для хранения. Один контейнер содержит обогащенные сообщения, а другой — исходные сообщения. Также включен концентратор IoT для получения сообщений и их направления в соответствующий контейнер для хранения в зависимости от того, обогащены они или нет.

* Первый метод заключается в использовании Azure CLI для создания ресурсов и настройки реуктора сообщения. Затем вы определяете обогащение вручную с помощью [портала Azure](https://portal.azure.com).

* Второй метод заключается в использовании шаблона менеджера ресурсов Azure для создания ресурсов *и* конфигураций для разгрома сообщений и обогащения сообщений.

После завершения конфигураций для разгрома сообщений и обогащения сообщений используется приложение для отправки сообщений в концентратор IoT. Затем концентратор направляет их в оба контейнера для хранения. Обогащаются только сообщения, отправленные в конечную точку для **обогащенного** контейнера для хранения.

Вот задачи, которые вы выполняете, чтобы завершить этот учебник:

**Используйте обогащение сообщений Концентратора IoT**
> [!div class="checklist"]
> * Первый метод: Создание ресурсов и настройка реуктора сообщения с помощью Azure CLI. Налаживать обогащение сообщений вручную с помощью [портала Azure.](https://portal.azure.com)
> * Второй метод: Создание ресурсов и настройка разгрома сообщений и обогащения сообщений с помощью шаблона «Менеджер ресурсов». 
> * Запустите приложение, имитирующее устройство IoT, отправляющее сообщения в концентратор.
> * Просмотр результатов и проверка того, что обогащение сообщений работает в режиме ожидания.

## <a name="prerequisites"></a>Предварительные требования

* У вас должна быть подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Установка [визуальной студии](https://www.visualstudio.com/).

* Убедитесь, что в брандмауэре открыт порт 8883. Пример устройства в этом руководстве использует протокол MQTT, который передает данные через порт 8883. В некоторых корпоративных и академических сетях этот порт может быть заблокирован. Дополнительные сведения и способы устранения этой проблемы см. в разделе о [подключении к Центру Интернета вещей по протоколу MQTT](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Изыскать репозиторий образцов IoT C

Загрузите [образцы IoT C с](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) GitHub и расстегните их. В этом репозитории есть несколько приложений, скриптов и шаблонов менеджера ресурсов. Те, которые будут использоваться для этого учебника являются следующими:

* Для ручного метода есть скрипт CLI, который используется для создания ресурсов. Этот скрипт находится в /Azure-iot-образцы-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/ресурсы/iothub_msgenrichment_cli.azcli. Этот скрипт создает ресурсы и настраивает на разгром сообщений. После запуска этого скрипта создайте обогащение сообщений вручную, используя [портал Azure.](https://portal.azure.com)
* Для автоматизированного метода есть шаблон менеджера ресурсов Azure. Шаблон находится в /Azure-iot-образцы-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Этот шаблон создает ресурсы, настраивает разгром сообщения, а затем настраивает обогащение сообщений.
* Третьим приложением, которое используется, является приложение моделирования устройств, которое используется для отправки сообщений в концентратор IoT и тестирования обогащения сообщений.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Ручно настраиваемый и настраиваемый с помощью Azure CLI

Помимо создания необходимых ресурсов, скрипт Azure CLI также настраивает два маршрута на конечные точки, которые являются отдельными контейнерами для хранения. Для получения дополнительной информации о том, как [Routing tutorial](tutorial-routing.md)настроить разгром сообщения, см. После настройки ресурсов используйте [портал Azure](https://portal.azure.com) для настройки обогащения сообщений для каждой конечной точки. Затем перейдите к этапу тестирования.

> [!NOTE]
> Все сообщения направляются в обе конечные точки, но обогащаются только сообщения, идущие в конечную точку с настроенными обогащениями сообщений.
>

Вы можете использовать следующий сценарий, или вы можете открыть сценарий в папке /ресурсов загруженного репозитория. Скрипт выполняет следующие задачи:

* Создайте Центр Интернета вещей.
* Создайте учетную запись хранения.
* Создайте два контейнера в учетной записи хранилища. Один контейнер предназначен для обогащенных сообщений, а другой контейнер предназначен для сообщений, которые не обогащаются.
* Настройка routing для двух различных учетных записей хранения:
    * Создайте конечную точку для каждого контейнера с учетной записью хранения.
    * Создайте маршрут к каждой из конечных точек контейнера хранилища.

Существует несколько имен ресурсов, которые должны быть глобально уникальными, например, имя концентратора IoT и имя учетной записи хранилища. Чтобы облегчить работу скрипта, эти имена ресурсов применяются случайным алфавитным значением, называемым *randomValue.* Случайное значение генерируется один раз в верхней части скрипта. Он придатим к именам ресурсов по мере необходимости на протяжении всего скрипта. Если вы не хотите, чтобы значение было случайным, его можно установить на пустую строку или на определенное значение.

Если вы еще этого не сделали, откройте окно Azure [Cloud Shell](https://shell.azure.com) и убедитесь, что оно настроено на Bash. Откройте скрипт в распаванном репозитории, выберите Ctrl-A, чтобы выбрать все это, а затем выберите Ctrl'C, чтобы скопировать его. Кроме того, вы можете скопировать следующий скрипт CLI или открыть его непосредственно в облачной оболочке. Вставьте скрипт в окне облачной оболочки, нажав правой щелчком командной строки и выбрав **пасту.** Скрипт выполняет одно утверждение одновременно. После того, как скрипт перестанет работать, выберите **Enter,** чтобы убедиться, что он выполняет последнюю команду. Следующий блок кода показывает используемый скрипт с комментариями, объясняющие, что он делает.

Вот ресурсы, созданные скриптом. *Обогащенный* означает, что ресурс предназначен для сообщений с обогащением. *Оригинал* означает, что ресурс предназначен для сообщений, которые не обогащаются.

| name | Значение |
|-----|-----|
| resourceGroup | ContosoРесурсЫMsgEn |
| название контейнера | оригинальный  |
| название контейнера | Обогащенный  |
| Имя устройства IoT | Контосо-Тест-Устройство |
| Имя Центра Интернета вещей | ContosoTestHubMsgEn |
| Имя учетной записи хранилища | contosostorage |
| имя конечных точек 1 | ContosoStorageEndpointOriginal |
| имя конечных точек 2 | ContosoStorageEndpointОорж|
| Название маршрута 1 | ContosoStorageRouteOriginal |
| Маршрут Имя 2 | ContosoStorageRouteОбогал |

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
az extension add --name azure-iot

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
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

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

# This is the endpoint for the first container, for endpoint messages that are not enriched.
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

# This is the endpoint for the second container, for endpoint messages that are enriched.
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

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

На этом этапе все ресурсы настроены и настроена наигнетаемый контур сообщения. Можно просмотреть конфигурацию разгрома сообщений на портале и настроить обогащение сообщений для сообщений, отправляющихся в **обогащенный** контейнер для хранения.

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Ручная настройка обогащения сообщений с помощью портала Azure

1. Перейдите к своему концентратору IoT, выбрав **группы ресурсов.** Затем выберите группу ресурсов, созданную для этого учебника **(ContosoResourcesMsgEn**). Найдите концентратор IoT в списке и выберите его. Выберите **routing сообщений** для концентратора IoT.

   ![Выберите разгром сообщений](./media/tutorial-message-enrichments/select-iot-hub.png)

   Панель маршрутизатора сообщения имеет три вкладки с маркировкой **Routes,** **пользовательские конечные точки**и **сообщения Enrich.** Просмотрите первые две вкладки, чтобы увидеть конфигурацию, настроенную скриптом. Используйте третью вкладку для добавления обогащения сообщений. Давайте обогатим сообщения, идущие в конечную точку для контейнера для хранения, называемого **обогащенным.** Заполните имя и значение, а затем выберите конечную точку **ContosoStorageEndpoint, обогащенную** из списка выпадающих. Вот пример того, как настроить обогащение, которое добавляет имя концентратора IoT в сообщение:

   ![Добавить первое обогащение](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Добавьте эти значения в список для конечной точки ContosoStorageEndpoint.

   | Ключ | Значение | Конечная точка (список выпадающих) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageКонтейнеры > ContosoStorageEndpointОосили |
   | Место устройства | $twin.tags.location | AzureStorageКонтейнеры > ContosoStorageEndpointОосили |
   |Customerid | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageКонтейнеры > ContosoStorageEndpointОосили |

   > [!NOTE]
   > Если ваше устройство не имеет близнеца, значение, которое вы положили здесь, будет штампваемым в виде строки для значения в обогащении сообщений. Чтобы увидеть информацию о двойном устройстве, перейдите в концентратор на портале и выберите **устройства IoT.** Выберите устройство, а затем выберите **устройство близнеца** в верхней части страницы.
   >
   > Вы можете отсеивать информацию о близнецах, чтобы добавить теги, такие как местоположение, и установить ее на определенное значение. См. [общие сведения о двойниках устройств и их использовании в Центре Интернета вещей](iot-hub-devguide-device-twins.md).

3. Когда вы закончите, ваше стекло должно выглядеть так же, как это изображение:

   ![Таблица со всеми добавленными обогащениями](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Выберите **применить,** чтобы сохранить изменения. Перейти в раздел [обогащения сообщений Test.](#test-message-enrichments)

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Создание и настройка с помощью шаблона «Менеджер ресурсов»
Шаблон менеджера ресурсов можно использовать для создания и настройки ресурсов, разгрома сообщений и обогащения сообщений.

1. Войдите на портал Azure. Выберите **и создайте ресурс** для создания окна поиска. Введите *развертывание шаблонов*и ищите его. В панели результатов выберите **развертывание шаблонов (развертывание с помощью пользовательского шаблона).**

   ![Развертывание шаблонов на портале Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Выберите **Создать** в панели **развертывания шаблона.**

1. В панели **пользовательского развертывания** выберите **«Создайте свой шаблон в редакторе».**

1. В панели **шаблона Edit** выберите **файл Load.** Отослана Windows Explorer. Найдите файл **template_messageenrichments.json** в незастегнутом файле репо в **/iot-hub/Tutorials/Routing/SimulatedDevice/resources.** 

   ![Выберите шаблон из локальной машины](./media/tutorial-message-enrichments/template-select.png)

1. Выберите **Open** для загрузки файла шаблона из локальной машины. Он загружается и появляется в панели для отсылки.

   Этот шаблон настроен на использование уникального в глобальном масштабе имени концентратора IoT и имени учетной записи хранилища, добавляя случайное значение в конец имен по умолчанию, так что вы можете использовать шаблон без внесения каких-либо изменений в него.

   Вот ресурсы, созданные путем загрузки шаблона. **Обогащенный** означает, что ресурс предназначен для сообщений с обогащением. **Оригинал** означает, что ресурс предназначен для сообщений, которые не обогащаются. Это те же значения, что и в скрипте Azure CLI.

   | name | Значение |
   |-----|-----|
   | resourceGroup | ContosoРесурсЫMsgEn |
   | название контейнера | оригинальный  |
   | название контейнера | Обогащенный  |
   | Имя устройства IoT | Контосо-Тест-Устройство |
   | Имя Центра Интернета вещей | ContosoTestHubMsgEn |
   | Имя учетной записи хранилища | contosostorage |
   | имя конечных точек 1 | ContosoStorageEndpointOriginal |
   | имя конечных точек 2 | ContosoStorageEndpointОорж|
   | Название маршрута 1 | ContosoStorageRouteOriginal |
   | Маршрут Имя 2 | ContosoStorageRouteОбогал |

1. Нажмите кнопку **Сохранить**. Панель **пользовательского развертывания** отображается и отображает все параметры, используемые шаблоном. Единственное поле, необходимое для установки, это **группа ресурсов.** Либо создайте новый, либо выберите один из списка выпадающих.

   Вот верхняя половина панели **пользовательского развертывания.** Вы можете увидеть, где вы заполните группу ресурсов.

   ![Верхняя половина панели пользовательского развертывания](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Вот нижняя половина панели **пользовательского развертывания.** Вы можете увидеть остальные параметры и условия. 

   ![Нижняя половина панели пользовательского развертывания](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Выберите флажок, чтобы согласовать условия. Затем выберите **Покупка,** чтобы продолжить развертывание шаблона.

1. Подождите, пока шаблон будет полностью развернут. Выберите значок колокола в верхней части экрана, чтобы проверить прогресс. Когда она будет закончена, перейти к разделу [обогащения сообщений test.](#test-message-enrichments)

## <a name="test-message-enrichments"></a>Обогащение сообщений тестирования

Для просмотра обогащения сообщений выберите **группы ресурсов.** Затем выберите группу ресурсов, используемую для этого учебника. Выберите концентратор IoT из списка ресурсов и перейдите на **сообщение.** Появляются конфигурация контурирования сообщений и настроенные обогащения.

Теперь, когда обогащение сообщений настроено для конечных точек, запустите приложение «Смоделированное устройство» для отправки сообщений в концентратор IoT. Концентратор был настроен с настройками, которые выполняют следующие задачи:

* Сообщения, отправленные в конечную точку хранения ContosoStorageEndpointOriginal, не будут `original`обогащаться и будут храниться в контейнере для хранения.

* Сообщения, направленные в конечную точку хранения ContosoStorageEndpointEnriched, будут обогащены и сохранены в контейнере `enriched`для хранения.

Приложение "Имитация устройства" является одним из приложений в распаковной загрузке. Приложение отправляет сообщения для каждого из различных методов передачи сообщений в учебнике по [реучировке,](tutorial-routing.md)который включает хранилище Azure.

Дважды щелкните файл решения **IoT_SimulatedDevice.sln,** чтобы открыть код в Visual Studio, а затем открыть **Program.cs.** Заменить название концентратора IoT для маркера. `{your hub name}` Формат имени узла концентратора IoT — **это «ваше имя концентратора devices.net».** Для этого учебника имя узла концентратора ContosoTestHubMsgEn.azure-devices.net. Затем замените ключ устройства, сохраненный ранее при проветре `{your device key}`скрипта, для создания ресурсов для маркера.

Если у вас нет ключа устройства, вы можете получить его с портала. После вступления в систему перейдите в **группы ресурсов,** выберите группу ресурсов, а затем выберите концентратор IoT. Загляните под **устройства IoT** для тестового устройства и выберите устройство. Выберите значок копии рядом с **ключом Primary,** чтобы скопировать его в буфер обмена.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Запуск и тестирование

Запустите консольное приложение в течение нескольких минут. Отправляемые сообщения отображаются на экране консоли приложения.

Приложение отправляет новое сообщение с устройства в облако к Центру IoT каждую секунду. Сообщение содержит JSON-сериализованный объект с идентификатором устройства, температурой, влажностью и уровнем сообщений, значение которого по умолчанию `normal`. Он случайным образом назначает `critical` уровень или `storage`, что приводит к сообщению, которое будет направлено на учетную запись хранилища или в конечную точку по умолчанию. Сообщения, отправленные в **обогащенный** контейнер в учетной записи хранилища, будут обогащены.

После отправки нескольких сообщений хранилища просмотрите данные.

1. Выберите **группы ресурсов.** Найдите свою группу ресурсов, **ContosoResourcesMsgEn,** и выберите ее.

2. Выберите учетную запись хранения, которая является **contosostorage.** Затем выберите **Исследователь хранения (предварительный просмотр)** в левом стеле.

   ![Выберите проводник хранения](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Выберите **BLOB CONTAINERS,** чтобы увидеть два контейнера, которые могут быть использованы.

   ![Посмотреть контейнеры в учетной записи хранилища](./media/tutorial-message-enrichments/show-blob-containers.png)

Сообщения в контейнере, называемом **обогащенным,** имеют обогащение сообщений, включенных в сообщения. Сообщения в контейнере, называемом **оригиналом,** имеют необработанные сообщения без обогащения. Просверлите в один из контейнеров, пока не доберетесь до дна, и откройте самый последний файл сообщений. Затем сделайте то же самое для другого контейнера, чтобы убедиться, что в этом контейнере нет обогащения, добавленного в сообщения.

Когда вы смотрите на сообщения, которые были обогащены, вы должны увидеть "мой IoT концентратор" с именем концентратора и местоположение и идентификатор клиента, как это:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Вот необогащенное сообщение. Обратите внимание, что "мой концентратор IoT", "устройство" и "customerID" здесь не отображаются, потому что эти поля добавляются обогащением. Эта конечная точка не имеет обогащения.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этом учебнике, удалите группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление центра Интернета вещей, учетной записи хранения и самой группы ресурсов.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Очистка ресурсов с помощью Azure CLI

Чтобы удалить группу ресурсов, используйте команду [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Напомним, что `$resourceGroup` был установлен на **ContosoResourcesMsgEn** в начале этого учебника.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом уроке вы настроили и протестировали обогащение сообщений в сообщения IoT Hub с помощью следующих шагов:

**Используйте обогащение сообщений Концентратора IoT**
> [!div class="checklist"]
> * Первый метод: Создание ресурсов и настройка реуктора сообщения с помощью Azure CLI. Налаживать обогащение сообщений вручную с помощью [портала Azure.](https://portal.azure.com)
> * Второй метод: Создание ресурсов и настройка разгрома сообщений и обогащения сообщений с помощью шаблона Azure Resource Manager.
> * Запустите приложение, имитирующее устройство IoT, отправляющее сообщения в концентратор.
> * Просмотр результатов и проверка того, что обогащение сообщений работает в режиме ожидания.

Для получения дополнительной информации об обогащении [сообщений](iot-hub-message-enrichments-overview.md)см.

Для получения дополнительной информации о направлении сообщений см.

* [Используйте разгром сообщений IoT Hub для отправки сообщений от устройства в облако в различные конечные точки](iot-hub-devguide-messages-d2c.md)
* [Учебник: Разгром концентратора IoT](tutorial-routing.md)
