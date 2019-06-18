---
title: Подключения и управления устройства Microsoft Azure Data Box в интерфейсе Windows PowerShell | Документация Майкрософт
description: Описывает, как подключиться к и затем управлять Edge поле данных через интерфейс Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 8cd89b21e80662ec50746e0c7721a5544cfbce30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717499"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Управление Azure Data Box пограничного устройства с помощью Windows PowerShell

Решение Azure Edge поле данных позволяет обрабатывать данные и отправлять их по сети в Azure. В этой статье описываются некоторые задачи конфигурации и управления для устройства Edge поле данных. Можно использовать портал Azure, локального веб-интерфейса или в интерфейсе Windows PowerShell для управления устройством.

Эта статья посвящена задачи, которые можно сделать с помощью интерфейса PowerShell.

В этой статье описываются следующие процедуры:

- Подключение к интерфейсу PowerShell
- Создать пакет поддержки.
- Передача сертификата
- Сброс устройства
- Просмотр сведений об устройстве
- Получение журналов вычислений
- Мониторинг и устранение неполадок модули вычислений

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Можно также отправить сертификаты IoT Edge, чтобы обеспечить безопасное подключение между вашим устройством IoT Edge и подчиненными устройствами, которые могут подключаться к нему. Существует три сертификата IoT Edge (*PEM-файл* формат), необходимо установить:

- Сертификат корневого ЦС или ЦС владельца
- Сертификат ЦС устройства
- Сертификат ключа устройства

В следующем примере показано использование этого командлета для установки сертификатов IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Дополнительные сведения о сертификатах см. в статье [сертификаты Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) или [установить сертификаты на шлюзе](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Просмотр сведений об устройстве
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Сброс устройства

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Получение журналов вычислений

Если роль вычислительного настроено на вашем устройстве, можно также передавать журналы вычислений через интерфейс PowerShell.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-AzureDataBoxEdgeComputeRoleLogs` получить журналы вычислений для вашего устройства.

    В следующем примере показано использование этого командлета:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Ниже приведено описание параметров, используемых для командлета:
    - `Path`: Введите сетевой путь к общей папке, где вы хотите создать пакет журналов вычисления.
    - `Credential`: Укажите имя пользователя и пароль на общий сетевой ресурс.
    - `RoleInstanceName`: Укажите эту строку `IotRole` для этого параметра.
    - `FullLogCollection`: Этот параметр гарантирует, что пакет журналов будет содержать все журналы вычислений. По умолчанию пакет журналов содержит только подмножество журналов.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Мониторинг и устранение неполадок модули вычислений

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Выйдите из удаленного сеанса

Чтобы выйти из удаленного сеанса PowerShell, закройте окно PowerShell.

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Azure Data Box Edge](data-box-edge-deploy-prep.md) на портале Azure.
