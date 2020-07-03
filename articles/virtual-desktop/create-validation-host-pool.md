---
title: Обновление службы в пуле узлов Виртуального рабочего стола Windows (Azure)
description: Сведения о создании пула узлов проверки для мониторинга обновлений службы перед их развертыванием в рабочую среду.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b74b7f0b79ad4064d7133a19316d6aec6bd5ba3a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611574"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Руководство по Создание пула узлов для проверки обновлений службы

>[!IMPORTANT]
>Это содержимое применимо к обновлению за весну 2020 года с объектами Azure Resource Manager для Виртуального рабочего стола Windows. Если вы используете выпуск Виртуального рабочего стола Windows за осень 2019 года без объектов Azure Resource Manager, см. [эту статью](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).
>
> Обновление Виртуального рабочего стола Windows за весну 2020 года пока предоставляется как общедоступная предварительная версия. без соглашений об уровне обслуживания. Мы не рекомендуем использовать ее для выполнения производственных рабочих нагрузок. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. 
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Перед развертыванием пулов узлов в рабочую среду настоятельно рекомендуется создать пул узлов проверки. Обновления применяются в первую очередь к пулам узлов проверки, что позволяет отслеживать обновления служб перед их развертыванием в рабочую среду. Без пула узлов проверки вы не сможете обнаружить изменения, которые могут привести к ошибкам и, следовательно, к простоям для пользователей в рабочей среде.

Чтобы обеспечить совместимость приложения с последними обновлениями, пул узлов проверки должен быть максимально похож на пулы узлов в рабочей среде. Пользователи должны подключаться к пулу узлов проверки так же часто, как и к рабочему пулу узлов. Если в пуле узлов есть автоматическое тестирование, необходимо включить его в пул узлов проверки.

Вы можете выполнять отладку ошибок в пуле узлов проверки с помощью [функции диагностики](diagnostics-role-service.md) или сведений из [статей по устранению неполадок Виртуального рабочего стола Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> Мы рекомендуем не менять расположение пула узлов проверки, чтобы протестировать все будущие обновления.

>[!IMPORTANT]
>Сейчас в выпуске Виртуального рабочего стола Windows за весну 2020 года возникают ошибки при включении и отключении среды проверки. Мы обновим эту статью, когда устраним эту проблему.

## <a name="prerequisites"></a>Предварительные требования

Прежде чем начать работу, выполните эти [ инструкции](powershell-module.md), чтобы настроить модуль PowerShell для Виртуального рабочего стола Windows и войти в Azure.

## <a name="create-your-host-pool"></a>Создание пула узлов

Пул узлов можно создать, следуя инструкциям в любой из этих статей:
- [Руководство. Создание пула узлов с помощью Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Create a host pool with PowerShell](create-host-pools-powershell.md) (Создание пула узлов с помощью PowerShell)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Определение пула узлов в качестве пула узлов проверки

Выполните следующие командлеты PowerShell, чтобы определить новый пул узлов в качестве пула узлов проверки. Замените значения в скобках значениями для вашего сеанса.

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Выполните следующий командлет PowerShell, чтобы убедиться, что свойство проверки установлено. Замените значения в скобках значениями для вашего сеанса.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Результаты из командлета должны выглядеть подобным образом:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>Обновление расписания

Обновления службы выполняются ежемесячно. Если есть серьезные проблемы, критические обновления будут предоставляться чаще.

При обновлении службы убедитесь, что у вас есть хотя бы небольшая группа пользователей, которые выполняют вход каждый день для проверки среды. Рекомендуем регулярно посещать наш [сайт TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) и читать публикации, посвященные WVDUPdate, чтобы следить за обновлениями службы.

## <a name="next-steps"></a>Дальнейшие действия

Создав пул узлов проверки, вы можете узнать, как использовать решение "Работоспособность служб Azure" для мониторинга развертывания Виртуального рабочего стола Windows. 

> [!div class="nextstepaction"]
> [Настройка оповещений служб](./set-up-service-alerts.md)
