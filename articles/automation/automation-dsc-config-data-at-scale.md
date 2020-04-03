---
title: Данные конфигурации в масштабе - Автоматизация Azure
description: Узнайте, как настроить данные в масштабе для конфигурации состояния в Azure Automation.
keywords: dsc,powershell,конфигурация,установка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 644ea1c00af7e71ff56852298fff18e5293c137b
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585572"
---
# <a name="configuration-data-at-scale"></a>Использование данных конфигурации в нужном масштабе

> Применяется к: Windows PowerShell 5.1

Управление сотнями или тысячами серверов может быть сложной задачей.
Клиенты предоставили обратную связь, что наиболее сложным аспектом является управление [данными конфигурации.](/powershell/scripting/dsc/configurations/configdata)
Организация информации в логических конструкциях, таких как местоположение, тип и среда.

> [!NOTE]
> Эта статья относится к решению, которое поддерживается сообществом open Source.
> Поддержка доступна только в форме совместной работы GitHub, а не от корпорации Майкрософт.

## <a name="community-project-datum"></a>Общественный проект: Datum

Для решения этой проблемы было создано решение под названием [Datum.](https://github.com/gaelcolas/Datum)
Datum основывается на отличных идеях других платформ управления конфигурацией и реализует тот же тип решения для PowerShell DSC.
Информация [организована в текстовых файлах](https://github.com/gaelcolas/Datum#3-intended-usage) на основе логических идей.
Пример будет следующим:

- Настройки, которые должны применяться во всем мире
- Настройки, которые должны применяться ко всем серверам в месте
- Настройки, которые должны применяться ко всем серверам баз данных
- Индивидуальные настройки сервера

Эта информация организована в формате файла, который вы предпочитаете (JSON, Yaml или PSD1).
Затем cmdlets предоставляются для генерации файлов данных конфигурации путем [консолидации информации](https://github.com/gaelcolas/Datum#datum-tree) из каждого файла в одном представлении роли сервера или сервера.

После того, как файлы данных были созданы, их можно использовать со [скриптами Конфигурации DSC](/powershell/scripting/dsc/configurations/write-compile-apply-configuration) для генерации файлов MOF и [загрузки файлов MOF в Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Затем зарегистрируйте серверы либо в [помещении,](/azure/automation/automation-dsc-onboarding#onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) либо [в Azure,](/azure/automation/automation-dsc-onboarding#onboarding-azure-vms) чтобы вытащить конфигурации.

Чтобы опробовать Datum, посетите [галерею PowerShell](https://www.powershellgallery.com/packages/datum/) и загрузите решение или нажмите "Сайт проекта", чтобы просмотреть [документацию.](https://github.com/gaelcolas/Datum#2-getting-started--concepts)

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального менеджера конфигурации](/powershell/scripting/dsc/managing-nodes/metaconfig)
