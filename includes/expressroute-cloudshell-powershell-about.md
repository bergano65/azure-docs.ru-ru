---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/21/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ba6673b5fda6af8c9bd3ef53dff33fe63f41e26d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133056"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов, можно использовать Azure Cloud Shell. Azure Cloud Shell — это бесплатная интерактивная оболочка, который имеет Общие инструменты Azure, которые предварительно установлены и настроены для использования с вашей учетной записи. Нажмите кнопку **Копировать**, чтобы скопировать код. Вставьте его в Cloud Shell и нажмите клавишу ВВОД, чтобы запустить код. Cloud Shell можно запустить разными способами:

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. | ![Cloud Shell в этой статье](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Откройте Cloud Shell в браузере. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна портала Azure. | [![Cloud Shell на портале](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Локальный запуск Azure PowerShell**

Можно также установить и запустить командлеты Azure PowerShell локально на компьютере. Командлеты PowerShell часто обновляются. Если вы не используете последнюю версию, значения, указанные в инструкциях по может завершиться ошибкой. Чтобы найти установленных версий PowerShell в вашей системе, используйте `Get-Module -ListAvailable Az` командлета. Чтобы установить или обновить, см. в разделе [установить модуль Azure PowerShell](/powershell/azure/install-az-ps).
