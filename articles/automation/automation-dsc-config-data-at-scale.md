---
title: Данные конфигурации в масштабе — служба автоматизации Azure
description: Узнайте, как настроить масштабируемые данные для конфигурации состояния в службе автоматизации Azure.
keywords: DSC, PowerShell, Настройка, Настройка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f6bb89370c85389d6c41306ed224d27d710cd7c8
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559537"
---
# <a name="configuration-data-at-scale"></a>Использование данных конфигурации в нужном масштабе

> Область применения. Windows PowerShell 5.1

Управление сотнями или тысячами серверов может оказаться сложной задачей.
Клиенты предоставили отзыв о том, что наиболее сложный аспект фактически управляет [данными конфигурации](/powershell/dsc/configurations/configdata).
Упорядочение информации по логическим конструкциям, таким как расположение, тип и среда.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-datum"></a>Проект сообщества: Использует

Решение, обслуживаемое сообществом [Datum](https://github.com/gaelcolas/Datum) , было создано для решения этой проблемы.
База данных основана на замечательных идеях от других платформ управления конфигурацией и реализует тот же тип решения для PowerShell DSC.
Информация [организована в текстовые файлы](https://github.com/gaelcolas/Datum#3-intended-usage) на основе логических идей.
Примеры:

- Параметры, которые должны применяться глобально
- Параметры, которые должны применяться ко всем серверам в расположении
- Параметры, которые должны применяться ко всем серверам баз данных
- Параметры отдельных серверов

Эти сведения организованы в предпочтительный формат файла (JSON, YAML или PSD1).
Затем предоставляются командлеты для создания файлов данных конфигурации путем [консолидации информации](https://github.com/gaelcolas/Datum#datum-tree) из каждого файла в единое представление сервера или роли сервера.

После создания файлов данных их можно использовать с [сценариями конфигурации DSC](/powershell/dsc/configurations/write-compile-apply-configuration) для создания MOF-файлов и [передачи MOF-файлов в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.

Чтобы испытать версию Datum, посетите [коллекция PowerShell](https://www.powershellgallery.com/packages/datum/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/gaelcolas/Datum#2-getting-started--concepts).

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о платформе Desired State Configuration Windows PowerShell](/powershell/dsc/overview/overview)
- [Ресурсы DSC](/powershell/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
