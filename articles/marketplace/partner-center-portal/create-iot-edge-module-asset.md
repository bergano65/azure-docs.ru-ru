---
title: Подготовьте технические активы модуля IoT Edge - Azure Marketplace
description: Узнайте о технических требованиях и требованиях к конфигурации, которые должны выполнить технические активы модуля Internet of Things (IoT) Edge, прежде чем публиковать их в Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730693"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>Подготовка технических ресурсов для модуля IoT Edge

> [!IMPORTANT]
> Мы перемещаем управление вашим испугом модуля IoT Edge с облачного партнерского портала в партнерский центр. До тех пор, пока ваши предложения не будут перенесены, пожалуйста, следуйте инструкциям в [подготовке технических активов модуля IoT Edge](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets) для Cloud Partner Portal для управления вашими предложениями.

В этой статье описаны требования, которым должны соответствовать технические активы модуля Internet of Things (IoT) Edge, прежде чем они будут опубликованы в Azure Marketplace.

## <a name="get-started"></a>Начало работы

Модуль IoT Edge — это контейнер, совместимый с Docker, который работает на устройстве IoT Edge.

- Дополнительные сведения о модулях IoT Edge см. в статье [Общие сведения о модулях IoT Edge Azure](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).
- Чтобы начать работу с разработкой модуля IoT Edge, [см.](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="technical-requirements"></a>Технические требования

Модуль IoT Edge должен соответствовать следующим техническим требованиям, которые должны быть сертифицированы и опубликованы в Azure Marketplace.

### <a name="platform-support"></a>Поддержка платформы

Модуль IoT Edge должен поддерживать один из следующих вариантов платформы:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>Платформы уровня 1, поддерживаемые IoT Edge

Модуль должен поддерживать все платформы Уровня 1, поддерживаемые поддержкой IoT Edge (как записано в [поддержке Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Мы рекомендуем использовать именно этот вариант, так как он обеспечивает максимальное удобство для клиентов. Модули, отвечающие этим критериям, будут продемонстрированы. Модуль с поддержкой этих платформ должен соответствовать следующим требованиям:

- Предоставьте последний тег и тег версии (например, 1.0.1), которые являются явнотегами, построенными с помощью [gitHub Manifest-инструмента.](https://github.com/estesp/manifest-tool)

- Используйте вкладку о включении предложения в [Partner Center,](https://partner.microsoft.com/dashboard/commercial-marketplace) чтобы добавить ссылку в разделе **Полезные ссылки** на [каталог сертифицированных устройств Azure IoT Edge.](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Группа платформ уровня 1, поддерживаемых IoT Edge

Модуль должен поддерживать подмножество (по крайней мере одну) платформ Tier 1, поддерживаемых поддержкой IoT Edge (записано в [поддержке Azure IoT Edge).](https://docs.microsoft.com/azure/iot-edge/support) Модуль с поддержкой этих платформ должен соответствовать следующим требованиям:

- Предоставьте последний тег и тег версии (например, 1.0.1), которые являются явнотегами, построенными с [помощью манифеста](https://github.com/estesp/manifest-tool) GitHub, если поддерживается более одной платформы. Теги манифеста необязательны, если поддерживается только одна платформа.
- Используйте вкладку о включении предложения в [Partner Center,](https://partner.microsoft.com/dashboard/commercial-marketplace) чтобы добавить ссылку в разделе **Полезные ссылки** по крайней мере на одно устройство IoT Edge из [каталога сертифицированных устройств Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Это изображение раздела о предложении в Партнерс-центре":::

### <a name="device-dimensions"></a>Измерения устройства

Размеры модуля IoT Edge (такие как процессор, оперативная память, хранилище и графический процессор) на целевых устройствах IoT Edge должны соответствовать следующим требованиям:

- Модуль должен работать по крайней мере с одним устройством IoT Edge из [каталога сертифицированных устройств Azure IoT Edge.](https://catalog.azureiotsolutions.com/)

- Минимальные требования к оборудованию должны быть задокументированы в качестве последнего пункта в описании предложения (в соответствии с вкладкой листинга предложения в [Partner Center).](https://partner.microsoft.com/dashboard/commercial-marketplace) При желании вы можете указать рекомендуемые требования к оборудованию, если они значительно отличаются от минимальных. Например, можно добавить в конец описания предложения следующий абзац:

Копируйте этот HTML-текст или используйте соответствующие функции насыщенного текста в окне редактирования.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Конфигурация

Модуль должен включать настройки конфигурации по умолчанию, чтобы сделать развертывание устройства IoT Edge максимально простым. Эта информация может быть предоставлена на странице **Технической конфигурации** плана в [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace) Контейнер может также включать модуль IoT Edge Module SDK для связи с концентратором края и Концентратором IoT.

#### <a name="default-configuration"></a>Конфигурация по умолчанию

Модули IoT Edge должны быть в состоянии начать с параметров по умолчанию, предусмотренных на странице **технической конфигурации** для плана в [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace) Доступны следующие параметры по умолчанию:

- **Маршруты** по умолчанию
- Модуль по умолчанию **двойные желаемые свойства**
- **Переменные среды** по умолчанию
- **Параметры создания контейнера** по умолчанию

В сценарии, когда параметр, необходимый для значения по умолчанию, не имеет смысла (например, IP-адрес сервера клиента), добавить параметр в качестве значения по умолчанию. Это значение верхнего корпуса и заключено в скобки. Например, можно настроить следующую переменную среды по умолчанию:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Документация по конфигурации

Все настройки конфигурации модуля IoT Edge должны быть четко документированы. Например, необходимо задокументировать, как использовать его маршруты, двойные желаемые свойства, переменные среды, создаватьОпции и так далее. Вы должны либо предоставить ссылку на вашу документацию или сделать его частью вашего предложения или описания плана. Вы можете предоставить эту информацию в **объявлении о предложении** и странице **листинга плана** в [Партнерс-центре.](https://partner.microsoft.com/dashboard/commercial-marketplace)

#### <a name="tags-and-versioning"></a>Теги и управление версиями

Клиенты должны иметь возможность легко развернуть модуль и автоматически получать обновления с рынка (в сценарии разработчика). Они также должны быть в состоянии использовать и заморозить точную версию, которые они тестировали (в производственном сценарии).

Чтобы удовлетворить эти ожидания клиентов и быть опубликованными на рынке, модули IoT Edge должны соответствовать следующим требованиям

- Включите манифест последнего тега, который указывает на последнюю версию на всех поддерживаемых платформах.
- Сделайте теги версий в форме X.Y., где X, Y и No являются многообразиями.
- Включите тег "версия", например 1.0.1, который указывает на конкретную версию на всех поддерживаемых платформах.
- Не обновляйте теги "версия", такие как 1.0.1, потому что они не должны быть изменены.

> [!NOTE]
> В качестве опционов версия может включать теги "rolling version", такие как 2.0 и 1.0. Эти теги позволяют параллельно использовать несколько основных версий.

### <a name="telemetry"></a>Телеметрия

Модули с использованием IoT-модуля SDK должны установить уникальный идентификатор модуля на PublisherId.OfferId.SkuId для телеметрии целей. Уникальный идентификатор помогает Azure Marketplace определить количество запущенных экземпляров модуля.

Используйте один из следующих методов из SDK IoT Module для установки ProductInfo в этот идентификатор:

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

Для модулей, которые не используют Модуль IoT SDK, менее точные сведения доступны через Partner Center, такие как количество загрузок.

### <a name="security"></a>Безопасность

Модули IoT Edge должны избегать [привилегированных модулей.](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) Вместо этого попросите наименее привилегированный доступ к хосту, насколько это возможно.

### <a name="module-iot-sdk"></a>Пакет SDK для модуля Интернета вещей

Включение пакета SDK для модуля Интернета вещей не является необходимым условием для сертификации, но это может повысить удобство работы пользователей. Например, он помогает поддерживать маршрутизацию или отправку сообщений в облако.

Модуль IoT SDK необходим для получения телеметрических данных о количестве запущенных экземпляров модуля.

## <a name="recertification-process"></a>Процесс повторной сертификации

Партнеры уведомляются всякий раз, когда происходит нарушение изменений, влияющих на их модули, такие как:

- Матрица поддержки ОС/арки уровня 1, поддерживаемая IoT Edge
- Пакет SDK для модуля Интернета вещей.
- Среда выполнения IoT Edge
- Руководящие принципы сертификации модулей IoT Edge

Партнеры должны обновить и переосертифицировать свои предложения, переопубликовав их в [Partner Center.](https://partner.microsoft.com/dashboard/commercial-marketplace)

Ваше предложение также будет переаттестовано, если вы обновите его, например, добавление новых тегов изображений.

## <a name="host-module-in-azure-container-registry"></a>Модуль-хост в реестре контейнеров Azure

Чтобы загрузить модуль IoT Edge в Azure Marketplace, сначала необходимо разместить его в [реестре контейнеров Azure](https://azure.microsoft.com/services/container-registry/) (ACR). Модуль должен включать все теги, которые вы хотите опубликовать, включая теги изображений, на которые ссылается явное тег. Для получения дополнительной информации см. [Create an Azure container registry and push a container image](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)

## <a name="next-steps"></a>Следующие шаги

- [Создание предложения модуля IoT Edge](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)