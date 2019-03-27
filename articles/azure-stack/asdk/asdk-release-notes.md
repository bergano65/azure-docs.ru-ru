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
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 33f1ccf3f1c7bc657cc66efe7c5025356c954ad6
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58187767"
---
# <a name="asdk-release-notes"></a>Заметки о выпуске ASDK

Эта статья содержит сведения об изменениях, исправлениях ошибок и известных проблемах в Пакете средств разработки Azure Stack (ASDK). Если вы не знаете, какая версия используется, проверьте ее [с помощью портала](../azure-stack-updates.md#determine-the-current-version).

Будьте в курсе новых возможностей ASDK, подписавшись на этот [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [веб-канал](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11902069"></a>Сборка 1.1902.0.69

### <a name="new-features"></a>новые функции;

- В сборке 1902 для портала администрирования Azure Stack представлен новый пользовательский интерфейс для создания планов, предложений, квот и дополнительных планов. Дополнительные сведения, включая снимки экрана, см. в статье [Создание плана в Azure Stack](../azure-stack-create-plan.md).

- Список других изменений и усовершенствований для этого выпуска см. в [этом разделе](../azure-stack-update-1902.md#improvements) заметок о выпуске Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Известные проблемы

- Обнаружена проблема, из-за которой отбрасываются пакеты размером более 1450 байт для внутреннего балансировщика нагрузки (ILB). Проблема связана с тем, что значение параметра MTU на узле слишком низкое для размещения инкапсулированных пакетов VXLAN, проходящих роль, которая в сборке 1901 была перемещена на узел. Есть как минимум два сценария, при работе с которыми наблюдается проявление этой проблемы:

  - SQL-запросы к группам доступности SQL Always On, находящиеся за внутренним балансировщиком нагрузки (ILB), размером более 660 байт.
  - Возникновение ошибки во время развертывания Kubernetes при попытке включить несколько главных экземпляров.  

  Эта проблема возникает при наличии связи между виртуальной машиной и внутренним балансировщиком нагрузки в той же виртуальной сети, но в разных подсетях. Вы можете обойти эту проблему, выполнив следующие команды в командной строке с повышенными привилегиями на узле ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```

## <a name="build-11901095"></a>Сборка 1.1901.0.95

Дополнительные сведения см. в разделе [Указание сборки](../azure-stack-update-1901.md#build-reference).

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
