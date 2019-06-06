---
title: включение файла
description: включение файла
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f6af5e5e42a16548b1997845ea5076ddd4dd3be5
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66736015"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов, можно использовать Azure Cloud Shell, интерактивную оболочку среды размещенная в Azure и использующаяся через браузер. Azure Cloud Shell поставляется с предустановленным командлетов Azure PowerShell.

Для выполнения любого кода, содержащихся в этой статье на Azure Cloud Shell, откройте сеанс Cloud Shell, используйте **копирования** кнопки в блоке кода, скопируйте код и вставьте его в сеанс Cloud Shell с __Ctrl + Shift + V__ на Windows и Linux, или __Cmd + Shift + V__ в macOS. Вставленный текст не выполняется автоматически, поэтому нажмите клавишу **ввод** для выполнения кода.

Вы можете запустить Azure Cloud Shell с помощью:

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. Это __не__ автоматически скопировать текст в Cloud Shell. | ![Открытие Azure Cloud Shell с помощью кнопки "Попробовать"](./media/cloud-shell-try-it/cli-try-it.png) |
| Откройте [shell.azure.com](https://shell.azure.com) в браузере. | [![Запуск кнопки Azure Cloud Shell](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна [портала Azure](https://portal.azure.com). | ![Кнопка "Cloud Shell" на портале Azure](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**PowerShell запущен локально**

Можно также установить и запустить командлеты Azure PowerShell локально на компьютере. Командлеты PowerShell часто обновляются. Если вы не используете последнюю версию, значения, указанные в инструкциях по может завершиться ошибкой. Чтобы узнать версию PowerShell, которые выполняются локально, используйте `Get-Module -ListAvailable Az` командлета. Чтобы установить или обновить, см. в разделе [установить модуль Azure PowerShell](/powershell/azure/install-az-ps).
