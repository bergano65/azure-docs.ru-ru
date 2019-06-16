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
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133429"
---
В этой статье используются командлеты PowerShell. Для запуска командлетов можно использовать Azure Cloud Shell, интерактивную оболочку среды, размещенную в Azure и работающую в браузере. В Azure Cloud Shell предустановлены командлеты Azure PowerShell.

Для выполнения кода из этой статьи в Azure Cloud Shell откройте сеанс Cloud Shell, скопируйте блок кода с помощью кнопки **Копировать** и вставьте его в сеанс Cloud Shell, нажав комбинацию клавиш __CTRL + SHIFT + V__ в Windows и Linux или __CMD + SHIFT + V__ в macOS. Вставленный текст не выполняется автоматически, поэтому нажмите клавишу **ВВОД** для выполнения кода.

Запустить Azure Cloud Shell можно следующим образом.

|  |   |
|-----------------------------------------------|---|
| Нажмите кнопку **Попробовать** в правом верхнем углу блока с кодом. При этом __не выполняется__ автоматическое копирование текста в Cloud Shell. | ![Открытие Azure Cloud Shell с помощью кнопки "Попробовать"](./media/cloud-shell-try-it/cli-try-it.png) |
| В браузере перейдите по адресу [shell.azure.com](https://shell.azure.com). | [![Запуск кнопки Azure Cloud Shell](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна [портала Azure](https://portal.azure.com). | ![Кнопка "Cloud Shell" на портале Azure](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**PowerShell запущен локально**

Можно также установить и запустить командлеты Azure PowerShell локально на компьютере. Командлеты PowerShell часто обновляются. Если вы не используете последнюю версию, значения, указанные в инструкциях по может завершиться ошибкой. Чтобы найти версии Azure PowerShell, установленных на компьютере, используйте `Get-Module -ListAvailable Az` командлета. Чтобы установить или обновить, см. в разделе [установить модуль Azure PowerShell](/powershell/azure/install-az-ps).
