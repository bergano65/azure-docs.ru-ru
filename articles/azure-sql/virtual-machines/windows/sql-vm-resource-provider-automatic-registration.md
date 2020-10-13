---
title: Автоматическая регистрация с помощью поставщика ресурсов виртуальной машины SQL
description: Узнайте, как включить функцию автоматической регистрации для автоматической регистрации всех прошлых и будущих SQL Server виртуальных машин с помощью поставщика ресурсов виртуальной машины SQL, используя портал Azure.
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996886"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Автоматическая регистрация с помощью поставщика ресурсов виртуальной машины SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Включите функцию автоматической регистрации в портал Azure для автоматической регистрации всех текущих и будущих SQL Server на виртуальных машинах Azure с поставщиком ресурсов виртуальной машины SQL в упрощенном режиме.

В этой статье описывается включение функции автоматической регистрации. Кроме того, можно [зарегистрировать одну виртуальную машину](sql-vm-resource-provider-register.md)или [зарегистрировать виртуальные](sql-vm-resource-provider-bulk-register.md) машины с помощью поставщика ресурсов виртуальной машины SQL. 

## <a name="overview"></a>Обзор

[Поставщик ресурсов виртуальной машины SQL](sql-vm-resource-provider-register.md#overview) позволяет управлять SQL Server виртуальной машиной из портал Azure. Кроме того, поставщик ресурсов обеспечивает надежный набор функций, включая [автоматическую установку исправлений](automated-patching.md), [автоматическую архивацию](automated-backup.md), а также мониторинг и возможности управления. Кроме того, вы получите широкий выбор [лицензий](licensing-model-azure-hybrid-benefit-ahb-change.md) и [выпусков](change-sql-server-edition.md). Ранее эти функции были доступны только для образов виртуальных машин SQL Server, развернутых из Azure Marketplace. 

Функция автоматической регистрации позволяет клиентам автоматически регистрировать все текущие и будущие SQL Server виртуальные машины в своей подписке Azure с помощью поставщика ресурсов виртуальной машины SQL. Это отличается от ручной регистрации, которая сосредоточена только на текущих SQL Server виртуальных машинах. 

Автоматическая регистрация SQL Server виртуальные машины в упрощенном режиме. Чтобы воспользоваться полным набором функций, вам по-прежнему необходимо [вручную перейти в режим полного управления](sql-vm-resource-provider-register.md#upgrade-to-full) . 

## <a name="prerequisites"></a>Предварительные требования

Для регистрации виртуальной машины SQL Server с помощью поставщика ресурсов понадобится: 

- [Подписка Azure](https://azure.microsoft.com/free/).
- Модель ресурсов Azure — [Виртуальная машина Windows](../../../virtual-machines/windows/quick-create-portal.md) с [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) , развернутая в общедоступном облаке или Azure для государственных организаций. 


## <a name="enable"></a>Включить

Чтобы включить автоматическую регистрацию SQL Server виртуальных машин в портал Azure, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com).
1. Перейдите на страницу ресурсов [**виртуальных машин SQL**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Выберите **автоматическое SQL Server регистрации виртуальной машины** , чтобы открыть страницу **автоматической регистрации** . 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="Выберите автоматическое SQL Server регистрации виртуальной машины, чтобы открыть страницу автоматической регистрации.":::

1. Выберите подписку в раскрывающемся списке. 
1. Прочтите условия и, если согласны, выберите **я принимаю**. 
1. Выберите **зарегистрировать** , чтобы включить функцию и автоматически зарегистрировать все текущие и будущие SQL Server виртуальные машины с помощью поставщика ресурсов ВИРТУАЛЬНОЙ машины SQL. Это не приведет к перезапуску службы SQL Server на любой из виртуальных машин. 

## <a name="disable"></a>Отключить

Чтобы отключить функцию автоматической регистрации, используйте [Azure CLI](/cli/azure/install-azure-cli) или [Azure PowerShell](/powershell/azure/install-az-ps) . Если функция автоматической регистрации отключена, SQL Server виртуальные машины, добавленные в подписку, необходимо вручную зарегистрировать в поставщике ресурсов виртуальной машины SQL. Регистрация существующих SQL Server виртуальных машин, которые уже зарегистрированы, не будет отменена.



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Чтобы отключить автоматическую регистрацию с помощью Azure CLI, выполните следующую команду: 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы отключить автоматическую регистрацию с помощью Azure PowerShell, выполните следующую команду: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Включить для нескольких подписок

Функцию автоматической регистрации можно включить для нескольких подписок Azure с помощью PowerShell. 

Для этого выполните указанные ниже действия.

1. Сохраните [этот скрипт](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) в `.ps1` файл, например `EnableBySubscription.ps1` . 
1. Перейдите к месту сохранения скрипта с помощью административной командной строки или окна PowerShell. 
1. Подключитесь к Azure ( `az login` ).
1. Выполните скрипт, передав идентификаторов подписки в качестве параметров, таких как   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   Пример: 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

Ошибки регистрации, которые не удалось зарегистрировать, хранятся в `RegistrationErrors.csv` том же каталоге, в котором был сохранен и выполнен `.ps1` скрипт. 

## <a name="next-steps"></a>Дальнейшие действия

Обновите свой режим управления до [полной](sql-vm-resource-provider-register.md#upgrade-to-full) , чтобы воспользоваться преимуществами полного набора функций, предоставляемых поставщиком ресурсов ВИРТУАЛЬНОЙ машины SQL. 
