---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6a0ea318f2e9b8f392ac7c0a1f1091c062c59d41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852370"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов можно использовать бесплатную интерактивную оболочку Azure Cloud Shell. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Нажмите кнопку **Копировать**, чтобы скопировать код. Вставьте его в Cloud Shell и нажмите клавишу ВВОД, чтобы запустить код. Cloud Shell можно запустить разными способами:

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. | ![Cloud Shell в этой статье](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Откройте Cloud Shell в браузере. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна портала Azure. | [![Cloud Shell на портале](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Если вы не хотите использовать Azure Cloud Shell, можно установить PowerShell локально. Если вы хотите установить и использовать PowerShell локально, обязательно установите последнюю версию командлетов PowerShell для Azure Resource Manager, чтобы воспользоваться их новейшими функциональными возможностями.

Чтобы узнать, какая версия PowerShell выполняется локально, используйте командлет Get-Module -ListAvailable AzureRM. Если необходимо выполнить обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Подробнее: [Установка и настройка Azure PowerShell](/powershell/azure/overview).