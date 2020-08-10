---
title: Создание панели мониторинга портала Azure с помощью PowerShell
description: Узнайте, как создать панель мониторинга на портале Azure с помощью Azure PowerShell.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: 14bf4129a73de2b51a575c3a0a7a3b9c86387b2c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87440727"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Краткое руководство. Создание панели мониторинга портала Azure с помощью PowerShell

Панель мониторинга на портале Azure — это организованное и упорядоченное представление облачных ресурсов. В этой статье рассматривается создание панели мониторинга с помощью модуля PowerShell Az.Portal.
На панели мониторинга отображаются сведения о производительности виртуальной машины, а также некоторые статические данные и ссылки.

## <a name="requirements"></a>Требования

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

Если вы решили использовать PowerShell локально, для работы с этой статьей установите модуль PowerShell Az и подключитесь к учетной записи Azure с помощью командлета [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount). См. сведения об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Так как модуль PowerShell **Az.Portal** предоставляется в режиме предварительной версии, его нужно установить отдельно от модуля Az с помощью командлета `Install-Module`. Как только этот модуль PowerShell станет общедоступным, он будет включен в один из будущих выпусков Az PowerShell и встроен в Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Выбор требуемой подписки Azure

Если вы используете несколько подписок Azure, выберите ту, за ресурсы в которой будут выставляться счета. Выберите требуемую подписку с помощью командлета [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Определение переменных

Некоторый фрагменты данных будут многократно использоваться. Создайте переменные для хранения информации.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте [группу ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) с помощью командлета [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов на основе имени в переменной `$resourceGroupName` в регионе, указанном в переменной `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Создание виртуальной машины

Для панели мониторинга, которую вы создадите с помощью этого краткого руководства, требуется виртуальная машина. Чтобы создать виртуальную машину, выполните следующие действия.

Сохраните в переменной учетные данные входа для виртуальной машины. Пароль должен быть сложным. Вам нужно создать новые имя пользователя и пароль, а не вводить данные от существующей учетной записи, например используемой для входа в Azure. Дополнительные сведения см. в описании требований к [имени пользователя](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) и [паролю](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Создайте виртуальную машину.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

Начнется развертывание виртуальной машины, которое обычно занимает несколько минут. По завершении развертывания перейдите к следующему разделу.

## <a name="download-the-dashboard-template"></a>Скачивание шаблона панели мониторинга

Так как панели мониторинга Azure являются ресурсами, то их можно представить в виде кода JSON. Следующий код скачивает JSON-представление примера панели мониторинга. Дополнительные сведения см. в статье [Структура панелей мониторинга Azure](/azure/azure-portal/azure-portal-dashboards-structure).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Настройка шаблона

Настройте скачанный шаблон, выполнив следующий код.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Дополнительные сведения см. в [справочнике по шаблону мониторинга портала Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Развертывание шаблона панели мониторинга

Для развертывания шаблона непосредственно из PowerShell можно использовать командлет `New-AzPortalDashboard`, который входит в модуль AZ.Portal.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Просмотр развернутых ресурсов

Проверьте, что панель мониторинга успешно создана.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Убедитесь, что данные от виртуальной машины поступают на портал Azure.

1. На портале Azure выберите **Панель мониторинга**.

   ![Переход к панели мониторинга на портале Azure](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. На странице панели мониторинга выберите **Простая панель мониторинга виртуальной машины**.

   ![Переход к простой панели мониторинга виртуальной машины](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Проверьте панель мониторинга. Вы увидите, что часть содержимого является статическим, но здесь также представлены диаграммы, отображающие производительность виртуальной машины.

   ![Обзор простой панели мониторинга виртуальной машины](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить виртуальную машину и связанную с ней панель мониторинга, удалите связанную группу ресурсов.

> [!CAUTION]
> Следующий пример удаляет указанную группу ресурсов и все содержащиеся в ней ресурсы.
> Если в указанной группе ресурсов существуют другие ресурсы, кроме созданных для этой статьи, они также будут удалены.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о командлетах модуля PowerShell Az.Portal см. в следующей статье:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell. Командлеты панели мониторинга портала](https://docs.microsoft.com/powershell/module/Az.Portal/)
