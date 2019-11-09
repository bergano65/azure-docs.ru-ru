---
title: Использовать управляемое системой удостоверение для доступа к Azure Key Vault
description: Узнайте, как создать управляемое удостоверение для приложений службы приложений и как использовать его для доступа к Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 75317f73ad724b4ce8ad7a894890b2269bd8c5d0
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837502"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Предоставление Key Vault проверки подлинности с помощью управляемого удостоверения

Управляемое удостоверение из Azure Active Directory позволяет приложению легко получать доступ к другим ресурсам, защищенным с помощью Azure AD. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md) 

В этой статье показано, как создать управляемое удостоверение для приложения службы приложений и использовать его для доступа к Azure Key Vault. Сведения о приложениях, размещенных на виртуальных машинах Azure, см. [в статье Использование управляемого удостоверения, назначенного системой виртуальной машины Windows для доступа к Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Предварительные требования 

Для работы с этим руководством необходимы следующие ресурсы. 

- Хранилище ключей. Вы можете использовать существующее хранилище ключей или создать новое, выполнив действия, описанные в одном из этих кратких руководств:
   - [Создание хранилища ключей с Azure CLI](quick-create-cli.md)
   - [Создание хранилища ключей с Azure PowerShell](quick-create-powershell.md)
   - [Создайте хранилище ключей с портал Azure](quick-create-portal.md).
- Существующее приложение службы приложений, к которому предоставляется доступ к хранилищу ключей. Его можно быстро создать, выполнив действия, описанные в [документации по службе приложений](../app-service/overview.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) или [Azure PowerShell](/powershell/azure/overview). Кроме того, можно использовать [портал Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Добавление назначаемого системой удостоверения 

Во-первых, необходимо добавить назначенное системой удостоверение в приложение. 
 
### <a name="azure-portal"></a>Портал Azure 

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать обычное приложение, а затем активировать соответствующую функцию. 

1. Если используется приложение-функция, перейдите к **функциям платформы**. Для других типов приложений прокрутите вниз до группы **параметров** в левой области навигации. 

1. Выберите **Управляемое удостоверение**. 

1. На вкладке **Назначено системой** для параметра **Состояние** установите значение **Вкл**. Щелкните **Сохранить**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Для этого краткого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать, какая версия используется сейчас, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Чтобы войти с помощью Azure CLI, используйте команду [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Дополнительные сведения о параметрах входа с Azure CLI см. в разделе [Вход с помощью Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Чтобы создать удостоверение для этого приложения, используйте команду Azure CLI [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) или [AZ functionapp Identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) :


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Запишите `PrincipalId`, который потребуется в следующем разделе.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Предоставьте приложению доступ к Key Vault 

### <a name="azure-portal"></a>Портал Azure

1.  Перейдите к Key Vault ресурсу. 

1.  Выберите **политики доступа** и щелкните **Добавить политику доступа**. 

1.  В окне « **секретные разрешения**» выберите **Get, List**. 

1.  Выберите **выбрать субъект**и в поле поиска введите имя приложения.  Выберите приложение в списке результатов и нажмите кнопку **выбрать**. 

1.  Нажмите кнопку **Добавить** , чтобы завершить добавление новой политики доступа.

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Чтобы предоставить приложению доступ к хранилищу ключей, используйте команду Azure CLI [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) , указав параметр **ObjectID** с указанным выше **principalId** .

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Дальнейшие действия

- [Azure Key Vault безопасность: Управление удостоверениями и доступом](overview-security.md#identity-and-access-management)
- [Предоставление Key Vault проверки подлинности с помощью политики управления доступом](key-vault-group-permissions-for-apps.md)
- [Сведения о ключах, секретах и сертификатах](about-keys-secrets-and-certificates.md)
- [Обеспечьте безопасность хранилища ключей](key-vault-secure-your-key-vault.md).
- [Azure Key Vaultное руководством разработчика](key-vault-developers-guide.md)
- [Рекомендации по Azure Key Vault](key-vault-best-practices.md)