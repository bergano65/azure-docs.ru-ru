---
title: Учебник по созданию видеоаналитики — приложения для распознавания объектов и движения в Azure IoT Central (YOLO версии 3)
description: В этом учебнике показано, как создать приложение видеоаналитики в IoT Central. Его можно создать, настроить и подключить к другим службам Azure. В этом руководстве используется система обнаружения объектов в реальном времени YOLO версии 3.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: 8066f42a5ab34f5cab0afcdc42cc6ccacfff2855
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876732"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Руководство по созданию видеоаналитики — приложения для распознавания объектов и движения в Azure IoT Central (YOLO версии 3)

Узнайте, как разработчики решений создают приложения для видеоаналитики с помощью шаблона приложения *видеоаналитики для распознавания объектов и движения* в IoT Central, устройств Azure IoT Edge, Служб мультимедиа Azure и системы распознавания объектов и движения в реальном времени YOLO версии 3. Решение использует магазин розничной торговли, чтобы продемонстрировать, как удовлетворить стандартные бизнес-потребности для мониторинга с помощью камер безопасности. Решение использует автоматическое обнаружение объектов в видеоканале, чтобы быстро находить и распознавать интересующие события.

> [!TIP]
> Сведения об использовании OpenVINO&trade; вместо YOLO версии 3 для обнаружения объектов и движения см. в статье [Учебник по созданию видеоаналитики — приложение для распознавания объектов и движения в Azure IoT Central (OpenVINO&trade;)](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt)
- [deployment.amd64.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.json](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json)

> [!NOTE]
> Репозиторий GitHub также содержит исходный код для модулей IoT Edge **LvaEdgeGatewayModule** и **lvaYolov3**. Дополнительные сведения о работе с исходным кодом см. в статье [Учебник по изменению и созданию модулей шлюза видеоаналитики в реальном времени](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Изменение манифеста развертывания

Модуль IoT Edge развертывается с помощью манифеста развертывания. В IoT Central можно импортировать манифест как шаблон устройства, а затем разрешить IoT Central управлять развертыванием модуля.

Чтобы подготовить манифест развертывания, выполните следующие действия.

1. Откройте файл *deployment.amd64.json*, сохраненный в папке *lva-configuration*, с помощью текстового редактора.

1. Найдите параметры `LvaEdgeGatewayModule` и измените имя образа, как показано в следующем фрагменте кода:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Добавьте имя учетной записи Служб мультимедиа в узел `env` в разделе `LvaEdgeGatewayModule`. Вы записали это имя учетной записи в файле *scratchpad.txt*:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_ACCOUNT_NAME>"
        }
    }
    ```

1. Шаблон не предоставляет эти свойства в IoT Central, поэтому необходимо добавить значения конфигурации Служб мультимедиа в манифест развертывания. Найдите модуль `lvaEdge` и замените заполнители значениями, которые вы записали в файле *scratchpad.txt* при создании учетной записи Служб мультимедиа.

    `azureMediaServicesArmId` является **идентификатором ресурса**, который вы записали в файле *scratchpad.txt* при создании учетной записи Служб мультимедиа.

    При создании субъекта-службы для учетной записи Служб мультимедиа вы записали `aadTenantId`, `aadServicePrincipalAppId` и `aadServicePrincipalSecret` в файле *scratchpad.txt*.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Сохраните изменения.

В рамках этого учебника выполняется настройка решения для использования модуля YOLO версии 3 для обнаружения объектов и движения. В следующем фрагменте кода показана конфигурация модуля:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>Замена манифеста

На странице **шлюза LVA Edge** выберите **+ Replace manifest** (+ Заменить манифест).

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Замена манифеста":::

Перейдите в папку *lva-configuration* и выберите файл манифеста *deployment.amd64.json*, который вы изменили ранее. Щелкните **Отправить**. После завершения проверки нажмите кнопку **Заменить**.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
