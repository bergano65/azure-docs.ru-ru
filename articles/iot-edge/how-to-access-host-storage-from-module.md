---
title: Используйте локальное хранилище устройства IoT Edge из модуля - Azure IoT Edge Документы Майкрософт
description: Используйте переменные среды и создавайте параметры для обеспечения доступа к локальному устройству IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75434522"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Предоставление модулям доступа к локальному хранилищу устройства

Помимо хранения данных с помощью служб хранения Azure или в контейнерном хранилище устройства, вы также можете посвятить хранение на устройстве IoT Edge для повышения надежности, особенно при работе в автономном режиме.

## <a name="link-module-storage-to-device-storage"></a>Ссылка на хранилище модулей с хранилищем устройств

Чтобы включить ссылку от хранилища модулей к хранилищу в системе-носителе создадут переменную среды для модуля, которая указывает на папку хранения в контейнере. Затем используйте параметры создания для привязки этой папки хранения к папке на хост-компьютере.

Например, если вы хотите включить концентратор IoT Edge для хранения сообщений в локальном хранилище устройства и их последующего получения, можно настроить переменные среды и создать параметры на портале Azure в разделе **Параметры выполнения.**

1. Для концентратора IoT Edge и агента IoT Edge добавьте переменную среды, называемую **storageFolder,** которая указывает на каталог в модуле.
1. Для концентратора IoT Edge и агента IoT Edge добавьте связывания для подключения локального каталога на локальном компьютере к каталогу в модуле. Пример:

   ![Добавление параметров создания и переменных среды для локального хранилища](./media/how-to-access-host-storage-from-module/offline-storage.png)

Или можно настроить локальное хранилище непосредственно в манифесте развертывания. Пример:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

`<HostStoragePath>` Заменить `<ModuleStoragePath>` и с вашего хоста и модуля пути хранения; оба значения должны быть абсолютным путем.

Например, в системе `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` Linux, означает, что каталог **/etc/iotedge/storage** в вашей системе хоста отображается в каталоге **/iotedge/storage/** в контейнере. В системе Windows, как `"Binds":["C:\\temp:C:\\contemp"]` еще один пример, означает каталог **\\C: температура** в вашей системе хоста отображается в каталоге **C:\\contemp** в контейнере.

Кроме того, на устройствах Linux убедитесь, что профиль пользователя для вашего модуля имеет необходимые разрешения на чтение, запись и выполнение разрешений в каталог еходной системы. Возвращаясь к более раннему примеру, позволяющему концентратору IoT Edge хранить сообщения в локальном хранилище устройства, необходимо предоставить разрешения на профиль пользователя UID 1000. (Агент IoT Edge работает как корень, поэтому ему не нужны дополнительные разрешения.) Существует несколько способов управления разрешениями каталогов `chown` в системах Linux, `chmod` включая использование для изменения владельца каталога, а затем для изменения разрешений, таких как:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Вы можете найти более подробную информацию о возможностях создания из [докерных документов.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительный пример доступа к хранилищу хоста из модуля см. [Данные хранилища на краю с Azure Blob Storage на IoT Edge.](how-to-store-data-blob.md)
