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
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285768"
---
Эта функция предоставляется в предварительной версии. Для этого нужно установить расширение предварительной версии или модуль.

### <a name="install-extension-for-azure-cli"></a>Установка расширения для Azure CLI

Для Azure CLI вам потребуется [расширение Сетки событий](/cli/azure/azure-cli-extensions-list).

В [CloudShell](/azure/cloud-shell/quickstart):

* Если вы установили расширение ранее, обновите его `az extension update -n eventgrid`.
* Если расширение еще не установлено, установите его `az extension add -n eventgrid`.

При локальной установке:

1. Удалите Azure CLI локально.
1. Установите [последнюю версию](/cli/azure/install-azure-cli) Azure CLI.
1. Запустите командное окно.
1. Удалите предыдущие версии расширения `az extension remove -n eventgrid`.
1. Установите расширение `az extension add -n eventgrid`.

### <a name="install-module-for-powershell"></a>Установка модуля для PowerShell

Для PowerShell вам потребуется [модуль AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

В [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

При локальной установке:

1. Откройте консоль PowerShell от имени администратора.
1. Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.

Если параметр `-AllowPrerelease` недоступен, выполните следующие действия:

1. Запустите `Install-Module PowerShellGet -Force`
1. Запустите `Update-Module PowerShellGet`
1. Закройте консоль PowerShell.
1. Перезапустите PowerShell от имени администратора.
1. Установите модуль `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`.
