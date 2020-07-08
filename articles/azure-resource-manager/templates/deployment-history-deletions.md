---
title: Удаление журнала развертывания
description: Описывает, как Azure Resource Manager автоматически удаляет развертывания из журнала развертывания. Развертывания удаляются, если размер журнала близок к превышению лимита в 800.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 70730ce814ebc689d9672952bad7c3dd39b5a7f1
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85981662"
---
# <a name="automatic-deletions-from-deployment-history"></a>Автоматическое удаление из журнала развертывания

Каждый раз при развертывании шаблона сведения о развертывании записываются в журнал развертывания. Каждая группа ресурсов ограничена до 800 развертываний в журнале развертывания.

Azure Resource Manager скоро начнет автоматически удалять развертывания из журнала, так как вы приближаетесь к ограничению. Автоматическое удаление — это изменение предыдущего поведения. Ранее было необходимо вручную удалить развертывания из журнала развертывания, чтобы избежать возникновения ошибки. **Эта функция еще не добавлена в Azure. Мы свяжемся с этим ближайшим изменением, если вы хотите отказаться от него.**

> [!NOTE]
> Удаление развертывания из журнала не влияет ни на один из развернутых ресурсов.
>
> При наличии [CanNotDelete блокировки](../management/lock-resources.md) для группы ресурсов невозможно удалить развертывания для этой группы ресурсов. Необходимо снять блокировку, чтобы воспользоваться преимуществами автоматического удаления в журнале развертывания.

## <a name="when-deployments-are-deleted"></a>При удалении развертываний

Развертывания удаляются из журнала развертывания при достижении 790 развертываний. Azure Resource Manager удаляет небольшой набор самых старых развертываний, чтобы освободить место для будущих развертываний. Большая часть журнала остается неизменной. Самые старые развертывания всегда удаляются первыми.

:::image type="content" border="false" source="./media/deployment-history-deletions/deployment-history.svg" alt-text="Удаления из журнала развертывания":::

Помимо развертываний, при выполнении [операции "что-If](template-deploy-what-if.md) " или проверке развертывания также запускаются удаления.

При присвоении развертыванию имени, совпадающего с именем в журнале, вы сбрасываете его место в журнале. Развертывание перемещается в последнее место в журнале. При [откате к развертыванию](rollback-on-error.md) после возникновения ошибки также выполняется сброс развертывания.

> [!NOTE]
> Если у вашей группы ресурсов уже есть ограничение в 800, следующее развертывание завершится ошибкой. Процесс автоматического удаления запускается немедленно. Вы можете повторить попытку развертывания после короткого ожидания.

## <a name="opt-out-of-automatic-deletions"></a>Отказаться от автоматических удалений

Вы можете отказаться от автоматического удаления из журнала. **Используйте этот параметр, только если требуется самостоятельно управлять журналом развертывания.** Ограничение в 800 развертываний в журнале по-прежнему применяется. Если вы превысили 800 развертываний, вы получите сообщение об ошибке, и развертывание завершится ошибкой.

Чтобы отключить автоматическое удаление, зарегистрируйте `Microsoft.Resources/DisableDeploymentGrooming` флаг компонента. При регистрации флага функции вы отказываетесь от автоматического удаления для всей подписки Azure. Вы не можете отказаться от конкретной группы ресурсов. Чтобы повторно включить автоматическое удаление, отмените регистрацию флага компонента.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для PowerShell используйте командлет [Register-азпровидерфеатуре](/powershell/module/az.resources/Register-AzProviderFeature).

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Чтобы просмотреть текущее состояние подписки, используйте:

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Resources -FeatureName DisableDeploymentGrooming
```

Чтобы повторно включить автоматическое удаление, используйте Azure REST API или Azure CLI.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Для Azure CLI используйте команду [AZ Feature Register](/cli/azure/feature#az-feature-register).

```azurecli-interactive
az feature register --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Чтобы просмотреть текущее состояние подписки, используйте:

```azurecli-interactive
az feature show --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

Чтобы повторно включить автоматическое удаление, используйте команду [AZ Feature Отмена регистрации](/cli/azure/feature#az-feature-unregister).

```azurecli-interactive
az feature unregister --namespace Microsoft.Resources --name DisableDeploymentGrooming
```

# <a name="rest"></a>[REST](#tab/rest)

Для REST API используйте [функции-Register](/rest/api/resources/features/register).

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Чтобы просмотреть текущее состояние подписки, используйте:

```rest
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/register?api-version=2015-12-01
```

Чтобы повторно включить автоматическое удаление, используйте [функции — Отмена регистрации](/rest/api/resources/features/unregister)

```rest
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Resources/features/DisableDeploymentGrooming/unregister?api-version=2015-12-01
```

---

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения о просмотре журнала развертывания см. в статье [Просмотр журнала развертывания с помощью Azure Resource Manager](deployment-history.md).
