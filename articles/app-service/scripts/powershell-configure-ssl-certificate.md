---
title: Пример сценария Azure PowerShell. Привязка пользовательского SSL-сертификата к веб-приложению | Документация Майкрософт
description: Пример сценария Azure PowerShell для привязки пользовательского SSL-сертификата к веб-приложению.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c9214d8906ad00744f811782f732b15aeec3a7f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66136543"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Привязка SSL-сертификата к веб-приложению с помощью PowerShell

Этот сценарий создает в службе приложений веб-приложение со связанными ресурсами, а затем привязывает к нему SSL-сертификат имени личного домена. 

При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Connect-AzAccount`, чтобы создать подключение к Azure. Кроме того, убедитесь в следующем.

- Подключение к Azure установлено с помощью команды `az login`.
- У вас есть доступ к странице конфигурации DNS вашего регистратора доменных имен.
- У вас есть допустимый PFX-файл и пароль для SSL-сертификата, который требуется передать и привязать.

## <a name="sample-script"></a>Пример скрипта

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполнив пример сценария, вы можете удалить группу ресурсов, веб-приложение и все связанные ресурсы, используя следующую команду.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Команда | Примечания |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Создает план службы приложений. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Создает веб-приложение. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Обновляет план службы приложений для изменения ценовой категории. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Изменяет конфигурацию веб-приложения. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Создает привязку SSL-сертификата к веб-приложению. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

Дополнительные примеры сценариев Azure PowerShell для веб-приложений службы приложений Azure доступны [здесь](../samples-powershell.md).
