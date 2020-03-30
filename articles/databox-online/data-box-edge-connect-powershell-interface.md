---
title: Подключение к устройству Microsoft Azure Data Box Edge и управление имеющее его с помощью интерфейса Windows PowerShell Документы Майкрософт
description: Описывает, как подключиться к Data Box Edge и затем управлять ими с помощью интерфейса Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265484"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Управление устройством Azure Data Box Edge с помощью Windows PowerShell

Решение Azure Data Box Edge позволяет обрабатывать данные и отправлять их по сети в Azure. В этой статье описаны некоторые задачи конфигурации и управления для устройства Data Box Edge. Для управления устройством можно использовать портал Azure, локальный веб-интерфейс или интерфейс Windows PowerShell.

В этой статье основное внимание уделяется задачам, которые вы делаете с помощью интерфейса PowerShell.

Эта статья включает в себя следующие процедуры:

- Подключение к интерфейсу PowerShell
- Создать пакет поддержки.
- Передача сертификата
- Сбросить устройство
- Просмотр информации об устройстве
- Получить журналы вычислений
- Монитор и устранение неполадок вычислили модули

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Вы также можете загрузить сертификаты IoT Edge, чтобы обеспечить безопасное соединение между устройством IoT Edge и устройствами ниже по течению, которые могут подключиться к нему. Есть три сертификата IoT Edge *(формат .pem),* которые необходимо установить:

- Сертификат Root CA или владелец CA
- Сертификат ЦС устройства
- Сертификат ключа устройства

Следующий пример показывает использование этого cmdlet для установки сертификатов IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Когда вы запустите этот cmdlet, вам будет предложено предоставить пароль для сети доля.

Для получения дополнительной информации о сертификатах перейдите на [сертификаты Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) или [установите сертификаты на шлюзе.](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)

## <a name="view-device-information"></a>Просмотр информации об устройстве
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>выполнить сброс устройства;

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Получить журналы вычислений

Если роль вычисления настроена на устройстве, вы также можете получить журналы вычислений через интерфейс PowerShell.

1. [Подключение к интерфейсу PowerShell.](#connect-to-the-powershell-interface)
2. `Get-AzureDataBoxEdgeComputeRoleLogs` Используйте, чтобы получить журналы вычислений для вашего устройства.

    Следующий пример показывает использование этого cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Вот описание параметров, используемых для cmdlet:
    - `Path`: Предоставьте сетевой путь к доле, где требуется создать пакет журналов вычислений.
    - `Credential`: Укажите имя пользователя для совместной акции сети. При запуске этого cmdlet, вам нужно будет предоставить пароль общего обмена.
    - `FullLogCollection`: Этот параметр гарантирует, что пакет журналов будет содержать все журналы вычислений. По умолчанию пакет журналов содержит только подмножество журналов.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Монитор и устранение неполадок вычислили модули

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Выход из удаленного сеанса

Чтобы выйти из удаленного сеанса PowerShell, закройте окно PowerShell.

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Azure Data Box Edge](data-box-edge-deploy-prep.md) на портале Azure.
