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
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "66814722"
---
Эта функция предоставляется в предварительной версии. Для этого нужно установить расширение предварительной версии или модуль.

### <a name="install-extension-for-azure-cli"></a>Установка расширения для Azure CLI

Для Azure CLI вам потребуется [расширение Сетки событий](/cli/azure/azure-cli-extensions-list).

В [CloudShell](/azure/cloud-shell/quickstart):

* Если вы установили расширение ранее, обновите его `az extension update -n eventgrid`.
* Если расширение еще не установлено, установите его `az extension add -n eventgrid`.

При локальной установке:

1. [Установите Azure CLI](/cli/azure/install-azure-cli). Убедитесь, что у вас установлена последняя версия, путем проверки `az --version`.
1. Удалите предыдущие версии расширения `az extension remove -n eventgrid`.
1. Установите `eventgrid` расширение с помощью`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Установка модуля для PowerShell

Для PowerShell вам потребуется [модуль AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

В [CloudShell](/azure/cloud-shell/quickstart-powershell):

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
