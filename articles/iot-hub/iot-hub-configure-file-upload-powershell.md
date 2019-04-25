---
title: Использование Azure PowerShell для настройки отправки файлов | Документация Майкрософт
description: В этой статье рассказывается, как с помощью командлетов Azure PowerShell настроить Центр Интернета вещей, чтобы включить отправку файлов с подключенных устройств. Содержит сведения о настройке целевой учетной записи хранения Azure.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: robinsh
ms.openlocfilehash: c8fc0393e0961b46fbb8031d735f27e9ad785031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318466"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Настройка отправки файлов в Центре Интернета вещей с помощью PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

Чтобы использовать [функцию передачи файлов в Центре Интернета вещей](iot-hub-devguide-file-upload.md), сначала необходимо связать учетную запись хранения Azure с Центром Интернета вещей. Можно использовать существующую учетную запись хранения или создать новую.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Для работы с этим учебником требуется:

* Активная учетная запись Azure. Если ее нет, можно создать [бесплатную учетную запись](https://azure.microsoft.com/pricing/free-trial/) всего за несколько минут.

* [Командлеты Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Центр интернета вещей Azure. Если у вас нет центра Интернета вещей, можно использовать [командлет New-AzIoTHub](https://docs.microsoft.com/powershell/module/az.iothub/new-aziothub) или использовать портал для [создание центра Интернета вещей](iot-hub-create-through-portal.md).

* Учетная запись хранения Azure. [Создать учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) (если у вас ее еще нет) можно с помощью [командлетов PowerShell службы хранилища Azure](https://docs.microsoft.com/powershell/module/az.storage/) или на портале

## <a name="sign-in-and-set-your-azure-account"></a>Выполнение входа и установка учетной записи Azure

Войдите в учетную запись Azure и выберите подписку.

1. В командной строке PowerShell выполните **Connect AzAccount** командлета:

    ```powershell
    Connect-AzAccount
    ```

2. Если у вас есть несколько подписок Azure, то при входе в Azure вы получите доступ ко всем подпискам Azure, связанным с вашими учетными данными. Используйте следующую команду, чтобы просмотреть подписки Azure, доступные для использования:

    ```powershell
    Get-AzSubscription
    ```

    Чтобы выбрать подписку, с помощью которой будут выполняться команды для управления Центром Интернета вещей, используйте следующую команду. Вы можете использовать имя подписки или идентификатор из выходных данных предыдущей команды:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

## <a name="retrieve-your-storage-account-details"></a>Получение сведений об учетной записи хранения

В следующих шагах предполагается, что для создания учетной записи хранения вы использовали модель развертывания **с помощью Resource Manager**, а не **классическую** модель развертывания.

Для настройки отправки файлов с ваших устройств необходима строка подключения учетной записи хранения Azure. Эта учетная запись хранения должна относиться к той же подписке, что и Центр Интернета вещей. Кроме того, вам понадобится имя контейнера BLOB-объектов в учетной записи хранения. Для получения ключей учетной записи хранения используйте следующую команду:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Запишите значение ключа **key1** учетной записи хранения. Оно понадобится вам на следующих этапах.

Для отправки файлов можно использовать существующий контейнер BLOB-объектов или создать новый.

* Чтобы получить список существующих контейнеров BLOB-объектов в вашей учетной записи хранения, используйте следующие команды:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Для создания контейнера BLOB-объектов в учетной записи хранения используйте следующие команды:

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Настройка Центра Интернета вещей

Теперь с помощью данных учетной записи хранения можно настроить Центр Интернета вещей для [отправки файлов в Центр Интернета вещей](iot-hub-devguide-file-upload.md).

Для настройки потребуются следующие значения:

* **Контейнер хранилища**: контейнер BLOB-объектов в учетной записи хранения Azure в текущей подписке Azure, который нужно связать с Центром Интернета вещей. Необходимые сведения об учетной записи хранения вы получили в предыдущем разделе. Центр Интернета вещей автоматически генерирует универсальные коды ресурсов (URI) подписанных URL-адресов с разрешениями на запись в этом контейнере больших двоичных объектов, чтобы устройства могли их использовать во время передач файлов.

* **Получение уведомления об отправленных файлах**: включение или отключение уведомлений об отправке файлов.

* **Срок жизни SAS**: этот параметр определяет срок жизни URI-адресов SAS, возвращаемых Центром Интернета вещей на устройство. Значение по умолчанию — один час.

* **Срок жизни уведомления о файле по умолчанию**: срок жизни уведомления об отправке файла. Значение по умолчанию — один день.

* **Максимальное число доставок уведомления о файле**: количество попыток доставки уведомления о передаче файла, предпринимаемых Центром Интернета вещей. Значение по умолчанию — 10.

Чтобы настроить параметры отправки файлов в Центре Интернета вещей, используйте следующий командлет PowerShell:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о возможностях Центра Интернета вещей, связанных с отправкой файлов, см. в разделе [Отправка файлов с помощью Центра Интернета вещей](iot-hub-devguide-file-upload.md).

Дополнительные сведения об управлении Центром Интернета вещей в Azure см. по следующим ссылкам:

* [Массовое управление удостоверениями устройств Центра Интернета вещей](iot-hub-bulk-identity-mgmt.md)
* [Общие сведения о метриках Центра Интернета вещей](iot-hub-metrics.md)
* [Мониторинг операций](iot-hub-operations-monitoring.md)

Для дальнейшего изучения возможностей Центра Интернета вещей см. следующие статьи:

* [Руководство разработчика для Центра Интернета вещей](iot-hub-devguide.md)
* [Краткое руководство. Развертывание первого модуля IoT Edge на устройстве под управлением 64-разрядной ОС Linux](../iot-edge/tutorial-simulate-device-linux.md)
* [Комплексная защита в Интернете вещей](../iot-fundamentals/iot-security-ground-up.md)
