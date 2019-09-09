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
ms.openlocfilehash: b8c39ba6c12d43da1b2311ae4d7d85dd13946f25
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559627"
---
# <a name="create-configurations-from-existing-servers"></a>Создание конфигураций на основе существующих серверов

> Область применения. Windows PowerShell 5.1

Создание конфигураций из существующих серверов может оказаться сложной задачей.
Может потребоваться не *все* параметры, а только те, которые вас интересуют.
Даже затем необходимо знать, в каком порядке должны быть применены параметры, чтобы конфигурация была успешно применена.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-reversedsc"></a>Проект сообщества: реверседск

Для работы в этой области, запускающей SharePoint, было создано решение для сообщества с именем [реверседск](https://github.com/microsoft/reversedsc) .

Решение строится на [ресурсе шарепоинтдск](https://github.com/powershell/sharepointdsc) и расширяет его для координации [сбора информации](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) с существующих серверов SharePoint.
Последняя версия имеет несколько [режимов извлечения](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) для определения того, какой уровень информации следует включить.

Результатом использования решения является создание [данных конфигурации](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) для использования с сценариями конфигурации шарепоинтдск.

После создания файлов данных их можно использовать с [сценариями конфигурации DSC](/powershell/dsc/overview/overview) для создания MOF-файлов и [передачи MOF-файлов в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.

Чтобы испытать Реверседск, перейдите на [коллекция PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/Microsoft/sharepointDSC.reverse).

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о платформе Desired State Configuration Windows PowerShell](/powershell/dsc/overview/overview)
- [Ресурсы DSC](/powershell/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
