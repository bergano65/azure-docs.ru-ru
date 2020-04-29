---
title: Коллекции модулей Runbook и других модулей для службы автоматизации Azure
description: Вы можете устанавливать и использовать в своей среде службы автоматизации Azure модули Runbook и другие модули, созданные корпорацией Майкрософт и сообществом.  В этой статье описывается, как получить доступ к этим ресурсам и как передавать модули Runbook в коллекцию.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81535559"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Коллекции модулей Runbook и других модулей для службы автоматизации Azure

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом. Runbook и [модули](#modules-in-powershell-gallery) PowerShell можно получить из коллекции центра сценариев для модулей [Runbook](#use-python-runbooks) коллекция PowerShell и Python. Вы также можете участвовать в сообществе, [разрабатывая сценарии, которые вы разрабатываете](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Модули Runbook в коллекция PowerShell

[Коллекция PowerShell](https://www.powershellgallery.com/packages) предоставляет разнообразные модули Runbook от Майкрософт и сообщества, которые можно импортировать в службу автоматизации Azure. Чтобы использовать его, скачайте модуль Runbook из коллекции или непосредственно импортируйте модули Runbook из коллекции или из учетной записи службы автоматизации в портал Azure.

> [!NOTE]
> Графические модули Runbook не поддерживаются в коллекция PowerShell.

Импортировать можно только непосредственно из коллекция PowerShell с помощью портал Azure. Эту функцию нельзя выполнить с помощью PowerShell.

> [!NOTE]
> Необходимо проверить содержимое всех модулей Runbook, получаемых из коллекция PowerShell, и при установке и запуске в рабочей среде с особой осторожностью соблюдать осторожность.

## <a name="modules-in-powershell-gallery"></a>Модули в коллекции PowerShell

Модули PowerShell содержат командлеты, которые можно использовать в модулях Runbook, а в [коллекции PowerShell](https://www.powershellgallery.com) собраны готовые модули, которые можно установить в службе автоматизации Azure. Вы можете открыть эту коллекцию на портале Azure и установить модули из нее непосредственно в службе автоматизации Azure. Вы также можете скачать модули для установки вручную.

## <a name="common-solutions-available-in-powershell-gallery"></a>Общие решения, доступные в коллекция PowerShell

Список ниже содержит несколько модулей Runbook, предоставляющих решения для распространенных сценариев. Полный список модулей Runbook, созданных командой службы автоматизации Azure, см. в разделе [азуреаутоматионтеам Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-модулесинаутоматионтолатестверсион](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — импортирует последнюю версию всех модулей в учетной записи службы автоматизации из коллекция PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — настраивает система диагностики Azure и log Analytics для получения журналов службы автоматизации Azure, содержащих состояние задания и потоки заданий.
   * [Copy-итемфромазуревм](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — копирует удаленный файл из виртуальной машины Windows Azure.
   * [Copy-итемфромазуревм](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — копирует локальный файл на виртуальную машину Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Импорт модуля Runbook PowerShell из коллекции Runbook с помощью портал Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **коллекция модулей Runbook** в разделе **Автоматизация процессов**.
3. Выберите **Источник: коллекция PowerShell**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать элемент, щелкните его, чтобы просмотреть сведения о нем, а затем нажмите кнопку **Импорт**.

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Модуль Runbook появится на вкладке **модули Runbook** учетной записи службы автоматизации.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Добавление модуля Runbook PowerShell в коллекцию

Корпорация Майкрософт рекомендует добавлять модули Runbook в коллекция PowerShell, которые вы считаете полезными для других клиентов. Коллекция PowerShell принимает модули PowerShell и скрипты PowerShell. Модуль Runbook можно добавить, [загружая его в коллекция PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Импорт модуля из коллекции модулей с портал Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Модули** в разделе **Общие ресурсы** чтобы открыть список модулей.
3. В верхней части страницы щелкните **Обзор коллекции**.

   ![Коллекция модулей](media/automation-runbook-gallery/modules-blade.png)

4. На странице Обзор коллекции можно выполнить поиск по следующим полям:

   * Имя модуля
   * Теги
   * Автор
   * Командлет или имя ресурса DSC

5. Найдите интересующий вас модуль и выберите его, чтобы просмотреть сведения.

   Во время подробного рассмотрения конкретного модуля просмотрите дополнительную информацию. Данная информация включает ссылку на коллекцию PowerShell, все необходимые зависимости и все командлеты или ресурсы DSC, которые содержит модуль.

   ![Сведения о модуле PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Чтобы установить модуль непосредственно в службу автоматизации Azure, нажмите кнопку **Импорт**.
7. На панели импорта можно просмотреть имя импортируемого модуля. Если установлены все зависимости, кнопка **ОК** будет активна. Если какие-либо зависимости отсутствуют, их необходимо будет импортировать перед импортом данного модуля.
8. На панели Импорт нажмите кнопку **ОК** , чтобы импортировать модуль. Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле и командлетах. Это действие может занять несколько минут, так как необходимо извлечь каждое действие.
9. Вы получите два уведомления о начале и завершении развертывания модуля соответственно.
10. Доступные действия появятся после импорта модуля. Ресурсы модуля можно использовать в модулях Runbook и ресурсах DSC.

> [!NOTE]
> Модули, которые поддерживают только PowerShell Core, не поддерживаются в службе автоматизации Azure. Их нельзя импортировать на портале Azure и развернуть непосредственно из коллекции PowerShell.

## <a name="use-python-runbooks"></a>Использование модулей Runbook Python

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Вы можете добавить модули Runbook Python в коллекцию центра скриптов, щелкнув **Отправить публикацию**. При этом обязательно добавьте тег `Python` при отправке публикации.

> [!NOTE]
> Чтобы передать содержимое в [центр сценариев](https://gallery.technet.microsoft.com/scriptcenter), вам потребуется не менее 100 точек.

## <a name="request-a-runbook-or-module"></a>Запрос Runbook или модуля

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля Runbook или освоении PowerShell, опубликуйте вопрос на нашем [форуме](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Дальнейшие шаги

* Чтобы приступить к работе с модулями Runbook, см. статью [Управление модулем Runbook в службе автоматизации Azure](manage-runbooks.md).
* Сведения о различиях между PowerShell и рабочим процессом PowerShell с модулями Runbook см. в статье [изучение рабочего процесса PowerShell](automation-powershell-workflow.md).
* Дополнительные сведения о PowerShell, включая справочник по языку и обучающие модули, вы найдете в [этой документации](https://docs.microsoft.com/powershell/scripting/overview).
