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
ms.openlocfilehash: d4a2d9e43dadc53008c04b44ea1dda9cb337da99
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308344"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>Автоматическая регистрация с помощью поставщика ресурсов виртуальной машины SQL
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Включите функцию автоматической регистрации в портал Azure для автоматической регистрации всех текущих и будущих SQL Server на виртуальных машинах Azure с поставщиком ресурсов виртуальной машины SQL в упрощенном режиме. При регистрации в поставщике ресурсов виртуальной машины SQL устанавливается [расширение агента IaaS SQL](sql-server-iaas-agent-extension-automate-management.md).

В этой статье описывается включение функции автоматической регистрации. Кроме того, можно [зарегистрировать одну виртуальную машину](sql-vm-resource-provider-register.md)или [зарегистрировать виртуальные](sql-vm-resource-provider-bulk-register.md) машины с помощью поставщика ресурсов виртуальной машины SQL. 

## <a name="overview"></a>Обзор

Регистрация SQL Server виртуальной машины с помощью поставщика ресурсов виртуальной машины SQL устанавливает [расширение агента IaaS SQL](sql-server-iaas-agent-extension-automate-management.md). 

Если включена автоматическая регистрация, задание выполняется ежедневно, чтобы определить, установлены ли SQL Server на всех незарегистрированных виртуальных машинах в подписке. Это делается путем копирования двоичных файлов расширения агента IaaS SQL на виртуальную машину, а затем запуска одноразовой программы, которая проверяет наличие куста реестра SQL Server. При обнаружении SQL Server Hive виртуальная машина регистрируется в [поставщике ресурсов виртуальной машины SQL](sql-vm-resource-provider-register.md) в упрощенном режиме. Если в реестре нет SQL Server Hive, двоичные файлы будут удалены.

После включения автоматической регистрации для подписки все текущие и будущие виртуальные машины, на которых установлено SQL Server, будут зарегистрированы в поставщике ресурсов виртуальной машины SQL в упрощенном режиме. Чтобы воспользоваться полным набором функций, вам по-прежнему необходимо [вручную перейти в режим полного управления](sql-vm-resource-provider-register.md#upgrade-to-full) . 

> [!IMPORTANT]
> Расширение агента IaaS SQL собирает данные для предоставления клиентам дополнительных преимуществ при использовании SQL Server в виртуальных машинах Azure. Корпорация Майкрософт не будет использовать эти данные для аудита лицензирования без согласия клиента. Дополнительные сведения см. в разделе [SQL Server о конфиденциальности](/sql/sql-server/sql-server-privacy#non-personal-data) .

## <a name="prerequisites"></a>Предварительные требования

Для регистрации виртуальной машины SQL Server с помощью поставщика ресурсов понадобится: 

- [Подписка Azure](https://azure.microsoft.com/free/) и, как минимум, разрешения [роли участника](../../../role-based-access-control/built-in-roles.md#all) .
- Модель ресурсов Azure — это [Виртуальная машина Windows Server 2008 R2 (или более поздней версии)](../../../virtual-machines/windows/quick-create-portal.md) с [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) , развернутая в общедоступном облаке или Azure для государственных организаций. Windows Server 2008 не поддерживается. 


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

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Чтобы отключить автоматическую регистрацию с помощью Azure PowerShell, выполните следующую команду: 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>Включить для нескольких подписок

Функцию автоматической регистрации можно включить для нескольких подписок Azure с помощью PowerShell. 

Для этого выполните следующие действия.

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
