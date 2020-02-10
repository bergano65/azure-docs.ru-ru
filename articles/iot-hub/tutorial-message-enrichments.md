---
title: Руководство. Использование дополнений сообщений в центре Интернета вещей Azure
description: Руководство, в котором показано, как использовать обогащение сообщений для сообщений центра Интернета вещей Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.openlocfilehash: d16954760d1f2bf11ec5575f912ee32810696590
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/09/2020
ms.locfileid: "77108223"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Учебник. Использование дополнений сообщений в центре Интернета вещей Azure

*Обогащение сообщений* описывает возможность использования центра Интернета вещей Azure для *отметки* сообщений дополнительными сведениями перед отправкой сообщений в указанную конечную точку. Одной из причин использования обогащений сообщений является включение данных, которые могут быть использованы для упрощения обработки в более низком направлении. Например, дополнение сообщений телеметрии устройства с помощью тега двойникаа устройства может снизить нагрузку на клиентов, чтобы сделать вызовы API двойника для этих данных. Дополнительные сведения см. в разделе [Обзор обогащений сообщений](iot-hub-message-enrichments-overview.md).

В этом руководстве вы увидите два способа создания и настройки ресурсов, необходимых для тестирования дополнений сообщений для центра Интернета вещей. Ресурсы включают одну учетную запись хранения с двумя контейнерами хранилища. Один контейнер содержит обогащенные сообщения, а другой контейнер содержит исходные сообщения. Кроме того, в него входит центр Интернета вещей, который получает сообщения и направляет их в соответствующий контейнер хранилища в зависимости от того, являются ли они расширенными.

