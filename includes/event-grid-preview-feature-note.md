---
title: включить файл
description: включить файл
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028426"
---
Эта функция предоставляется в предварительной версии. Для этого нужно установить расширение предварительной версии или модуль.

### <a name="install-extension-for-azure-cli"></a>Установка расширения для Azure CLI

Для Azure CLI вам потребуется [расширение Сетки событий](/cli/azure/azure-cli-extensions-list).

В [CloudShell](../articles/cloud-shell/quickstart.md):

* Если вы установили расширение ранее, обновите его `az extension update -n eventgrid`.
* Если расширение еще не установлено, установите его `az extension add -n eventgrid`.

При локальной установке:

1. [Установка Azure CLI](/cli/azure/install-azure-cli). С помощью `az --version` проверьте, что установлена последняя версия.
1. Удалите предыдущие версии расширения `az extension remove -n eventgrid`.
1. Установка расширения `eventgrid` с помощью `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Установка модуля для PowerShell

Для PowerShell вам потребуется [модуль AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

В [CloudShell](../articles/cloud-shell/quickstart-powershell.md):

* Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

При локальной установке:

1. Откройте консоль PowerShell от имени администратора.
1. Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Если параметр `-AllowPrerelease` недоступен, выполните следующие действия:

1. Выполнить `Install-Module PowerShellGet -Force`
1. Выполнить `Update-Module PowerShellGet`
1. Закройте консоль PowerShell.
1. Перезапустите PowerShell от имени администратора.
1. Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.