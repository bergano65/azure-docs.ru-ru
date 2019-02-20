---
title: Заметки к выпуску Пакета средств разработки Azure Stack | Документация Майкрософт
description: Улучшения, исправления и известные проблемы Пакета средств разработки Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 02/09/2019
ms.openlocfilehash: 485d88a4765d7cedcb171a5b325fe5f366fff1f9
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004781"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](../azure-stack-updates.md#determine-the-current-version).

Будьте в курсе новых возможностей ASDK, подписавшись на этот [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11901095"></a>Сборка 1.1901.0.95

### <a name="changes"></a>изменения

Эта сборка включает в себя следующие улучшения для Azure Stack.

- Компоненты преобразования сетевых адресов (NAT) и BGP теперь развернуты на физическом узле. Это избавляет от необходимости иметь два общедоступных или корпоративных IP-адреса для развертывания ASDK, а также упрощает развертывание.
- Резервные копии интегрированных систем Azure Stack теперь можно [проверить](asdk-validate-backup.md) с помощью сценария PowerShell **asdk-installer.ps1**.

### <a name="new-features"></a>новые функции;

- Список новых функций для этого выпуска см. в [этом разделе](../azure-stack-update-1901.md#new-features) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

- Список исправленных проблем для этого выпуска см. в [этом разделе](../azure-stack-update-1901.md#fixed-issues) заметок к выпуску Azure Stack. Список известных проблем см. в [этом разделе](../azure-stack-update-1901.md#known-issues-post-installation).
- Обратите внимание, что [доступные исправления Azure Stack](../azure-stack-update-1901.md#azure-stack-hotfixes) нельзя применять к Azure Stack ASDK.

## <a name="build-118110101"></a>Сборка 1.1811.0.101

### <a name="changes"></a>изменения

Эта сборка включает в себя следующие улучшения для Azure Stack.  

- Существует новый набор требований к минимальной и рекомендуемой конфигурациям оборудования и программного обеспечения для ASDK. Эти новые рекомендуемые спецификации описаны в разделе [Рекомендации по планированию развертывания Azure Stack](asdk-deploy-considerations.md). С развитием платформы Azure Stack стали доступны дополнительные службы. Поэтому могут потребоваться дополнительные ресурсы. Повышенные спецификации отражают эти измененные рекомендации.

### <a name="new-features"></a>новые функции;

Список новых функций для этого выпуска см. в [этом разделе](../azure-stack-update-1811.md#new-features) заметок к выпуску Azure Stack.

### <a name="fixed-and-known-issues"></a>Исправленные и известные проблемы

Список исправленных проблем для этого выпуска см. в [этом разделе](../azure-stack-update-1811.md#fixed-issues) заметок к выпуску Azure Stack. Список известных проблем см. в [этом разделе](../azure-stack-update-1811.md#known-issues-post-installation).

## <a name="build-11809090"></a>Сборка 1.1809.0.90

### <a name="new-features"></a>новые функции;

Список новых функций для этого выпуска см. в [этом разделе](../azure-stack-update-1809.md#new-features) заметок к выпуску Azure Stack.

### <a name="fixed-issues"></a>Исправленные проблемы

Список исправленных проблем для этого выпуска см. в [этом разделе](../azure-stack-update-1809.md#fixed-issues).

### <a name="known-issues"></a>Известные проблемы

Список известных проблем для этого выпуска см. в [этом разделе](../azure-stack-update-1809.md#known-issues-post-installation).