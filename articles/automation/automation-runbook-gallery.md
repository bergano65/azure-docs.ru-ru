---
title: Коллекции модулей Runbook и других модулей для службы автоматизации Azure
description: Вы можете устанавливать и использовать в своей среде службы автоматизации Azure модули Runbook и другие модули, созданные корпорацией Майкрософт и сообществом.  В этой статье описаны способы доступа к этим ресурсам и процесс добавления ваших модулей Runbook в коллекцию.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 58f666d7ebf8ac02d393a42f55e00f08d82b8cae
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337094"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Коллекции модулей Runbook и других модулей для службы автоматизации Azure

Вы можете не создавать собственные модули Runbook и другие модули в службе автоматизации Azure, а воспользоваться сценариями, уже созданными корпорацией Майкрософт и сообществом.

Вы можете получить [модули Runbook PowerShell](#runbooks-in-runbook-gallery) и [модули](#modules-in-powerShell-gallery) из коллекции PowerShell и [модули Runbook Python](#python-runbooks) из коллекции Script Center. Также вы можете поделиться с сообществом сценариями, которые вы разработали: см. раздел, посвященный [добавлению модуля Runbook в коллекцию](#adding-a-runbook-to-the-runbook-gallery).

## <a name="runbooks-in-powershell-gallery"></a>Модули Runbook в коллекции PowerShell

[Коллекции PowerShell](https://www.powershellgallery.com/packages) предоставляет широкий набор модулей Runbook от корпорации Майкрософт и сообщества, которые можно импортировать в службу автоматизации Azure. Для использования, модуль runbook можно скачать из коллекции, или можно импортировать непосредственно из коллекции, или из учетной записи службы автоматизации на портале Azure.

Можно импортировать только непосредственно из коллекции PowerShell с помощью портала Azure. Не поддерживает эту функцию, с помощью PowerShell.

> [!NOTE]
> Обязательно проверяйте содержимое всех модулей Runbook, что вы получаете из коллекции PowerShell и соблюдайте предельную осторожность при их установке и запуске в рабочей среде.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Импорт модуля runbook PowerShell из коллекции Runbook с помощью портала Azure

1. На портале Azure выберите свою учетную запись службы автоматизации.
2. В разделе **Автоматизация процессов** выберите пункт **Коллекция модулей Runbook**.
3. Выберите **источника: Коллекция PowerShell**.
4. Найдите нужный элемент коллекции и выберите его, чтобы просмотреть сведения о нем. Слева вы можете ввести дополнительные параметры для издателя и типа.

   ![Обзор коллекции](media/automation-runbook-gallery/browse-gallery.png)

5. Щелкните **Просмотреть исходный проект** , чтобы открыть соответствующий элемент в [центре сценариев TechNet](https://gallery.technet.microsoft.com/).
6. Чтобы импортировать элемент, щелкните его, а затем в окне просмотра сведений нажмите кнопку **Импорт** .

   ![Кнопка «Импорт»](media/automation-runbook-gallery/gallery-item-detail.png)

7. Также для импорта модуля Runbook вы можете изменить его имя, а затем нажать кнопку **ОК** .
8. Модуль runbook появится на вкладке **Модули runbook** учетной записи службы автоматизации.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Добавление модуля runbook PowerShell в коллекции

Корпорация Майкрософт рекомендует, чтобы добавить модули Runbook в коллекции PowerShell, которая будет полезна для других клиентов. Коллекция PowerShell принимает модули PowerShell и сценариев PowerShell. Можно добавить модуль runbook, [передачи их в коллекции PowerShell](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Графические модули Runbook не поддерживаются в коллекции PowerShell.

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

Модули runbook Python доступны в [ коллекции центра сценариев](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). У вас есть доступ модули Runbook Python в коллекции Script Center, щелкнув **загрузить публикацию**. При отправке материалов убедитесь, что вы добавили тег **Python**.

> [!NOTE]
> Чтобы отправить содержимое для [Script Center](https://gallery.technet.microsoft.com/scriptcenter) требуется не менее 100 точек. 

## <a name="requesting-a-runbook-or-module"></a>Запрос на создание модуля Runbook или другого модуля

Свой запрос можно отправить на сайте [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Если вам нужна помощь в написании модуля Runbook или освоении PowerShell, опубликуйте вопрос на нашем [форуме](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о том, как начать работу с модулями Runbook, см. в статье [Управление модулями runbook в службе автоматизации Azure](manage-runbooks.md).
* Чтобы понять различия между PowerShell и рабочим процессом PowerShell с модулями Runbook, см. статью [Изучение рабочего процесса Windows PowerShell](automation-powershell-workflow.md).