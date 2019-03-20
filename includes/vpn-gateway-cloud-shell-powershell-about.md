---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884038"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов, можно использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная интерактивная оболочка, который имеет Общие инструменты Azure, которые предварительно установлены и настроены для использования с вашей учетной записи. Просто щелкните **копирования** чтобы скопировать код, вставьте его в Cloud Shell и нажмите клавишу ВВОД, чтобы запустить его. Cloud Shell можно запустить разными способами:

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. | ![Cloud Shell в этой статье](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Откройте Cloud Shell в браузере. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна портала Azure. | [![Cloud Shell на портале](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**PowerShell запущен локально**

Можно также установить и запустить командлеты Azure PowerShell локально на компьютере. Командлеты PowerShell часто обновляются. Если вы не используете последнюю версию, значения, указанные в инструкциях по может завершиться ошибкой. Чтобы узнать версию PowerShell, которые выполняются локально, используйте `Get-Module -ListAvailable Az` командлета. Чтобы установить или обновить, см. в разделе [установить модуль Azure PowerShell](/powershell/azure/install-az-ps).