* Первый способ заключается в использовании Azure CLI для создания ресурсов и настройки маршрутизации сообщений. Затем можно определить обогащения вручную с помощью [портал Azure](https://portal.azure.com).

* Второй способ заключается в использовании шаблона Azure Resource Manager для создания ресурсов *и* конфигураций для маршрутизации сообщений и обогащения сообщений.

После завершения конфигураций маршрутизации сообщений и дополнений сообщений вы используете приложение для отправки сообщений в центр Интернета вещей. Затем концентратор направляет их в оба контейнера хранилища. Дополнены только сообщения, отправленные конечной точке для **обогащенного** контейнера хранилища.

Ниже приведены задачи, которые необходимо выполнить для работы с этим руководством.

**Использование дополнений сообщений центра Интернета вещей**
> [!div class="checklist"]
> * Первый метод: создание ресурсов и настройка маршрутизации сообщений с помощью Azure CLI. Настройте дополнения сообщений вручную с помощью [портал Azure](https://portal.azure.com).
> * Второй способ: создание ресурсов и настройка маршрутизации сообщений и дополнений сообщений с помощью шаблона диспетчер ресурсов. 
> * Запустите приложение, которое имитирует устройство IoT, отправляющее сообщения в центр.
> * Просмотрите результаты и убедитесь, что обогащения сообщений работают правильно.

## <a name="prerequisites"></a>предварительные требования

* У вас должна быть подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.
* Установить [Visual Studio](https://www.visualstudio.com/).

* Убедитесь, что в брандмауэре открыт порт 8883. Пример устройства в этом руководстве использует протокол MQTT, который обменивается данными через порт 8883. Этот порт может быть заблокирован в некоторых корпоративных и образовательных сетевых средах. Дополнительные сведения и способы решения этой проблемы см. [в статье подключение к центру Интернета вещей (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Получение репозитория примеров C# IOT

Скачайте [примеры IOT C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) из GitHub и распакуйте их. Этот репозиторий содержит несколько приложений, скриптов и диспетчер ресурсов шаблонов. Для работы с этим руководством используются следующие объекты:

* Для ручного метода существует сценарий CLI, который используется для создания ресурсов. Этот скрипт находится в/Азуре-ИОТ-самплес-кшарп/ИОТ-ХУБ/туториалс/раутинг/симулатеддевице/ресаурцес/iothub_msgenrichment_cli. azcli. Этот скрипт создает ресурсы и настраивает маршрутизацию сообщений. После выполнения этого скрипта создайте расширения сообщений вручную с помощью [портал Azure](https://portal.azure.com).
* Для автоматизированного метода существует шаблон Azure Resource Manager. Шаблон находится в/Азуре-ИОТ-самплес-кшарп/ИОТ-ХУБ/туториалс/раутинг/симулатеддевице/ресаурцес/template_msgenrichments. JSON. Этот шаблон создает ресурсы, настраивает маршрутизацию сообщений, а затем настраивает дополнения сообщений.
* Третье приложение, которое вы используете, — это приложение моделирования устройств, которое используется для отправки сообщений в центр Интернета вещей и тестирования обогащений сообщений.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Настройка и настройка вручную с помощью Azure CLI

Помимо создания необходимых ресурсов, сценарий Azure CLI также настраивает два маршрута к конечным точкам, которые являются отдельными контейнерами хранилища. Дополнительные сведения о настройке маршрутизации сообщений см. в [руководстве по маршрутизации](tutorial-routing.md). После настройки ресурсов используйте [портал Azure](https://portal.azure.com) для настройки обогащений сообщений для каждой конечной точки. Затем перейдите к шагу тестирование.

> [!NOTE]
> Все сообщения направляются в обе конечные точки, но будут расширены только те сообщения, которые передаются в конечную точку с настроенными дополнениями сообщений.
>

Можно использовать приведенный ниже сценарий или открыть скрипт в папке/ресаурцес скачанного репозитория. Скрипт выполняет следующие действия:

* Создайте Центр Интернета вещей.
* Создайте учетную запись хранения.
* Создайте два контейнера в учетной записи хранения. Один контейнер предназначен для расширенных сообщений, а другой контейнер — для сообщений, которые не являются обогащенными.
* Настройте маршрутизацию для двух разных учетных записей хранения:
    * Создайте конечную точку для каждого контейнера учетной записи хранения.
    * Создайте маршрут к каждой из конечных точек контейнера учетной записи хранения.

Существует несколько имен ресурсов, которые должны быть глобально уникальными, например имя центра Интернета вещей и имя учетной записи хранения. Чтобы упростить выполнение сценария, к именам ресурсов добавляется случайное буквенно-цифровое значение с именем *рандомвалуе*. Случайное значение создается один раз в начале скрипта. Он добавляется к именам ресурсов по мере необходимости во время выполнения скрипта. Если вы не хотите, чтобы значение было случайным, можно присвоить ему пустую строку или определенное значение.

Если вы еще не сделали этого, откройте окно Azure [Cloud Shell](https://shell.azure.com) и убедитесь, что оно имеет значение bash. Откройте скрипт в распакованном репозитории, нажмите клавиши CTRL + A, чтобы выбрать все его, а затем нажмите клавиши CTRL + C, чтобы скопировать его. Кроме того, можно скопировать следующий скрипт CLI или открыть его непосредственно в Cloud Shell. Вставьте скрипт в окно Cloud Shell, щелкнув правой кнопкой мыши командную строку и выбрав команду **Вставить**. Скрипт выполняет одну инструкцию за раз. После прекращения выполнения сценария нажмите клавишу **Ввод** , чтобы убедиться, что он выполняет последнюю команду. В следующем блоке кода показан скрипт, который используется, с комментариями, поясняющими, что он делает.

Ниже приведены ресурсы, созданные сценарием. *Обогащенный* — это то, что ресурс предназначен для сообщений с дополнениями. *Первоначальное* значение означает, что ресурс предназначен для необогащенных сообщений.

| Имя | Значение |
|-----|-----|
| resourceGroup | контосоресаурцесмсжен |
| имя контейнера | оригинальный  |
| имя контейнера | обогащенные  |
| Имя устройства IoT | Contoso-Test-device |
| Имя Центра Интернета вещей | контосотессубмсжен |
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

На этом этапе все ресурсы настроены и маршрутизация сообщений настроена. Вы можете просмотреть конфигурацию маршрутизации сообщений на портале и настроить сообщения для сообщений, поступающих в более подробные сведения **в контейнере хранилища.**

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Вручную настроить обогащение сообщений с помощью портал Azure

1. Перейдите в центр Интернета вещей, выбрав пункт **группы ресурсов**. Затем выберите группу ресурсов, настроенную для этого учебника (**контосоресаурцесмсжен**). Найдите в списке центр Интернета вещей и выберите его. Выберите **маршрутизацию сообщений** для центра Интернета вещей.

   ![Выбор маршрутизации сообщений](./media/tutorial-message-enrichments/select-iot-hub.png)

   Панель маршрутизации сообщений содержит три вкладки: **маршруты**, **пользовательские конечные точки**и **обогащенные сообщения**. Просмотрите первые две вкладки, чтобы просмотреть конфигурацию, настроенную сценарием. Используйте третью вкладку для добавления обогащений сообщений. Давайте добавим сообщения в конечную точку для контейнера хранилища с именем **обогащенный**. Введите имя и значение, а затем выберите конечную точку **контососторажеендпоинтенричед** из раскрывающегося списка. Ниже приведен пример того, как настроить обогащение, добавляющее имя центра Интернета вещей в сообщение:

   ![Добавить первое дополнение](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Добавьте эти значения в список для конечной точки Контососторажеендпоинтенричед.

   | Клавиши | Значение | Конечная точка (раскрывающийся список) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |
   | девицелокатион | $twin. Tags. Location | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |
   |Идентификатор | 6ce345b8-1e4a-411e-9398-d34587459a3a | Азуресторажеконтаинерс > Контососторажеендпоинтенричед |

   > [!NOTE]
   > Если на устройстве нет двойника, значение, которое вы поместите здесь, будет помечено как строка для значения в дополнениех сообщения. Чтобы просмотреть сведения о двойникае устройства, перейдите в центр на портале и выберите **устройства IOT**. Выберите устройство, а затем в верхней части страницы выберите **двойника устройства** .
   >
   > Вы можете изменить сведения двойника, чтобы добавить теги, например расположение, и присвоить ей определенное значение. См. [общие сведения о двойниках устройств и их использовании в Центре Интернета вещей](iot-hub-devguide-device-twins.md).

3. По завершении ваша панель должна выглядеть примерно так, как показано на следующем рисунке:

   ![Таблица со всеми дополнениями](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Нажмите кнопку **Применить** , чтобы сохранить изменения. Перейдите к разделу "дополнительные возможности [тестового сообщения](#test-message-enrichments) ".

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Создание и настройка с помощью шаблона диспетчер ресурсов
Шаблон диспетчер ресурсов можно использовать для создания и настройки ресурсов, маршрутизации сообщений и обогащения сообщений.

1. Войдите на портал Azure. Выберите **+ создать ресурс** , чтобы открыть поле поиска. Введите *Развертывание шаблона*и выполните его поиск. В области результатов выберите **шаблоны развертывания (развернуть с помощью настраиваемого шаблона)** .

   ![Шаблоны развертывания в портал Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. На панели **шаблоны развертывания** выберите **создать** .

1. В области **Настраиваемое развертывание** выберите **создать собственный шаблон в редакторе**.

1. В области **изменение шаблона** выберите **загрузить файл**. Откроется проводник Windows. Нахождение файла **template_messageenrichments. JSON** в распакованном файле репозитория в **/ИОТ-ХУБ/туториалс/раутинг/симулатеддевице/ресаурцес**. 

   ![Выбор шаблона на локальном компьютере](./media/tutorial-message-enrichments/template-select.png)

1. Выберите **Открыть** , чтобы загрузить файл шаблона с локального компьютера. Он загружается и отображается на панели редактирования.

   Этот шаблон настроен для использования глобального уникального имени центра Интернета вещей и имени учетной записи хранения путем добавления случайного значения к концу имен по умолчанию, чтобы можно было использовать шаблон, не внося в него никаких изменений.

   Ниже приведены ресурсы, созданные при загрузке шаблона. **Обогащенный** — это то, что ресурс предназначен для сообщений с дополнениями. **Первоначальное** значение означает, что ресурс предназначен для необогащенных сообщений. Это те же значения, которые используются в скрипте Azure CLI.

   | Имя | Значение |
   |-----|-----|
   | resourceGroup | контосоресаурцесмсжен |
   | имя контейнера | оригинальный  |
   | имя контейнера | обогащенные  |
   | Имя устройства IoT | Contoso-Test-device |
   | Имя Центра Интернета вещей | контосотессубмсжен |
   | Имя учетной записи хранения | контосостораже |
   | Имя конечной точки 1 | контососторажеендпоинторигинал |
   | Имя конечной точки 2 | контососторажеендпоинтенричед|
   | Имя маршрута 1 | контососторажераутеоригинал |
   | Имя маршрута 2 | контососторажераутинричед |

1. Щелкните **Сохранить**. Откроется область **Настраиваемое развертывание** , в которой отображаются все параметры, используемые шаблоном. Единственное поле, которое необходимо задать, — **Группа ресурсов**. Создайте новый или выберите его из раскрывающегося списка.

   Ниже приведена верхняя половина области **настраиваемого развертывания** . Вы можете увидеть, где вы заполнили группу ресурсов.

   ![Верхняя половина области настраиваемого развертывания](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Ниже приведена Нижняя половина области **настраиваемого развертывания** . Можно увидеть остальные параметры и условия. 

   ![Нижняя половина области настраиваемого развертывания](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Установите флажок, чтобы принять условия. Затем выберите **приобрести** , чтобы продолжить развертывание шаблона.

1. Дождитесь завершения полного развертывания шаблона. Щелкните значок колокольчика в верхней части экрана, чтобы проверить ход выполнения. По завершении перейдите к разделу " [тестовые сообщения](#test-message-enrichments) ".

## <a name="test-message-enrichments"></a>Улучшение дополнений сообщений

Чтобы просмотреть дополнения сообщений, выберите **группы ресурсов**. Затем выберите группу ресурсов, которую вы используете для работы с этим руководством. Выберите центр Интернета вещей в списке ресурсов и перейдите в раздел **Обмен сообщениями**. Появится конфигурация маршрутизации сообщений и настроенные дополнения.

Теперь, когда для конечной точки настроены дополнения сообщений, запустите приложение имитации устройства, чтобы отправить сообщения в центр Интернета вещей. Для концентратора настроены параметры, которые выполняют следующие задачи:

* Сообщения, направляемые в конечную точку хранилища Контососторажеендпоинторигинал, не будут расширены и будут храниться в контейнере хранилища `original`.

* Сообщения, направляемые в конечную точку хранилища Контососторажеендпоинтенричед, будут дополнены и сохранены в контейнере хранилища `enriched`.

Приложение имитации устройства является одним из приложений в распакованном скачивании. Приложение отправляет сообщения для каждого из различных методов маршрутизации сообщений в [учебнике по маршрутизации](tutorial-routing.md), который включает службу хранилища Azure.

Дважды щелкните файл решения **IoT_SimulatedDevice. sln** , чтобы открыть код в Visual Studio, а затем откройте **Program.CS**. Замените имя центра IoT `{your hub name}`маркером. В качестве имени узла центра Интернета вещей используется формат **{имя центра}. Azure-Devices.NET**. В этом руководстве имя узла концентратора — ContosoTestHubMsgEn.azure-devices.net. Затем замените ключ устройства, сохраненный ранее при выполнении сценария, чтобы создать ресурсы для маркера `{your device key}`.

Если у вас нет ключа устройства, его можно получить на портале. После входа перейдите в раздел **группы ресурсов**, выберите группу ресурсов, а затем выберите центр Интернета вещей. Просмотрите раздел **устройства IOT** для тестового устройства и выберите устройство. Щелкните значок копирования рядом с полем **первичный ключ** , чтобы скопировать его в буфер обмена.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Запуск и тестирование

Запустите консольное приложение в течение нескольких минут. Отправляемые сообщения отображаются на экране консоли приложения.

Приложение отправляет новое сообщение с устройства в облако к Центру IoT каждую секунду. Сообщение содержит JSON-сериализованный объект с идентификатором устройства, температурой, влажностью и уровнем сообщений, значение которого по умолчанию `normal`. Он случайным образом назначает уровень `critical` или `storage`, что приводит к маршрутизации сообщения в учетную запись хранения или в конечную точку по умолчанию. Сообщения, отправленные в **обогащенный** контейнер в учетной записи хранения, будут дополнены.

После отправки нескольких сообщений хранилища просмотрите данные.

1. Выберите **Группы ресурсов**. Найдите группу ресурсов **контосоресаурцесмсжен**и выберите ее.

2. Выберите учетную запись хранения, которая является **контосостораже**. Затем в левой области выберите **Обозреватель службы хранилища (Предварительная версия)** .

   ![Выберите Обозреватель службы хранилища](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Выберите **контейнеры больших двоичных объектов** , чтобы просмотреть два контейнера, которые можно использовать.

   ![Просмотр контейнеров в учетной записи хранения](./media/tutorial-message-enrichments/show-blob-containers.png)

Сообщения в контейнере с именем **обогащенные** имеют обогащенные сообщения, содержащиеся в сообщениях. Сообщения в контейнере с именем **Original** имеют необработанные сообщения без дополнений. Детализируйте один из контейнеров до конца и откройте последний файл сообщения. Затем сделайте то же самое для другого контейнера, чтобы убедиться в отсутствии дополнений к сообщениям в этом контейнере.

При просмотре сообщений, которые были дополнены, вы должны увидеть "Мой центр Интернета вещей" с именем концентратора, расположением и ИДЕНТИФИКАТОРом клиента следующим образом:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Вот необогащенное сообщение. Обратите внимание, что "Мой центр Интернета вещей", "девицелокатион" и "customerID" не отображаются здесь, так как эти поля добавляются дополнениями. Эта конечная точка не имеет обогащений.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить все ресурсы, созданные в этом руководстве, удалите группу ресурсов. При этом будут также удалены все ресурсы, содержащиеся в группе. В этом случае происходит удаление центра Интернета вещей, учетной записи хранения и самой группы ресурсов.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Очистка ресурсов с помощью Azure CLI

Чтобы удалить группу ресурсов, используйте команду [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Помните, что в начале этого руководства для `$resourceGroup` было задано значение **контосоресаурцесмсжен** .

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве вы настроили и протестировали Добавление дополнений сообщений в сообщения центра Интернета вещей, выполнив следующие действия.

**Использование дополнений сообщений центра Интернета вещей**
> [!div class="checklist"]
> * Первый метод: создание ресурсов и настройка маршрутизации сообщений с помощью Azure CLI. Настройте дополнения сообщений вручную с помощью [портал Azure](https://portal.azure.com).
> * Второй способ: создание ресурсов и настройка маршрутизации сообщений и дополнений сообщений с помощью шаблона Azure Resource Manager.
> * Запустите приложение, которое имитирует устройство IoT, отправляющее сообщения в центр.
> * Просмотрите результаты и убедитесь, что обогащения сообщений работают правильно.

Дополнительные сведения о обогащении сообщений см. в разделе Обзор дополнений [сообщений](iot-hub-message-enrichments-overview.md).

Дополнительные сведения о маршрутизации сообщений см. в следующих статьях:

* [Использование маршрутизации сообщений центра Интернета вещей для отправки сообщений с устройства в облако в разные конечные точки](iot-hub-devguide-messages-d2c.md)
* [Учебник. Маршрутизация центра Интернета вещей](tutorial-routing.md)
