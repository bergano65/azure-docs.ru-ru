---
title: Подключение к устройству Pro с помощью интерфейса Windows PowerShell и управление им Microsoft Azure Stack Документация Майкрософт
description: Описывает, как подключиться к Azure Stack пограничному Pro через интерфейс Windows PowerShell и затем управлять им.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: b6b0fe7e9e096b252d33d25c4a70305e57d206b1
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894429"
---
# <a name="manage-an-azure-stack-edge-pro-device-via-windows-powershell"></a>Управление устройством Azure Stack пограничной Pro с помощью Windows PowerShell

Azure Stack пограничных решений Pro позволяет обрабатывать данные и передавать их по сети в Azure. В этой статье описываются некоторые задачи по настройке и управлению для устройства на Azure Stack пограничной Pro. Для управления устройством можно использовать портал Azure, локальный веб-интерфейс или интерфейс Windows PowerShell.

В этой статье рассматриваются задачи, выполняемые с помощью интерфейса PowerShell. 

Эта статья содержит следующие процедуры.

- Подключитесь к интерфейсу PowerShell.
- Создать пакет поддержки.
- Передача сертификата
- Сброс устройства
- Просмотр сведений об устройстве
- Получение журналов вычислений
- Мониторинг и устранение неполадок модулей вычислений

## <a name="connect-to-the-powershell-interface"></a>Подключитесь к интерфейсу PowerShell.

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Можно также отправить сертификаты IoT Edge, чтобы обеспечить безопасное подключение между устройством IoT Edge и подчиненными устройствами, которые могут к нему подключаться. Необходимо установить три IoT Edge сертификатов (формат*PEM* ):

- Сертификат корневого ЦС или ЦС владельца
- Сертификат ЦС устройства
- Сертификат ключа устройства

В следующем примере показано использование этого командлета для установки сертификатов IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
При запуске этого командлета вам будет предложено ввести пароль для сетевой папки.

Дополнительные сведения о сертификатах см. в [подAzure IoT Edge сертификаты](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) или [Установка сертификатов на шлюзе](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Просмотр сведений об устройстве
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>выполнить сброс устройства;

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Получение журналов вычислений

Если на устройстве настроена роль вычислений, можно также получить журналы вычислений с помощью интерфейса PowerShell.

1. [Подключитесь к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-AzureDataBoxEdgeComputeRoleLogs` для получения журналов вычислений для устройства.

    В следующем примере показано использование этого командлета:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Ниже приведено описание параметров, используемых для командлета.
    - `Path`: Укажите сетевой путь к общей папке, в которой нужно создать пакет журнала вычислений.
    - `Credential`: Укажите имя пользователя для сетевой папки. При выполнении этого командлета необходимо указать пароль для общей папки.
    - `FullLogCollection`: Этот параметр гарантирует, что пакет журнала будет содержать все журналы вычислений. По умолчанию пакет журналов содержит только подмножество журналов.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Мониторинг и устранение неполадок модулей вычислений

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Выход из удаленного сеанса

Чтобы выйти из удаленного сеанса PowerShell, закройте окно PowerShell.

## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) на портале Azure.
