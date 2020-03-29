---
title: Обновление шаблона развертывания Visual Studio для использования Az PowerShell
description: Обновление сценария развертывания шаблона Visual Studio из AzureRM в Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963875"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Обновление сценария развертывания шаблона Visual Studio для использования модуля Az PowerShell

Visual Studio 16.4 поддерживает использование модуля Az PowerShell в скрипте развертывания шаблонов. Тем не менее, Visual Studio не устанавливает автоматически этот модуль. Чтобы использовать модуль Az, необходимо сделать четыре шага:

1. [Удалите модуль AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Установка модуля Az](/powershell/azure/install-az-ps)
1. Обновление Visual Studio до версии 16.4
1. (Обновите скрипт развертывания в проекте.)

## <a name="update-visual-studio-to-164"></a>Обновление Visual Studio до версии 16.4

Обновите установку Visual Studio до версии 16.4 или позже. Во время обновления убедитесь, что компонент Azure PowerShell не проверяется. Поскольку модуль Az был установлен через галерею, вы не хотите переустанавливать модуль AzureRM.

Если вы уже обновили до 16,4 и компонент Azure PowerShell был проверен, вы можете удалить его, запустив установку Visual Studio. Не выбирайте компонент Azure PowerShell в рабочей нагрузке Azure или на странице отдельных компонентов.

## <a name="update-the-deployment-script-in-your-project"></a>Обновление скрипта развертывания в проекте

Замените все вхождения строки "AzureRm" на "Az" в скрипте развертывания. Чтобы увидеть изменения, обратитесь к исправлениям в этом [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619). Для получения дополнительной информации об обновлении скриптов в модуль Az [см.](/powershell/azure/migrate-from-azurerm-to-az)

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать об использовании проекта Visual Studio, [см. Создание и развертывание проектов группы ресурсов Azure через Visual Studio.](create-visual-studio-deployment-project.md)
