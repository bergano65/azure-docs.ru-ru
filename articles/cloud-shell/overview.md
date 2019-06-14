---
title: Обзор Azure Cloud Shell | Документация Майкрософт
description: Обзор Azure Cloud Shell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: 5608b3e0f9b98db62d22245de5a864f757f48799
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199693"
---
# <a name="overview-of-azure-cloud-shell"></a>Обзор Azure Cloud Shell
Azure Cloud Shell — это интерактивная доступная браузеру оболочка для управления ресурсами Azure.
Она предоставляет гибкие возможности при выборе оболочки, соответствующей вашим методам работы.
Пользователи Linux могут использовать Bash, а пользователи Windows — PowerShell.

Выполните запуск через shell.azure.com, нажав кнопку ниже.

[![Встроенный запуск](https://shell.azure.com/images/launchcloudshell.png "Запуск Azure Cloud Shell")](https://shell.azure.com)

Выполните запуск через портал Azure с помощью значка Cloud Shell.

![Запуск с помощью портала](media/overview/portal-launch-icon.png)

## <a name="features"></a>Функции

### <a name="browser-based-shell-experience"></a>Оболочка на основе браузера
Cloud Shell предоставляет доступ к браузерному интерфейсу командной строки, созданному с учетом задач управления Azure.
Используйте Cloud Shell для работы из локального компьютера с неограниченными возможностями, которые может обеспечить только облако.

### <a name="choice-of-preferred-shell-experience"></a>Выбор предпочтительной оболочки
Пользователи могут выбрать Bash или PowerShell из раскрывающегося списка оболочек.

![Bash в Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell в Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Настроенная и аутентифицированная рабочая станция Azure
Службой Cloud Shell управляет Майкрософт, поэтому в ней присутствуют популярные программы командной строки и реализована поддержка различных языков. Кроме того, Cloud Shell автоматически безопасно проходит проверку подлинности, тем самым обеспечивая мгновенный доступ к ресурсам с помощью Azure CLI или командлетов Azure PowerShell.

Просмотрите полный список [средств, установленных в Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Интегрированный редактор Cloud Shell
Cloud Shell предоставляет интегрированный графический текстовый редактор на основе редактора Monaco с открытым кодом. Просто создайте и измените файлы конфигурации, выполнив `code .` для размеренного развертывания с помощью Azure CLI 2.0 или Azure PowerShell.

[Подробнее о редакторе Cloud Shell.](using-cloud-shell-editor.md)

### <a name="integrated-with-docsmicrosoftcom"></a>Интеграция с docs.microsoft.com

Cloud Shell можно использовать непосредственно из документации, размещенной на сайте [docs.microsoft.com](https://docs.microsoft.com). Эта оболочка интегрирована с документацией по [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) и [Azure CLI](https://docs.microsoft.com/cli/azure). Нажмите кнопку "Попробуйте!" в блоке с фрагментом кода, чтобы открыть иммерсивный интерфейс оболочки. 

### <a name="multiple-access-points"></a>Доступность в нескольких расположениях
Cloud Shell представляет собой гибкий инструмент, который можно использовать с помощью таких элементов:
* [portal.azure.com](https://portal.azure.com);
* [shell.azure.com](https://shell.azure.com);
* [документации по Azure CLI](https://docs.microsoft.com/cli/azure).
* [Документация по Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [мобильные приложения Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/);
* [Расширение учетной записи Azure для Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

### <a name="connect-your-microsoft-azure-files-storage"></a>Подключение хранилища файлов Microsoft Azure
Компьютеры Cloud Shell временные и требуют подключения новой или существующей общей папки Azure в качестве `clouddrive` для хранения файлов.

При первом запуске Cloud Shell предлагает создать группу ресурсов, учетную запись хранения и файловый ресурс Azure от вашего имени. Это одноразовое действие, которое автоматически применяется для всех сеансов. Можно сопоставить отдельный файловый ресурс, который будет использоваться Bash и PowerShell в Cloud Shell.

Дополнительные сведения о подключении [новой или существующей учетной записи хранения](persisting-shell-storage.md).

## <a name="concepts"></a>Основные понятия
* Cloud Shell работает на временном узле, предоставляемом для каждого сеанса и для каждого пользователя отдельно.
* Время ожидания Cloud Shell истекает через 20 минут при отсутствии интерактивных действий.
* Для Cloud Shell требуется подключение файлового ресурса Azure.
* Cloud Shell использует один и тот же файловый ресурс Azure для Bash и PowerShell.
* Cloud Shell назначается один компьютер на учетную запись пользователя.
* Cloud Shell сохраняет каталог $Home с помощью образа размером 5 ГБ, размещенного в общей папке.
* Разрешения задаются как для обычного пользователя Linux в Bash.

Узнайте больше о возможностях [Bash в Cloud Shell](features.md) и [PowerShell в Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Цены
За использование компьютера, на котором размещена среда Cloud Shell, плата не взимается. На этом компьютере должен быть подключен файловый ресурс Azure. Применяются расходы на обычное хранение.

## <a name="next-steps"></a>Дальнейшие действия
[Краткое руководство по использованию Bash в Azure Cloud Shell](quickstart.md) <br>
[Краткое руководство по использованию PowerShell в Azure Cloud Shell (предварительная версия)](quickstart-powershell.md)
