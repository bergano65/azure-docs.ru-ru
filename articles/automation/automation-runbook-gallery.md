---
title: Коллекции модулей Runbook и других модулей для службы автоматизации Azure
description: Вы можете устанавливать и использовать в своей среде службы автоматизации Azure модули Runbook и другие модули, созданные корпорацией Майкрософт и сообществом.  В этой статье описаны способы доступа к этим ресурсам и процесс добавления ваших модулей Runbook в коллекцию.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5daa1cf04e04507174fa480c785bd15b656541a1
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019110"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Коллекции модулей Runbook и других модулей для службы автоматизации Azure

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом.

Runbook и [модули](#modules-in-powershell-gallery) PowerShell можно получить из коллекции центра сценариев для модулей [Runbook](#python-runbooks) коллекция PowerShell и Python. Вы также можете участвовать в сообществе, разрабатывая сценарии, которые вы разрабатываете, см. статью Добавление модуля Runbook в коллекцию.

## <a name="runbooks-in-powershell-gallery"></a>Модули Runbook в коллекция PowerShell

[Коллекция PowerShell](https://www.powershellgallery.com/packages) предоставляет разнообразные модули Runbook от Майкрософт и сообщества, которые можно импортировать в службу автоматизации Azure. Чтобы использовать его, скачайте модуль Runbook из коллекции или непосредственно импортируйте модули Runbook из коллекции или из учетной записи службы автоматизации в портал Azure.

Импортировать можно только непосредственно из коллекция PowerShell с помощью портал Azure. Эту функцию нельзя выполнить с помощью PowerShell.

> [!NOTE]
> Необходимо проверить содержимое всех модулей Runbook, получаемых из коллекция PowerShell, и при установке и запуске в рабочей среде с особой осторожностью соблюдать осторожность.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Импорт модуля Runbook PowerShell из коллекции Runbook с помощью портал Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В разделе **Автоматизация процессов** выберите пункт **Коллекция модулей Runbook**.
3. Выберите **источник: Коллекция PowerShell**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать элемент, щелкните его, а затем в окне просмотра сведений нажмите кнопку **Импорт** .

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Модуль runbook появится на вкладке **Модули runbook** учетной записи службы автоматизации.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Добавление модуля Runbook PowerShell в коллекцию

Корпорация Майкрософт рекомендует добавлять модули Runbook в коллекция PowerShell, которые вы считаете полезными для других клиентов. Коллекция PowerShell принимает модули PowerShell и сценарии PowerShell. Модуль Runbook можно добавить, [загружая его в коллекция PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Графические модули Runbook не поддерживаются в коллекция PowerShell.

## <a name="modules-in-powershell-gallery"></a>Модули в коллекции PowerShell

Модули PowerShell содержат командлеты, которые можно использовать в модулях Runbook, а в [коллекции PowerShell](https://www.powershellgallery.com) собраны готовые модули, которые можно установить в службе автоматизации Azure. Вы можете открыть эту коллекцию на портале Azure и установить модули из нее непосредственно в службе автоматизации Azure. Вы также можете скачать модули для установки вручную.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Импорт модуля из коллекции модулей службы автоматизации с помощью портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Модули** в разделе **Общие ресурсы** чтобы открыть список модулей.
3. В верхней части страницы щелкните **Обзор коллекции**.

   ![Коллекция модулей](media/automation-runbook-gallery/modules-blade.png)

4. На странице **Обзор коллекции** можно выполнить поиск по следующим полям:

   * Имя модуля
   * Теги
   * Автор
   * Командлет или имя ресурса DSC

5. Найдите интересующий вас модуль и выберите его, чтобы просмотреть сведения.

   Во время подробного рассмотрения конкретного модуля просмотрите дополнительную информацию. Данная информация включает ссылку на коллекцию PowerShell, все необходимые зависимости и все командлеты или ресурсы DSC, которые содержит модуль.

   ![Сведения о модуле PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Чтобы установить модуль непосредственно в службу автоматизации Azure, нажмите кнопку **Импорт** .
7. Когда на панели **Импорт** вы нажмете кнопку "Импорт", вы увидите имя импортируемого модуля. Если установлены все зависимости, кнопка **ОК** будет активна. Если какие-либо зависимости отсутствуют, их необходимо будет импортировать перед импортом данного модуля.
8. На странице **импорта** нажмите кнопку **ОК**, чтобы импортировать модуль. Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле и командлетах. Это действие может занять несколько минут, так как каждое действие необходимо извлечь.
9. Вы получите два уведомления о начале и завершении развертывания модуля соответственно.
10. Доступные действия появятся после импорта модуля. Его ресурсы можно использовать в Runbook и в настройке требуемого состояния.

> [!NOTE]
> Модули, которые поддерживают только PowerShell Core, не поддерживаются в службе автоматизации Azure. Их нельзя импортировать на портале Azure и развернуть непосредственно из коллекции PowerShell.

## <a name="python-runbooks"></a>Модули runbook Python

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Вы можете добавить модули Runbook Python в коллекцию центра скриптов, щелкнув **Отправить публикацию**. При отправке материалов убедитесь, что вы добавили тег **Python**.

> [!NOTE]
> Для отправки содержимого в [центр сценариев](https://gallery.technet.microsoft.com/scriptcenter) требуется не менее 100 точек.

## <a name="requesting-a-runbook-or-module"></a>Запрос на создание модуля Runbook или другого модуля

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля Runbook или освоении PowerShell, опубликуйте вопрос на нашем [форуме](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Общие решения, доступные в коллекции модулей Runbook

Список ниже содержит несколько модулей Runbook, предоставляющих решения для распространенных сценариев. Полный список модулей Runbook, созданных командой службы автоматизации Azure, см. в разделе [азуреаутоматионтеам Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-модулесинаутоматионтолатестверсион](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — импортирует последнюю версию на коллекция PowerShell всех модулей в учетной записи службы автоматизации.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — этот сценарий настраивает система диагностики Azure и log Analytics для получения журналов службы автоматизации Azure, содержащих состояние задания и потоки заданий.
* [Copy-итемфромазуревм](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — этот модуль Runbook копирует удаленный файл из виртуальной машины Windows Azure.
* [Copy-итемфромазуревм](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — этот модуль Runbook копирует локальный файл на виртуальную машину Azure.

## <a name="next-steps"></a>Следующие шаги

* Сведения о том, как начать работу с модулями Runbook, см. в статье [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md).
* Чтобы понять различия между PowerShell и рабочим процессом PowerShell с модулями Runbook, см. статью [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).
* Дополнительные сведения о PowerShell, включая Справочник по языку и обучающие модули, см. в документации по [PowerShell](https://docs.microsoft.com/en-us/powershell/scripting/overview).
