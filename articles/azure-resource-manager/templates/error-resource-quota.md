---
title: Ошибки квоты
description: Описывает, как устранять ошибки квот ресурсов при развертывании ресурсов с помощью Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: d36f7d12f432839760b68c85e1c5761e0caf8d85
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659361"
---
# <a name="resolve-errors-for-resource-quotas"></a>Устранение ошибок квот ресурсов

В этой статье описываются ошибки квоты, которые могут возникнуть при развертывании ресурсов.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Симптом

При развертывании шаблона, создающего ресурсы, превышающие ваши квоты Azure, возникнет ошибка развертывания следующего вида.

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Вы можете также увидеть следующую ошибку.

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Причина

Квоты применяются к группам ресурсов, подпискам, учетным записям и другим областям. Например, для подписки может быть настроено ограничение числа ядер для региона. При попытке развертывания виртуальной машины с большим количеством ядер, чем разрешено, вы получите сообщение о том, что квота превышена.
Дополнительные сведения о квотах Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Устранение неисправностей

### <a name="azure-cli"></a>Интерфейс командной строки Azure

Чтобы узнать квоты виртуальной машины, выполните команду `az vm list-usage` в Azure CLI.

```azurecli
az vm list-usage --location "South Central US"
```

Возвращаемые данные:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

Чтобы узнать квоты виртуальной машины, выполните команду **Get-AzVMUsage** в PowerShell.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Возвращаемые данные:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Решение

Если необходимо увеличить квоту, перейдите на портал и отправьте запрос в службу поддержки. В службе поддержки запросите увеличение квоты для региона, в котором требуется осуществить развертывание.

> [!NOTE]
> Следует помнить, что для групп ресурсов квоты устанавливаются для каждого отдельного региона, а не для всей подписки. Если необходимо развернуть 30 ядер в западной части США, необходимо запросить 30 ядер управления ресурсами в этом регионе. Если необходимо развернуть 30 ядер в любом из регионов, к которым у вас есть доступ, следует запросить 30 ядер Resource Manager во всех регионах.
>
>

1. Выберите **Подписки**.

   ![Подписки](./media/error-resource-quota/subscriptions.png)

2. Выберите подписку, которая требует увеличенную квоту.

   ![Выбор подписки](./media/error-resource-quota/select-subscription.png)

3. Выберите **Использование и квоты**.

   ![Использование и квоты](./media/error-resource-quota/select-usage-quotas.png)

4. В правом верхнем углу выберите **Запросить увеличение**.

   ![Запросить увеличение](./media/error-resource-quota/request-increase.png)

5. Заполните формы для типа квоты, которую необходимо увеличить.

   ![Заполнение формы](./media/error-resource-quota/forms.png)