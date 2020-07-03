---
title: Создание конфигураций из существующих серверов — служба автоматизации Azure
description: Узнайте, как создавать конфигурации из существующих серверов для службы автоматизации Azure.
keywords: dsc,powershell,конфигурация,установка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dff9b8f52207a38cf7eaddefa178aff262ddc546
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80585545"
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
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) среды или [в Azure](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) для извлечения конфигураций.

Чтобы испытать Реверседск, перейдите на [коллекция PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Дальнейшие шаги

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig)
