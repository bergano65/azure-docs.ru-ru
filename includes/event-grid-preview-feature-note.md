---
title: включение файла
description: включение файла
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814722"
---
Эта функция предоставляется в предварительной версии. Для этого нужно установить расширение предварительной версии или модуль.

### <a name="install-extension-for-azure-cli"></a>Установка расширения для Azure CLI

Для Azure CLI вам потребуется [расширение Сетки событий](/cli/azure/azure-cli-extensions-list).

В [CloudShell](/azure/cloud-shell/quickstart):

* Если вы установили расширение ранее, обновите его `az extension update -n eventgrid`.
* Если расширение еще не установлено, установите его `az extension add -n eventgrid`.

При локальной установке:

1. [Установка Azure CLI](/cli/azure/install-azure-cli). Убедитесь, что у вас есть последняя версия, путем проверки с `az --version`.
1. Удалите предыдущие версии расширения `az extension remove -n eventgrid`.
1. Установка `eventgrid` расширения с помощью `az extension add -n eventgrid`

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
