---
title: Коллекции модулей Runbook и других модулей для службы автоматизации Azure
description: Вы можете устанавливать и использовать в своей среде службы автоматизации Azure модули Runbook и другие модули, созданные корпорацией Майкрософт и сообществом.  В этой статье описывается, как вы можете получить доступ к этим ресурсам и внести свои книги в галерею.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535559"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Коллекции модулей Runbook и других модулей для службы автоматизации Azure

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом. Вы можете получить книги и [модули](#modules-in-powershell-gallery) PowerShell из галереи PowerShell и [runbooks Python](#use-python-runbooks) из галереи Script Center. Вы также можете внести свой вклад в сообщество, делясь [сценариями, которые вы разрабатываете.](#add-a-powershell-runbook-to-the-gallery) 

## <a name="runbooks-in-powershell-gallery"></a>Runbooks в галерее PowerShell

[Галерея PowerShell](https://www.powershellgallery.com/packages) предоставляет множество книг от корпорации Майкрософт и сообщества, которые можно импортировать в Azure Automation. Чтобы использовать один из них, загрузите книгу из галереи или можете напрямую импортировать книги из галереи или из учетной записи Автоматизации на портале Azure.

> [!NOTE]
> Графические руны не поддерживаются в галерее PowerShell.

Импортировать можно только из галереи PowerShell с помощью портала Azure. Вы не можете выполнять эту функцию с помощью PowerShell.

> [!NOTE]
> Вы должны проверить содержимое любых runbooks, которые вы получаете от PowerShell Галерея и использовать крайнюю осторожность при установке и запуске их в рабочей среде.

## <a name="modules-in-powershell-gallery"></a>Модули в коллекции PowerShell

Модули PowerShell содержат командлеты, которые можно использовать в модулях Runbook, а в [коллекции PowerShell](https://www.powershellgallery.com) собраны готовые модули, которые можно установить в службе автоматизации Azure. Вы можете открыть эту коллекцию на портале Azure и установить модули из нее непосредственно в службе автоматизации Azure. Вы также можете скачать модули для установки вручную.

## <a name="common-solutions-available-in-powershell-gallery"></a>Общие решения, доступные в галерее PowerShell

Список ниже содержит несколько runbooks, которые предоставляют решения для общих сценариев. Полный список runbooks, созданный командой Azure Automation, можно ознакомиться с [профилем AzureAutomationTeam.](https://www.powershellgallery.com/profiles/AzureAutomationTeam)

   * [Обновление-МодулиInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Импортирует последнюю версию всех модулей в учетной записи автоматизации из powerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Настраивает диагностику azure и аналитику журналов для получения журналов автоматизации Azure, содержащих статус рабочих мест и потоки рабочих мест.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Копии удаленного файла из виртуальной машины Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Копирует локальный файл на виртуальную машину Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Импортируйте книгу PowerShell из галереи runbook с портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **галерею Runbooks** под **автоматизацией процессов.**
3. Выберите **Источник: Галерея PowerShell**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать товар, нажмите на него, чтобы просмотреть его детали, а затем нажмите **Импорт.**

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Запуск отослжеки отображается на вкладке **Runbooks** для учетной записи Automation.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Добавить в галерею книгу сыпчаткой PowerShell

Корпорация Майкрософт рекомендует добавлять в галерею PowerShell книги, которые, по вашему мнению, будут полезны другим клиентам. Коллекция PowerShell принимает модули PowerShell и скрипты PowerShell. Вы можете добавить книгу, [загрузив его в галерею PowerShell.](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Импортируйте модуль из галереи модулей с портала Azure

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

6. Чтобы установить модуль непосредственно в Azure Automation, нажмите **Импорт**.
7. На панели импорта можно увидеть название модуля для импорта. Если установлены все зависимости, кнопка **ОК** будет активна. Если какие-либо зависимости отсутствуют, их необходимо будет импортировать перед импортом данного модуля.
8. На панели импорта нажмите **OK,** чтобы импортировать модуль. Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле и командлетах. Это действие может занять несколько минут, так как каждое действие должно быть извлечено.
9. Вы получите два уведомления о начале и завершении развертывания модуля соответственно.
10. Доступные действия появятся после импорта модуля. Ресурсы модуля можно использовать в своих runbooks и DSC-ресурсах.

> [!NOTE]
> Модули, которые поддерживают только PowerShell Core, не поддерживаются в службе автоматизации Azure. Их нельзя импортировать на портале Azure и развернуть непосредственно из коллекции PowerShell.

## <a name="use-python-runbooks"></a>Используйте руны Python

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Вы можете внести runbooks Python в галерею Script Center, нажав **загрузить вклад.** Когда вы это сделаете, `Python` убедитесь, что вы добавляете тег при загрузке вашего вклада.

> [!NOTE]
> Для загрузки контента в [Script Center](https://gallery.technet.microsoft.com/scriptcenter)необходимо как минимум 100 баллов.

## <a name="request-a-runbook-or-module"></a>Запросить книгу или модуль

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля Runbook или освоении PowerShell, опубликуйте вопрос на нашем [форуме](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Следующие шаги

* Чтобы начать работу с runbooks, [см.](manage-runbooks.md)
* Чтобы понять различия между PowerShell и PowerShell Workflow с runbooks, [см.](automation-powershell-workflow.md)
* Для получения дополнительной информации о PowerShell, включая языковые справочные и учебные модули, обратитесь к [документам PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
