---
title: Создание конфигураций с существующих серверов - Azure Automation
description: Узнайте, как создавать конфигурации из существующих серверов для Azure Automation.
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
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585545"
---
# <a name="create-configurations-from-existing-servers"></a>Создание конфигураций с существующих серверов

> Применяется к: Windows PowerShell 5.1

Создание конфигураций с существующих серверов может быть сложной задачей.
Возможно, вам не нужны *все* настройки, только те, которые вас волнуют.
Даже тогда необходимо знать, в каком порядке необходимо применять настройки для успешного применения конфигурации.

> [!NOTE]
> Эта статья относится к решению, которое поддерживается сообществом open Source.
> Поддержка доступна только в форме совместной работы GitHub, а не от корпорации Майкрософт.

## <a name="community-project-reversedsc"></a>Проект сообщества: ReverseDSC

Для работы в этой области, начинающимСя SharePoint, создано сообщество, поддерживаемое решение [мейнс-Хесс.](https://github.com/microsoft/reversedsc)

Решение основано на [ресурсе SharePointDSC](https://github.com/powershell/sharepointdsc) и расширяет его для организации [сбора информации](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use) с существующих серверов SharePoint.
Последняя версия имеет несколько [режимов извлечения,](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes) чтобы определить, какой уровень информации, чтобы включить.

Результатом использования решения является генерация [конфигурационных данных,](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data) которые будут использоваться со скриптами конфигурации SharePointDSC.

После того, как файлы данных были созданы, их можно использовать со [скриптами Конфигурации DSC](/powershell/scripting/dsc/overview/overview) для генерации файлов MOF и [загрузки файлов MOF в Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Затем зарегистрируйте серверы либо в [помещении,](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) либо [в Azure,](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) чтобы вытащить конфигурации.

Чтобы опробовать ReverseDSC, посетите [галерею PowerShell](https://www.powershellgallery.com/packages/ReverseDSC/) и загрузите решение или нажмите "Сайт проекта", чтобы просмотреть [документацию.](https://github.com/Microsoft/sharepointDSC.reverse)

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального менеджера конфигурации](/powershell/scripting/dsc/managing-nodes/metaconfig)
