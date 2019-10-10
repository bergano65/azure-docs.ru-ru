---
title: Конфигурация на основе Стиг для использования в конфигурации состояния — служба автоматизации Azure
description: Сведения о конфигурациях, основанных на Стиг, для настройки состояния в службе автоматизации Azure.
keywords: DSC, PowerShell, Настройка, Настройка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 685b6bda09026e64154590afd66bdfbec43b8b1e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243524"
---
# <a name="configuration-based-on-stig"></a>Использование конфигурации на основе STIG

> Область применения: Windows PowerShell 5.1

Создание содержимого конфигурации в первый раз может быть непростой задачей.
Во многих случаях цель состоит в том, чтобы автоматизировать настройку серверов после "базовой", которая, надеюсь, соответствует отраслевым рекомендациям.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-powerstig"></a>Проект сообщества: поверстиг

Проект сообщества с именем [поверстиг](https://github.com/microsoft/powerstig) предназначен для решения этой проблемы путем создания содержимого DSC на основе [общедоступной информации](https://public.cyber.mil/stigs/) о Стиг (руководству по технической реализации по безопасности).

Работа с базовыми показателями сложнее, чем она звучит.
Многим организациям необходимо [документировать исключения](https://github.com/microsoft/powerstig#powerstigdata) в правилах и управлять этими данными в масштабе.
Поверстиг решает проблему, предоставляя [Составные ресурсы](https://github.com/microsoft/powerstig#powerstigdsc) для решения каждой области конфигурации, не пытаясь обращаться ко всему диапазону параметров в одном большом файле.

После создания конфигураций можно использовать [сценарии конфигурации DSC](/powershell/scripting/dsc/configurations/configurations) для создания MOF-файлов и [передачи MOF-файлов в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.

Чтобы испытать Поверстиг, перейдите на [коллекция PowerShell](http://www.powershellgallery.com) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/microsoft/powerstig).

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о платформе Desired State Configuration Windows PowerShell](/powershell/dsc/overview/overview)
- [Ресурсы DSC](/powershell/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
