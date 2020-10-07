---
ms.openlocfilehash: be983a643b45847d2a44db018b1383aa56ab2302
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "88691196"
---
1. Клонируйте репозиторий из этого расположения: https://github.com/Azure-Samples/live-video-analytics-iot-edge-python.
1. В Visual Studio Code откройте папку, в которую вы скачали репозиторий.
1. В Visual Studio Code перейдите в папку *src/cloud-to-device-console-app*. Создайте там файл и назовите его *appsettings.json*. Этот файл содержит параметры, необходимые для выполнения программы.
1. Скопируйте содержимое из созданного ранее файла *~ / clouddrive / lva-sample / appsettings.json*.

    Текст должен выглядеть как следующий вывод:

    ```
    {  
        "IoThubConnectionString" : "HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX",  
        "deviceId" : "lva-sample-device",  
        "moduleId" : "lvaEdge"  
    }
    ```
1. Перейдите в папку *src/edge* и создайте файл с именем *.env*.
1. Скопируйте содержимое файла */clouddrive/lva-sample/edge-deployment/.env*. Текст должен выглядеть как следующий код:

    ```
    SUBSCRIPTION_ID="<Subscription ID>"  
    RESOURCE_GROUP="<Resource Group>"  
    AMS_ACCOUNT="<AMS Account ID>"  
    IOTHUB_CONNECTION_STRING="HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx"  
    AAD_TENANT_ID="<AAD Tenant ID>"  
    AAD_SERVICE_PRINCIPAL_ID="<AAD SERVICE_PRINCIPAL ID>"  
    AAD_SERVICE_PRINCIPAL_SECRET="<AAD SERVICE_PRINCIPAL ID>"  
    INPUT_VIDEO_FOLDER_ON_DEVICE="/home/lvaadmin/samples/input"  
    OUTPUT_VIDEO_FOLDER_ON_DEVICE="/var/media"
    APPDATA_FOLDER_ON_DEVICE="/var/local/mediaservices"
    CONTAINER_REGISTRY_USERNAME_myacr="<your container registry username>"  
    CONTAINER_REGISTRY_PASSWORD_myacr="<your container registry password>"      
    ```
    