---
title: Подключения и управления устройства Microsoft Azure Data Box в интерфейсе Windows PowerShell | Документация Майкрософт
description: Описывает, как подключиться к и затем управлять Edge поле данных через интерфейс Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556456"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>Управление Azure Data Box пограничного устройства с помощью Windows PowerShell (Предварительная версия)

Решение Azure Edge поле данных позволяет обрабатывать данные и отправлять их по сети в Azure. В этой статье описываются некоторые задачи конфигурации и управления для устройства Edge поле данных. Можно использовать портал Azure, локального веб-интерфейса или в интерфейсе Windows PowerShell для управления устройством.

Эта статья посвящена задачи, которые можно сделать с помощью интерфейса PowerShell.

В этой статье описываются следующие процедуры:

- Подключение к интерфейсу PowerShell
- Запуск сеанса поддержки
- Создать пакет поддержки.
- Передача сертификата
- Сброс устройства
- Просмотр сведений об устройстве
- Получение журналов вычислений
- Мониторинг и устранение неполадок модули вычислений

> [!IMPORTANT]
> Azure Edge поле данных находится в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Подключение к интерфейсу PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Запуск сеанса поддержки

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Создать пакет поддержки.

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Передача сертификата

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>Просмотр сведений об устройстве
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Сброс устройства

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Получение журналов вычислений

Если роль вычислительного настроено на вашем устройстве, можно также передавать журналы вычислений через интерфейс PowerShell.

1. [Подключение к интерфейсу PowerShell](#connect-to-the-powershell-interface).
2. Используйте `Get-AzureDataBoxEdgeComputeRoleLogs` получить журналы вычислений для вашего устройства.

    В следующем примере показано использование этого командлета:

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Ниже приведено описание параметров, используемых для командлета: 
    - `Path`: Введите сетевой путь к общей папке, где вы хотите создать пакет журналов вычисления.
    - `Credential`: Укажите имя пользователя и пароль на общий сетевой ресурс.
    - `RoleInstanceName`: Укажите эту строку `IotRole` для этого параметра.
    - `FullLogCollection`: Этот параметр гарантирует, что пакет журналов будет содержать все журналы вычислений. По умолчанию пакет журналов содержит только подмножество журналов.


## <a name="next-steps"></a>Дальнейшие действия

- Разверните [Azure Data Box Edge](data-box-edge-deploy-prep.md) на портале Azure.
