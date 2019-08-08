---
title: Создание пула узлов предварительной версии Виртуального рабочего стола Windows для проверки обновлений службы в Azure
description: Сведения о создании пула узлов проверки для мониторинга обновлений службы перед их развертыванием в рабочую среду.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/08/2019
ms.author: helohr
ms.openlocfilehash: 732192e481f88c0cd3c78e9dac43970eb5be72e6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840515"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Руководство по Создание пула узлов для проверки обновлений службы

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента предварительной версии Виртуального рабочего стола Windows. Перед развертыванием пулов узлов в рабочую среду настоятельно рекомендуется создать пул узлов проверки. Обновления применяются в первую очередь к пулам узлов проверки, что позволяет отслеживать обновления служб перед их развертыванием в рабочую среду. Без пула узлов проверки вы не сможете обнаружить изменения, которые могут привести к ошибкам и, следовательно, к простоям для пользователей в рабочей среде.

Чтобы обеспечить совместимость приложения с последними обновлениями, пул узлов проверки должен быть максимально похож на пулы узлов в рабочей среде. Пользователи должны подключаться к пулу узлов проверки так же часто, как и к рабочему пулу узлов. Если в пуле узлов есть автоматическое тестирование, необходимо включить его в пул узлов проверки.

Вы можете выполнять отладку ошибок в пуле узлов проверки с помощью [функции диагностики](diagnostics-role-service.md) или сведений из [статей по устранению неполадок Виртуального рабочего стола Windows](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview).

>[!NOTE]
> Мы рекомендуем не менять расположение пула узлов проверки, чтобы протестировать все будущие обновления.

Перед началом работы при необходимости [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview).

## <a name="create-your-host-pool"></a>Создание пула узлов

Пул узлов можно создать, следуя инструкциям в любой из этих статей:
- [Руководство. Создание пула узлов с помощью Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Create a host pool with an Azure Resource Manager template](create-host-pools-arm-template.md) (Создание пула узлов с помощью шаблона Azure Resource Manager)
- [Create a host pool with PowerShell](create-host-pools-powershell.md) (Создание пула узлов с помощью PowerShell)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Определение пула узлов в качестве пула узлов проверки

Выполните следующие командлеты PowerShell, чтобы определить новый пул узлов в качестве пула узлов проверки. Замените значения в кавычках значениями, относящимися к вашему сеансу:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

Выполните следующий командлет PowerShell, чтобы убедиться, что свойство проверки установлено. Замените значения в кавычках значениями, относящимися к вашему сеансу.

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

Результаты из командлета должны выглядеть подобным образом:

```
    TenantName          : contoso 
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False 
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Обновление расписания

В предварительной версии служба обновляется приблизительно раз в месяц. Если есть серьезные проблемы, критические обновления будут предоставляться чаще.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы создали пул узлов проверки, вы узнаете, как развернуть средство управления ресурсами виртуального рабочего стола Майкрософт и подключиться к нему.

> [!div class="nextstepaction"]
> [Tutorial: Deploy a management tool](./manage-resources-using-ui.md) (Учебник: развертывание средства управления)
