---
title: Использование модулей runbook и модулей службы автоматизации Azure в коллекции PowerShell
description: В этой статье рассказывается, как использовать модули runbook и модули, разработанные Майкрософт и сообществом, в коллекции PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: f2bf058ddce81ab9f04e97787a4dc93e44036b1b
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186118"
---
# <a name="use-runbooks-and-modules-in-powershell-gallery"></a>Поиск последовательностей runbook и модулей в коллекции PowerShell

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом. Модули runbook PowerShell и [другие модули](#modules-in-powershell-gallery) можно получить из коллекции PowerShell, а [модули runbook Python](#use-python-runbooks) — из коллекции Центра скриптов. Вы также можете поделиться с сообществом [скриптами, которые вы разработали](#add-a-powershell-runbook-to-the-gallery). 

## <a name="runbooks-in-powershell-gallery"></a>Модули runbook в коллекции PowerShell

В [коллекции PowerShell](https://www.powershellgallery.com/packages) содержатся разнообразные модули runbook, разработанные корпорацией Майкрософт и сообществом, которые вы можете импортировать в службу автоматизации Azure. Чтобы использовать модуль runbook, скачайте его из коллекции. Вы также можете напрямую импортировать модули runbook из этой коллекции или из своей учетной записи службы автоматизации на портале Azure.

> [!NOTE]
> Графические модули runbook в коллекции PowerShell не поддерживаются.

Их можно только импортировать напрямую из коллекции PowerShell через портал Azure. Эту функцию нельзя выполнить с помощью PowerShell.

> [!NOTE]
> Обязательно проверяйте содержимое всех модулей runbook, которые вы получаете из коллекции PowerShell, и соблюдайте предельную осторожность при их установке и запуске в рабочей среде.

## <a name="modules-in-powershell-gallery"></a>Модули в коллекции PowerShell

Модули PowerShell содержат командлеты, которые можно использовать в модулях Runbook, а в [коллекции PowerShell](https://www.powershellgallery.com) собраны готовые модули, которые можно установить в службе автоматизации Azure. Вы можете открыть эту коллекцию на портале Azure и установить модули из нее непосредственно в службе автоматизации Azure. Вы также можете скачать модули для установки вручную.

## <a name="common-scenarios-available-in-powershell-gallery"></a>Типовые сценарии, доступные в коллекции PowerShell

В следующем списке перечислено несколько модулей runbook, которые поддерживают типовые сценарии. Полный список модулей runbook, созданных командой службы автоматизации Azure, см. в разделе [Профиль AzureAutomationTeam](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) — импортирует последние версии всех модулей в учетной записи автоматизации из коллекции PowerShell.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) — настраивает Диагностику Azure и Log Analytics для получения журналов службы автоматизации Azure, содержащих сведения о состоянии заданий и потоки заданий.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) — копирует удаленный файл из виртуальной машины Windows Azure.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) — копирует локальный файл на виртуальную машину Azure.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Импорт модуля runbook из коллекции runbook с помощью портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Коллекция модулей runbook** в разделе **Автоматизация процессов**.
3. Выберите **Источник: коллекция PowerShell**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать элемент, щелкните его, чтобы открыть окно просмотра сведений, а затем нажмите кнопку **Импорт**.

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Модуль runbook появится на вкладке **Модули runbook** учетной записи службы автоматизации.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Добавление модуля runbook PowerShell в коллекцию

Корпорация Майкрософт рекомендует добавлять в коллекцию PowerShell модули runbook, которые, на ваш взгляд, могут пригодиться другим клиентам. Коллекция PowerShell принимает модули PowerShell и скрипты PowerShell. Вы можете добавить модуль runbook, [отправив его в коллекцию PowerShell](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Импорт модуля из коллекции модулей с помощью портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. Выберите **Модули** в разделе **Общие ресурсы** чтобы открыть список модулей.
3. В верхней части страницы щелкните **Обзор коллекции**.

   ![Коллекция модулей](media/automation-runbook-gallery/modules-blade.png)

4. На странице "Обзор коллекции" можно выполнить поиск по следующим полям.

   * Имя модуля
   * Теги
   * Автор
   * Командлет или имя ресурса DSC

5. Найдите интересующий вас модуль и выберите его, чтобы просмотреть сведения.

   Во время подробного рассмотрения конкретного модуля просмотрите дополнительную информацию. Данная информация включает ссылку на коллекцию PowerShell, все необходимые зависимости и все командлеты или ресурсы DSC, которые содержит модуль.

   ![Сведения о модуле PowerShell](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Чтобы установить модуль непосредственно в службу автоматизации Azure, нажмите кнопку **Импорт**.
7. В панели импорта можно просмотреть имя модуля, который нужно импортировать. Если установлены все зависимости, кнопка **ОК** будет активна. Если какие-либо зависимости отсутствуют, их необходимо будет импортировать перед импортом данного модуля.
8. На странице импорта нажмите кнопку **ОК**, чтобы импортировать модуль. Когда служба автоматизации Azure импортирует модуль в вашу учетную запись, она извлекает метаданные о модуле и командлетах. Эта операция может занять несколько минут, так как каждое действие необходимо извлечь.
9. Вы получите два уведомления о начале и завершении развертывания модуля соответственно.
10. Доступные действия появятся после импорта модуля. Вы можете использовать ресурсы модуля в своих модулях runbook и ресурсах DSC.

> [!NOTE]
> Модули, которые поддерживают только PowerShell Core, не поддерживаются в службе автоматизации Azure. Их нельзя импортировать на портале Azure и развернуть непосредственно из коллекции PowerShell.

## <a name="use-python-runbooks"></a>Использование модулей runbook Python

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Чтобы добавить модули runbook Python в коллекцию центра сценариев, нажмите **Отправить вклад**. При отправке материалов убедитесь, что вы добавили тег `Python`.

> [!NOTE]
> Для отправки материалов в [центр сценариев](https://gallery.technet.microsoft.com/scriptcenter) вам нужно не менее 100 баллов.

## <a name="request-a-runbook-or-module"></a>Запрос модуля runbook или другого модуля

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля runbook или освоении PowerShell, опубликуйте вопрос на нашей [странице вопросов и ответов Майкрософт](/answers/topics/azure-automation.html).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы начать работу с модулем runbook PowerShell, изучите документ [Руководство. Создание модуля runbook PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Сведения о работе с модулями runbook см. в статье [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md).
* Дополнительные сведения о PowerShell см. в [документации PowerShell](/powershell/scripting/overview).
* * Справочник по командлетам PowerShell см. в документации по [Az.Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
