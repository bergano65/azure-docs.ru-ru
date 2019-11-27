---
title: Создание конфигураций из существующих серверов — служба автоматизации Azure
description: Узнайте, как создавать конфигурации из существующих серверов для службы автоматизации Azure.
keywords: DSC, PowerShell, Настройка, Настройка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 35f967e946854c3ca097db379015a7ee0bbe2f3d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231687"
---
# <a name="create-configurations-from-existing-servers"></a>Создание конфигураций на основе существующих серверов

> Область применения: Windows PowerShell 5,1

Создание конфигураций из существующих серверов может оказаться сложной задачей.
Может потребоваться не *все* параметры, а только те, которые вас интересуют.
Даже затем необходимо знать, в каком порядке должны быть применены параметры, чтобы конфигурация была успешно применена.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-reversedsc"></a>Проект сообщества: Реверседск

Для работы в этой области, запускающей SharePoint, было создано решение для сообщества с именем [реверседск](https://github.com/microsoft/reversedsc) .

Решение строится на [ресурсе шарепоинтдск](https://github.com/powershell/sharepointdsc) и расширяет его для координации [сбора информации](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) с существующих серверов SharePoint.
Последняя версия имеет несколько [режимов извлечения](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) для определения того, какой уровень информации следует включить.

Результатом использования решения является создание [данных конфигурации](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) для использования с сценариями конфигурации шарепоинтдск.

После создания файлов данных их можно использовать с [сценариями конфигурации DSC](/powershell/scripting/dsc/overview/overview) для создания MOF-файлов и [передачи MOF-файлов в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.

Чтобы испытать Реверседск, перейдите на [коллекция PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения о платформе Desired State Configuration Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
