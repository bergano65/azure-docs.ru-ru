---
title: Преобразование конфигураций в составные ресурсы для конфигурации состояния — служба автоматизации Azure
description: Узнайте, как преобразовать конфигурации в составные ресурсы для конфигурации состояния в службе автоматизации Azure.
keywords: DSC, PowerShell, Настройка, Настройка
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.date: 08/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d830b8e27bb6f66a533b8106cbec53eeca4ca139
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69970720"
---
# <a name="convert-configurations-to-composite-resources"></a>Преобразование конфигураций в составные ресурсы

> Область применения. Windows PowerShell 5.1

После начала создания конфигураций можно быстро создать сценарии, которые управляют группами параметров.
Примеры:

- Создание веб-сервера
- Создание DNS-сервера
- Создание сервера SharePoint
- Настройка кластера SQL
- Управление параметрами брандмауэра
- Управление параметрами паролей

Если вы заинтересованы в совместном использовании этой работы с другими пользователями, лучшим вариантом является упаковка конфигурации как [составного ресурса](/powershell/dsc/resources/authoringresourcecomposite).
Создание составных ресурсов в первый раз может быть огромным.

> [!NOTE]
> В этой статье рассматривается решение, поддерживаемое сообществом с открытым исходным кодом.
> Поддержка доступна только в форме службы совместной работы GitHub, а не корпорацией Майкрософт.

## <a name="community-project-compositeresource"></a>Проект сообщества: компоситересаурце

Для решения этой проблемы было создано решение, поддерживаемое сообществом с именем [компоситересаурце](https://github.com/microsoft/compositeresource) .

Компоситересаурце автоматизирует процесс создания нового модуля из конфигурации.
Сначала с помощью [Dot](https://blogs.technet.microsoft.com/heyscriptingguy/2010/08/10/how-to-reuse-windows-powershell-functions-in-scripts/) настраивается сценарий конфигурации на рабочей станции (или на сервере сборки), поэтому он загружается в память.
Далее, вместо того чтобы запускать конфигурацию для создания MOF-файла, используйте функцию, предоставляемую модулем Компоситересаурце, для автоматизации преобразования.
Командлет загрузит содержимое конфигурации, получите список параметров и создаст новый модуль со всеми необходимыми данными.

После создания модуля можно увеличить его версию и добавить заметки о выпуске каждый раз при внесении изменений и публикации в собственном [репозитории PowerShellGet](https://kevinmarquette.github.io/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo).

После создания составного модуля ресурсов, содержащего конфигурацию (или несколько конфигураций), вы можете использовать их в [интерфейсе для создания](/azure/automation/compose-configurationwithcompositeresources) наборов, а также добавить их в [скрипты конфигурации DSC](/powershell/dsc/configurations/configurations) для создания MOF-файлов и [отправьте MOF-файлы в службу автоматизации Azure](/azure/automation/tutorial-configure-servers-desired-state#create-and-upload-a-configuration-to-azure-automation).
Затем зарегистрируйте серверы из [локальной](/azure/automation/automation-dsc-onboarding#physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azureaws) среды или [в Azure](/azure/automation/automation-dsc-onboarding#azure-virtual-machines) для извлечения конфигураций.
Последнее обновление проекта также опубликовало [модули Runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples) для службы автоматизации Azure, чтобы автоматизировать процесс импорта конфигураций из коллекция PowerShell.

Чтобы испытать автоматизацию создания составных ресурсов для DSC, посетите [коллекция PowerShell](https://www.powershellgallery.com/packages/compositeresource/) и скачайте решение или щелкните "сайт проекта", чтобы просмотреть [документацию](https://github.com/microsoft/compositeresource).

## <a name="next-steps"></a>Следующие шаги

- [Общие сведения о платформе Desired State Configuration Windows PowerShell](/powershell/dsc/overview/overview)
- [Ресурсы DSC](/powershell/dsc/resources/resources)
- [Настройка локального Configuration Manager](/powershell/dsc/managing-nodes/metaconfig)
