---
title: Обновление скрипта развертывания шаблона Visual Studio для использования Az Powershell
description: Обновление скрипта развертывания шаблона Visual Studio из AzureRM в Az Powershell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149065"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Обновление скрипта развертывания шаблона Visual Studio для использования Az Powershell

Visual Studio 16,4 поддерживает использование Az PowerShell в скрипте развертывания шаблона. Visual Studio не устанавливает Az PowerShell и не обновляет автоматически скрипт развертывания в проекте группы ресурсов. Для использования более новой версии Az PowerShell необходимо выполнить следующие четыре действия:
1. удаление AzureRM PowerShell;
1. установка Az Powershell;
1. обновление до Visual Studio 16,4;
1. (Обновите скрипт развертывания в проекте.)

## <a name="uninstall-azurerm-powershell"></a>удаление AzureRM PowerShell;
(Чтобы удалить AzureRM PowerShell, выполните следующие [инструкции](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module).)

## <a name="install-az-powershell"></a>установка Az Powershell;
(Чтобы установить Az PowerShell, выполните следующие [инструкции](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0).)

## <a name="update-visual-studio-to-164"></a>Обновление Visual Studio до версии 16.4
Обновите до Visual Studio 16,4, когда обновление будет доступно. Во время обновления убедитесь, что компонент Azure PowerShell не установлен. Поскольку вы установили Az Powershell через галерею, вам не нужна версия AzureRM Powershell, устанавливаемая в Visual Studio.

Если вы уже обновили до версии 16,4 и компонент Azure PowerShell был установлен, его можно удалить, запустив Visual Studio Installer и отменив этот компонент в рабочей нагрузке Azure или на странице отдельных компонентов.

## <a name="update-the-deployment-script-in-your-project"></a>Обновление скрипта развертывания в проекте
Замените все вхождения строки "AzureRm" на "Az" в скрипте развертывания. Чтобы увидеть изменения, обратитесь к исправлениям в этом [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619). Дополнительные сведения об обновлении скриптов для Az PowerShell см. в этой [Документации](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0).


