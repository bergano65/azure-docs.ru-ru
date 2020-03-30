---
title: Конфигурация на основе STIG для использования в конфигурации состояния - Azure Automation
description: Узнайте о конфигурациях, основанных на STIG для конфигурации состояния в Azure Automation.
keywords: dsc,powershell,конфигурация,установка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 275b3bd25f931b73e8a378433899ef9ade4d47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028276"
---
# <a name="configuration-based-on-stig"></a>Использование конфигурации на основе STIG

> Применяется к: Windows PowerShell 5.1

Создание содержимого конфигурации впервые может оказаться непростой задачей.
Во многих случаях цель заключается в автоматизации конфигурации серверов после "базовой линии", которая, как мы надеемся, соответствует рекомендации отрасли.

> [!NOTE]
> Эта статья относится к решению, которое поддерживается сообществом open Source.
> Поддержка доступна только в форме совместной работы GitHub, а не от корпорации Майкрософт.

## <a name="community-project-powerstig"></a>Общественный проект: PowerSTIG

Общинный проект под названием [PowerSTIG](https://github.com/microsoft/powerstig) направлен на решение этой проблемы путем создания контента DSC на основе [публичной информации](https://public.cyber.mil/stigs/) о STIG (Руководство по технической реализации безопасности),

Работа с базовыми линиями сложнее, чем кажется.
Многим организациям необходимо [документировать исключения](https://github.com/microsoft/powerstig#powerstigdata) из правил и управлять этими данными в масштабе.
PowerSTIG решает проблему, предоставляя [композитные ресурсы](https://github.com/microsoft/powerstig#powerstigdsc) для решения каждой области конфигурации, а не пытаясь решить весь диапазон параметров в одном большом файле.

После создания конфигураций можно использовать [скрипты Конфигурации DSC](/powershell/scripting/dsc/configurations/configurations) для генерации файлов MOF и [загрузки файлов MOF в Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Затем зарегистрируйте серверы либо в [помещении,](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) либо [в Azure,](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) чтобы вытащить конфигурации.

Чтобы опробовать PowerSTIG, посетите [галерею PowerShell](https://www.powershellgallery.com) и загрузите решение или нажмите "Сайт проекта", чтобы просмотреть [документацию.](https://github.com/microsoft/powerstig)

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального менеджера конфигурации](/powershell/scripting/dsc/managing-nodes/metaconfig)
