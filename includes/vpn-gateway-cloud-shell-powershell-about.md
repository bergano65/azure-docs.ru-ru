---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045087"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов можно использовать Azure Cloud Shell, интерактивную оболочку среды, размещенную в Azure и работающую в браузере. В Azure Cloud Shell предустановлены командлеты Azure PowerShell.

Для выполнения кода из этой статьи в Azure Cloud Shell откройте сеанс Cloud Shell, скопируйте блок кода с помощью кнопки **Копировать** и вставьте его в сеанс Cloud Shell, нажав клавиши __CTRL+SHIFT+V__ в Windows и Linux или __CMD+SHIFT+V__ в macOS. Вставленный текст не выполняется автоматически, поэтому нажмите клавишу **ВВОД** для выполнения кода.

Запустить Azure Cloud Shell можно следующим образом.

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. При этом __не выполняется__ автоматическое копирование текста в Cloud Shell. | ![Открытие Azure Cloud Shell с помощью кнопки "Попробовать"](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| В браузере перейдите по адресу [shell.azure.com](https://shell.azure.com). | [![Запуск кнопки Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна [портала Azure](https://portal.azure.com). | ![Кнопка "Cloud Shell" на портале Azure](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Локальное выполнение PowerShell**

Можно также установить и запустить командлеты Azure PowerShell локально на компьютере. Командлеты PowerShell часто обновляются. Если вы не используете последнюю версию, значения, указанные в инструкциях, могут завершиться ошибкой. Чтобы узнать, какие версии Azure PowerShell установлены на компьютере, используйте командлет `Get-Module -ListAvailable Az`. Сведения об установке или обновлении см. в разделе [Установка модуля Azure PowerShell](/powershell/azure/install-az-ps).

Если вы используете PowerShell локально, не забудьте выполнить команду "Connect-Азаккаунт", чтобы создать подключение к Azure.