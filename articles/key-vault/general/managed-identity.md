---
title: Используйте назначенную системой управляемую инификатор для доступа к Azure Key Vault
description: Узнайте, как создать управляемый идентификатор для приложений Службы приложений и как использовать его для доступа к Использованию ключей Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432129"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Обеспечить аутентификацию Key Vault с управляемой идентификацией

Управляемый имитизм из active-каталога Azure позволяет приложению легко получить доступ к другим защищенным Азовому АДУ ресурсам. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../../active-directory/managed-identities-azure-resources/overview.md) 

В этой статье показано, как создать управляемое удостоверение для приложения Службы Приложений и использовать его для доступа к Использованию ключей Azure Vault. Для приложений, размещенных в Azure VMs, [см.](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования 

Для выполнения этого руководства необходимо иметь следующие ресурсы. 

- Хранилище ключей. Вы можете использовать существующее хранилище ключей или создать новое, выполнив действия, описанные в одном из этих кратких руководств:
   - [Создание хранилища ключей с помощью интерфейса командной строки Azure](../secrets/quick-create-cli.md)
   - [Создание хранилища ключей с помощью Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Создание хранилища ключей с помощью портала Azure](../secrets/quick-create-portal.md)
- Существующее приложение Службы приложений, к которому можно предоставить доступ к хранилищу ключей. Вы можете быстро создать один, выследуя шаги в [документации службы приложений.](../../app-service/overview.md)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview). Кроме того, можно использовать [портал Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Добавление назначаемого системой удостоверения 

Во-первых, необходимо добавить в приложение удостоверение, назначенное системой. 
 
### <a name="azure-portal"></a>Портал Azure 

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать обычное приложение, а затем активировать соответствующую функцию. 

1. Если используется приложение-функция, перейдите к **функциям платформы**. Для других типов приложений прокрутите вниз до группы **параметров** в левой области навигации. 

1. Выберите **Управляемое удостоверение**. 

1. На вкладке **Назначено системой** для параметра **Состояние** установите значение **Вкл**. Выберите команду **Сохранить**. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Этот быстрый запуск требует версии Azure CLI 2.0.4 или позже. Чтобы узнать, какая версия используется сейчас, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Чтобы войти в систему с Azure CLI, используйте команду [входа в az:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Для получения дополнительной информации о опциях входа в систему с Azure CLI можно [ознакомиться с помощью Azure CLI.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) 

Чтобы создать идентификацию для этого приложения, используйте команду идентификации WebApp Azure CLI [az webapp,](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) [назначаемую](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) команду:


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Сделать к `PrincipalId`сведению, которые будут необходимы в следующем разделе.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Предоставить приложению доступ к Key Vault 

### <a name="azure-portal"></a>Портал Azure

1.  Перейдите к ресурсу Key Vault. 

1.  Выберите **политики доступа** и нажмите **«Дополнительная политика доступа».** 

1.  В **секретных разрешений,** выберите **Получить, Список**. 

1.  Выберите **выберите принцип,** и в поле поиска введите имя приложения.  Выберите приложение в списке результатов и нажмите **Выберите**. 

1.  **Нажмите Добавить,** чтобы закончить добавление новой политики доступа.

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Чтобы предоставить приложению доступ к хранилищу ключей, используйте команду [набора keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Azi az, поставляя параметр **ObjectId** с **принципом principalId,** который вы отметили выше.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Дальнейшие действия

- [Безопасность Azure Key Vault: управление идентификацией и доступом](overview-security.md#identity-and-access-management)
- [Обеспечение проверки подлинности Azure Key Vault с помощью политики управления доступом](group-permissions-for-apps.md)
- [Защитите хранилище ключей).](secure-your-key-vault.md)
- [Руководство разработчика Azure Key Vault](developers-guide.md)
- [Рекомендации по Azure Key Vault](best-practices.md)