---
title: Преобразование конфигураций в составные ресурсы для конфигурации состояния - Azure Automation
description: Узнайте, как преобразовать конфигурации в составные ресурсы для конфигурации состояния в Azure Automation.
keywords: dsc,powershell,конфигурация,установка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 05f0a81a738688df15ea9060071d9e266b54b7d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136661"
---
# <a name="convert-configurations-to-composite-resources"></a>Преобразование конфигураций в составные ресурсы

> Применяется к: Windows PowerShell 5.1

После начала авторизации конфигураций можно быстро создавать "сценарии", которые управляют группами параметров.
Пример будет следующим:

- создать веб-сервер
- создание DNS-сервера
- создание сервера SharePoint
- настройка кластера S'L
- управлять настройками брандмауэра
- управлять настройками паролей

Если вы заинтересованы в совместном использовании этой работы с другими пользователями, лучшим вариантом является упаковка конфигурации в качестве [композитного ресурса.](/powershell/scripting/dsc/resources/authoringresourcecomposite)
Создание композитных ресурсов впервые может быть подавляющим.

> [!NOTE]
> Эта статья относится к решению, которое поддерживается сообществом open Source.
> Поддержка доступна только в форме совместной работы GitHub, а не от корпорации Майкрософт.

## <a name="community-project-compositeresource"></a>Общественный проект: КомпозитРесурс

Для решения этой проблемы было создано сообщество, поддерживаемое решение под названием [CompositeResource.](https://github.com/microsoft/compositeresource)

CompositeResource автоматизирует процесс создания нового модуля из конфигурации.
Вы начинаете с [точки поиска](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) скрипт конфигурации на рабочей станции (или построить сервер), так что он загружается в память.
Затем, вместо того, чтобы запускать конфигурацию для создания файла MOF, используйте функцию, предоставляемую модулем CompositeResource, для автоматизации преобразования.
Cmdlet загрузит содержимое вашей конфигурации, получит список параметров и создаст новый модуль со всем необходимым.

После того как вы создали модуль, вы можете приравнять версию и добавить примечания к выпуску каждый раз, когда вы вносят изменения и публиковать его в свой собственный [репозиторий PowerShellGet.](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)

После создания модуля композитных ресурсов, содержащего конфигурацию (или несколько конфигураций), вы можете использовать их в опыте [композиций](/azure/automation/compose-configurationwithcompositeresources) в Azure или добавить их в [скрипты конфигурации DSC](/powershell/scripting/dsc/configurations/configurations) для генерации файлов MOF и [загрузки файлов MOF в Azure Automation.](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation)
Затем зарегистрируйте серверы либо в [помещении,](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances) либо [в Azure,](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) чтобы вытащить конфигурации.
Последнее обновление проекта также опубликовало [runbooks](https://www.powershellgallery.com/packages?q=DscGallerySamples) для Azure Automation для автоматизации процесса импорта конфигураций из галереи PowerShell.

Чтобы опробовать автоматизацию создания композитных ресурсов для DSC, посетите [галерею PowerShell](https://www.powershellgallery.com/packages/compositeresource/) и загрузите решение или нажмите "Сайт проекта", чтобы просмотреть [документацию.](https://github.com/microsoft/compositeresource)

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о службе настройки требуемого состояния Windows PowerShell](/powershell/scripting/dsc/overview/overview)
- [Ресурсы DSC](/powershell/scripting/dsc/resources/resources)
- [Настройка локального менеджера конфигурации](/powershell/scripting/dsc/managing-nodes/metaconfig)
