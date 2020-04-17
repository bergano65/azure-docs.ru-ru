---
title: Шифрование защищенных ресурсов в службе автоматизации Azure
description: Azure Automation защищает безопасные ресурсы, используя несколько уровней шифрования. По умолчанию шифрование осуществляется с помощью ключей, управляемых корпорацией Майкрософт. Клиенты могут настроить свои учетные записи автоматизации, чтобы использовать управляемые клиентами ключи для шифрования. В этой статье описаны детали обоих режимов шифрования и как можно переключаться между ними.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457490"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Шифрование защищенных ресурсов в службе автоматизации Azure

Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы защищены в Azure Automation с использованием нескольких уровней шифрования. На основе клавиши верхнего уровня, используемой для шифрования, существуют две модели шифрования:
-    Использование ключей, управляемых корпорацией Майкрософт
-    Использование ключей, управляемых клиентами

## <a name="microsoft-managed-keys"></a>Ключи, управляемые корпорацией Майкрософт

По умолчанию ваша учетная запись Azure Automation использует ключи, управляемые корпорацией Майкрософт.

Каждый защищенный актив шифруется и хранится в Azure Automation с помощью уникального ключа (ключ шифрования данных), который генерируется для каждой учетной записи автоматизации. Сами эти ключи шифруются и хранятся в Azure Automation с использованием еще одного уникального ключа, который генерируется для каждой учетной записи, называемой ключом шифрования учетной записи (AEK). Эти ключи шифрования учетных записей шифруются и хранятся в Azure Automation с помощью ключей, управляемых корпорацией Майкрософт. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Ключи, управляемые клиентами с Key Vault (предварительный просмотр)

Вы можете управлять шифрованием безопасных активов для вашей учетной записи Automation с помощью собственных ключей. Когда вы указываете ключ, управляемый клиентом, на уровне учетной записи Automation, этот ключ используется для защиты и контроля доступа к ключу шифрования учетной записи для учетной записи Automation. Это, в свою очередь, используется для шифрования и расшифровки всех безопасных активов. Управляемые клиентом клавиши обеспечивают большую гибкость для создания, вращения, отключания и отзыва элементов управления доступом. Вы также можете провести аудит ключей шифрования, используемых для защиты ваших безопасных активов.

Используйте Azure Key Vault для хранения ключей, управляемых клиентами. Вы можете создать свои собственные ключи и хранить их в хранилище ключей, либо использовать AA-иносы Azure Key Vault для генерации ключей.  Для получения дополнительной информации о [What is Azure Key Vault?](../key-vault/general/overview.md) Убежище ключей Azure см.

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Включить ключи, управляемые клиентом для учетной записи Автоматизация

При вводе шифрования с ключами, управляемыми клиентом для учетной записи Automation, Azure Automation обертывает ключ шифрования учетной записи ключом, управляемым клиентом, в хранилище связанных ключей. Включение ключей, управляемых клиентом, не влияет на производительность, и учетная запись шифруется с помощью нового ключа немедленно, без каких-либо задержек.

Новая учетная запись Automation всегда шифруется с помощью ключей, управляемых Корпорацией Майкрософт. Включить управляемые клиентами ключи на момент создания учетной записи невозможно. Управляемые клиентом ключи хранятся в Хранилище ключей Azure, и хранилище ключей должно быть подготовлено с политиками доступа, которые предоставляют ключевые разрешения управляемому идентификатору, связанного с учетной записью Automation. Управляемая идентификация доступна только после создания учетной записи хранилища.

При изменении ключа, используемого для шифрования безопасных активов Azure Automation, путем включения или отключения ключей, управляемых клиентом, обновления ключевой версии или указания другого ключа, шифрование ключа шифрования учетной записи изменяется, но безопасные ресурсы в вашей учетной записи Azure Automation не нуждаются в повторном шифровании.

В следующих трех разделах описывается механика включения ключей, управляемых клиентами для учетной записи Automation. 

