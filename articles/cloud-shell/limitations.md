---
title: Ограничения Azure Cloud Shell | Документация Майкрософт
description: Обзор ограничений Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951485"
---
# <a name="limitations-of-azure-cloud-shell"></a>Ограничения Azure Cloud Shell

Azure Cloud Shell имеет следующие известные ограничения:

## <a name="general-limitations"></a>Общие ограничения

### <a name="system-state-and-persistence"></a>Состояние системы и сохраняемость

Компьютер, предоставляющий сеанс Cloud Shell, является временным и перезапускается, если сеанс не используется в течение 20 минут. Для Cloud Shell требуется подключение файлового ресурса Azure. Поэтому ваша подписка должна иметь возможность настраивать ресурсы хранилища для доступа к Cloud Shell. Дополнительные рекомендации:

* Если подключено хранилище, то сохраняются только изменения в каталоге `$Home`.
* Файловые ресурсы Azure можно подключить только в пределах [назначенного региона](persisting-shell-storage.md#mount-a-new-clouddrive).
  * В Bash выполните команду `env`, чтобы найти регион, заданный для `ACC_LOCATION`.

### <a name="browser-support"></a>Поддержка браузеров

Cloud Shell поддерживает последние версии Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox и Apple Safari. Использование Safari в режиме защищенного просмотра не поддерживается.

### <a name="copy-and-paste"></a>Копирование и вставка

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Для заданного пользователя может быть активна только одна оболочка.

Пользователи могут запускать только один тип оболочки одновременно, **Bash** или **PowerShell**. Однако можно запустить несколько экземпляров Bash или PowerShell одновременно. Замена между Bash или PowerShell с помощью меню приводит к перезапуску Cloud Shell, что завершает существующие сеансы. Кроме того, вы можете запустить Баш `bash`внутри PowerShell, введя, и `pwsh`вы можете запустить PowerShell внутри Баш, введя .

### <a name="usage-limits"></a>Ограничения использования

Cloud Shell предназначен для интерактивного использования. В результате любые длительные неинтерактивные сеансы завершаются без предупреждения.

## <a name="bash-limitations"></a>Ограничения Bash

### <a name="user-permissions"></a>Разрешения пользователя

Разрешения задаются как для обычных пользователей без доступа к sudo. Все, что устанавливается за пределами каталога `$Home`, не сохраняется.

### <a name="editing-bashrc-or-profile"></a>Редактирование .bashrc или $PROFILE

Будьте осторожны при редактировании .bashrc или $PROFILE файла PowerShell, это может привести к неожиданным ошибкам в Cloud Shell.

## <a name="powershell-limitations"></a>Ограничения PowerShell

### <a name="azuread-module-name"></a>Имя модуля `AzureAD`

В настоящее время для модуля `AzureAD` указано имя `AzureAD.Standard.Preview`, но он предоставляет точно такие же функциональные возможности.

### <a name="sqlserver-module-functionality"></a>Функции модуля `SqlServer`

Модуль `SqlServer`, включенный в Cloud Shell, поддерживает PowerShell Core только в режиме предварительного выпуска. В частности, пока недоступно `Invoke-SqlCmd`.

### <a name="default-file-location-when-created-from-azure-drive"></a>Расположение файла по умолчанию при создании с помощью диска Azure:

Используя смдлеты PowerShell, пользователи не могут создавать файлы под Azure: диск. Когда пользователи создают новые файлы с помощью других инструментов, например vim или nano, по умолчанию файлы сохраняются в папке `$HOME`. 

### <a name="gui-applications-are-not-supported"></a>Приложения с графическим пользовательским интерфейсом не поддерживаются

Если пользователь запускает команду, которая создаст диалоговую коробку Windows, `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`можно увидеть сообщение об ошибке, такое как: .

### <a name="large-gap-after-displaying-progress-bar"></a>Большое пустое пространство после отображения индикатора выполнения

Если пользователь выполняет действие, отображаев панель прогресса, например, завершаемый вкладкой в `Azure:` диске, возможно, курсор не установлен должным образом и появляется пробел, где ранее находилась панель прогресса.

## <a name="next-steps"></a>Дальнейшие действия

[Устранение неполадок Cloud Shell](troubleshooting.md) <br>
[Краткое руководство по Bash](quickstart.md) <br>
[Краткое руководство по PowerShell](quickstart-powershell.md)
