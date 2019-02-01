---
title: Использование профилей версий API и PowerShell в Azure Stack | Документация Майкрософт
description: Сведения об использовании профилей версий API и PowerShell в Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: c6bee5c66661f59a6287d624bf5a55428ebbe56e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238124"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Использование профилей версий API и PowerShell в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Профили версий API позволяют управлять различиями между версиями Azure и Azure Stack. Профиль версии API — это набор модулей AzureRM PowerShell с определенными версиями API. Каждая облачная платформа имеет набор поддерживаемых профилей версий API. Например, Azure Stack поддерживает версию профиля на определенную дату, например **2018-03-01-hybrid**, а Azure поддерживает профиль API **последней** версии. При установке профиля устанавливается набор модулей AzureRM PowerShell, которые соответствуют выбранному профилю.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Установка модуля PowerShell, необходимого для использования профилей версий API

Модуль **AzureRM.Bootstrapper**, доступный в коллекции PowerShell, предоставляет командлеты PowerShell, необходимые для работы с профилями версий API. Чтобы установить модуль **AzureRM.Bootstrapper**, используйте следующий командлет:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Версия Azure Stack и версии профиля

В таблице перечислены необходимые версии профиля API и моникер модуля администратора PowerShell, используемые в последних версиях Azure Stack. Если вы читаете эту статью с версией до 1808, обновите профиль версии и моникер, указав правильные значения.

| Номер версии | Профиль версии API | Моникер модуля администрирования PS |
| --- | --- | --- |
| 1811 или более поздней версии | 2018-03-01-hybrid | 1.6.0 |
| 1808 или более поздняя | 2018-03-01-hybrid | 1.5.0 |
| 1804 или более поздняя | 2017-03-09-profile | 1.4.0 |
| Версии до 1804 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> Чтобы выполнить обновление с версии 1.2.11, прочитайте [руководство по миграции](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Установка профиля

Чтобы установить модули AzureRM, необходимые для Azure Stack, используйте командлет **Install-AzureRmProfile** с профилем версии API **2018-03-01-hybrid**. Модули для оператора Azure Stack не устанавливаются с этим профилем версии API. Их нужно устанавливать отдельно, как указано в шаге 3 статьи [Установка PowerShell для Azure Stack](../azure-stack-powershell-install.md).

```PowerShell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Установка и импорт модулей в профиль

Используйте командлет **Use-AzureRmProfile**, чтобы установить и импортировать модули, связанные с профилем версии API. Для сеанса PowerShell можно импортировать только один профиль версии API. Чтобы импортировать другой профиль версии API, необходимо открыть новый сеанс PowerShell. Командлет **Use-AzureRMProfile** выполняет следующие задачи:

1. Проверяет в текущей области наличие модулей PowerShell, связанных с определенным профилем версии API.  
2. Скачивает и устанавливает модули, которые еще не установлены.
3. Импортирует модули для текущего сеанса PowerShell.

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Чтобы установить и импортировать выбранные модули AzureRM из профиля версии API, выполните командлет **Use-AzureRMProfile** с параметром **Module**:

```PowerShell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Получение установленных профилей

Используйте командлет **Get-AzureRmProfile**, чтобы получить список доступных профилей версий API.

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Обновление профилей

Используйте командлет **Update-AzureRmProfile** для обновления модулей в профиле версии API до последней версии, доступной в PSGallery. Рекомендуется всегда запускать командлет **Update-AzureRmProfile** в новом сеансе PowerShell, чтобы избежать конфликтов при импорте модулей. Командлет **Update-AzureRmProfile** выполняет следующие задачи.

1. Проверяет в текущей области, установлены ли последние версии модулей в определенном профиле версии API.  
2. Предлагает установить их, если они еще не установлены.  
3. Устанавливает и импортирует обновленные модули для текущего сеанса PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```PowerShell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

Этот командлет выполняет следующие задачи.  

1. Проверяет в текущей области, установлены ли последние версии модулей в определенном профиле версии API.  
2. Удаляет устаревшие версии модулей из текущего профиля версии API и текущего сеанса PowerShell.  
3. Предлагает установить последнюю версию.  
4. Устанавливает и импортирует обновленные модули для текущего сеанса PowerShell.  

## <a name="uninstall-profiles"></a>Удаление профилей

Чтобы удалить определенный профиль версии API, используйте командлет **Uninstall-AzureRmProfile**.

```PowerShell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Дополнительная информация

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Установка PowerShell для Azure Stack)
* [Configure the Azure Stack user's PowerShell environment](azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack)  
