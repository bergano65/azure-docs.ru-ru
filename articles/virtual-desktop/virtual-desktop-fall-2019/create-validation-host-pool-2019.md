---
title: Обновление службы в пуле узлов Виртуального рабочего стола Windows (осень 2019 г.) — Azure
description: Узнайте, как создать пул узлов проверки для мониторинга обновлений службы перед их развертыванием в рабочую среду.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 806c3396d9188ea6abc5f779a26d99247d802ebe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527596"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-fall-2019-release"></a>Руководство по Руководство по созданию пула узлов для проверки обновлений службы (выпуск за осень 2019 г.)

>[!IMPORTANT]
>Это содержимое применимо к выпуску за осень 2019 года, который не поддерживает объекты Azure Resource Manager для Виртуального рабочего стола Windows. Если вы хотите обеспечить управление объектами Azure Resource Manager для Виртуального рабочего стола Windows, представленными в обновлении за весну 2020 г., см. [эту статью](../create-validation-host-pool.md).

Пулы узлов — это коллекция, состоящая из одной или нескольких идентичных виртуальных машин в средах клиента Виртуального рабочего стола Windows. Перед развертыванием пулов узлов в рабочую среду настоятельно рекомендуется создать пул узлов проверки. Обновления применяются в первую очередь к пулам узлов проверки, что позволяет отслеживать обновления служб перед их развертыванием в рабочую среду. Без пула узлов проверки вы не сможете обнаружить изменения, которые могут привести к ошибкам и, следовательно, к простоям для пользователей в рабочей среде.

Чтобы обеспечить совместимость приложения с последними обновлениями, пул узлов проверки должен быть максимально похож на пулы узлов в рабочей среде. Пользователи должны подключаться к пулу узлов проверки так же часто, как и к рабочему пулу узлов. Если в пуле узлов есть автоматическое тестирование, необходимо включить его в пул узлов проверки.

Вы можете выполнять отладку ошибок в пуле узлов проверки с помощью [функции диагностики](diagnostics-role-service-2019.md) или сведений из [статей по устранению неполадок Виртуального рабочего стола Windows](troubleshoot-set-up-overview-2019.md).

>[!NOTE]
> Мы рекомендуем не менять расположение пула узлов проверки, чтобы протестировать все будущие обновления.

Перед началом работы при необходимости [скачайте и импортируйте модуль PowerShell для Виртуального рабочего стола Windows](/powershell/windows-virtual-desktop/overview/). После этого выполните следующий командлет, чтобы войти в учетную запись:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>Создание пула узлов

Пул узлов можно создать, следуя инструкциям в любой из этих статей:
- [Руководство. Создание пула узлов с помощью Azure Marketplace](create-host-pools-azure-marketplace-2019.md)
- [Create a host pool with an Azure Resource Manager template](create-host-pools-arm-template.md) (Создание пула узлов с помощью шаблона Azure Resource Manager)
- [Create a host pool with PowerShell](create-host-pools-powershell-2019.md) (Создание пула узлов с помощью PowerShell)

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
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>Обновление расписания

Обновления службы выполняются ежемесячно. Если есть серьезные проблемы, критические обновления будут предоставляться чаще.

## <a name="next-steps"></a>Дальнейшие действия

Создав пул узлов проверки, вы можете узнать, как использовать решение "Работоспособность служб Azure" для мониторинга развертывания Виртуального рабочего стола Windows. 

> [!div class="nextstepaction"]
> [Настройка оповещений служб](set-up-service-alerts-2019.md)