> [!NOTE] 
> Для включения ключей, управляемых клиентами, необходимо сделать API API Azure Automation REST с помощью api-версии 2020-01-13-предварительного просмотра

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Предварительные требования к использованию ключей, управляемых клиентами, в Azure Automation

Прежде чем включить ключи, управляемые клиентом для учетной записи Automation, необходимо обеспечить выполнение следующих предварительных запросов:

 - Ключ с клиентом хранится в Хранилище ключей Azure. 
 - Включите свойства **Soft Delete** и Do **Not Purge** на хранилище ключей. Эти функции необходимы для восстановления ключей в случае случайного удаления.
 - Только клавиши RSA поддерживаются шифрованием Azure Automation. Для получения дополнительной информации о ключах можно [ознакомиться с ключами, секретами и сертификатами Azure Key Vault.](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)
- Учетная запись Автоматизации и хранилище ключей могут быть в разных подписках, но они должны быть в одном и том же арендаторе Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Присвоить идентификационный учет учетной записи автоматизации

Чтобы использовать управляемые клиентом ключи с учетной записью Automation, учетная запись Автоматизации должна быть проверена на основе ключей, храняющих ключи, управляемые клиентом. Azure Automation использует систему, назначенную управляемыми идентификаторами, для проверки подлинности учетной записи с помощью Azure Key Vault. Дополнительные сведения об управляемых удостоверениях см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Налажить систему, назначенную управляемой учетной записью автоматизации, с помощью следующего вызова REST API:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Тело запроса:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Система, назначенная идентификация для учетной записи Automation, возвращается в ответ, аналогичный следующему:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Настройка политики доступа Key Vault

После того, как управляемая идентификация будет назначена учетной записи Automation, вы настраиваете доступ к ключу хранилища, на котором хранятся ключи, управляемые клиентом. Azure Automation требует **получения,** **восстановления,** **оберточного ключа,** **UnwrapKey** на клавишах, управляемых клиентом.

Такая политика доступа может быть установлена с помощью следующего вызова REST API:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Тело запроса:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> **Поля tenantId** и **objectId** должны быть обеспечены значениями **идентификации.tenantId** и **identity.principalId** соответственно от ответа управляемой идентификации для учетной записи Автоматизации.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Изменение конфигурации учетной записи Автоматизации для использования ключа, управляемого клиентом

Наконец, вы можете переключить учетную запись Автоматизации с ключей, управляемых Microsft, на клавиши, управляемые клиентом, используя следующий вызов REST API:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Тело запроса:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Пример ответа

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>Управление клиентами управляемых ключей жизненного цикла

### <a name="rotate-customer-managed-keys"></a>Поверните клавиши, управляемые клиентом

Вы можете повернуть ключ, управляемый клиентом, в Azure Key Vault в соответствии с вашими политиками соответствия. При повороте ключа необходимо обновить учетную запись Автоматизации, чтобы использовать новый ключ URI.

Вращение ключа не вызывает повторного шифрования безопасных активов в учетной записи Automation. Дальнейших действий не требуется.

### <a name="revoke-access-to-customer-managed-keys"></a>Отмена доступа к ключам, управляемым клиентами

Чтобы отозвать доступ к ключам, управляемым клиентами, используйте PowerShell или Azure CLI. Для получения дополнительной информации смотрите [ВAzure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) или [Azure Key Vault CLI.](https://docs.microsoft.com/cli/azure/keyvault) Отмена доступа фактически блокирует доступ ко всем защищенным ресурсам в учетной записи Automation, так как ключ шифрования недоступен Azure Automation.

## <a name="next-steps"></a>Следующие шаги

- [Что такое хранилище ключей Azure?](../key-vault/general/overview.md)

- [Сертификация активов в службе автоматизации Azure](shared-resources/certificates.md)

- [Ресурсы учетных данных в службе автоматизации Azure](shared-resources/credentials.md)

- [Ресурсы переменных в службе автоматизации Azure](shared-resources/variables.md)
