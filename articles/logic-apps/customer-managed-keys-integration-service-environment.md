---
title: Настройка ключей, управляемых клиентами, для шифрования данных в состоянии покоя в IsEs
description: Создавайте и управляйте собственными ключами шифрования для защиты данных в состоянии покоя для интеграционных сервисных сред (ISEs) в приложениях Логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127650"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Настройка ключей, управляемых клиентами, для шифрования данных в состоянии покоя для интеграционных сервисных сред (ISEs) в приложениях логики Azure

Azure Logic Apps полагается на Хранилище Azure для хранения и автоматического [шифрования данных в состоянии покоя.](../storage/common/storage-service-encryption.md) Это шифрование защищает ваши данные и помогает выполнять свои обязательства по обеспечению безопасности и соответствию организации. По умолчанию Azure Storage использует управляемые корпорацией Майкрософт ключи для шифрования данных. Для получения дополнительной информации о том, как работает шифрование хранилища Azure, смотрите [шифрование хранилища Azure для данных в состоянии покоя](../storage/common/storage-service-encryption.md) и [шифрование данных Azure.at-Rest.](../security/fundamentals/encryption-atrest.md)

При создании [среды интеграционных служб (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) для размещения логических приложений и требуется больший контроль над ключами шифрования, используемыми Azure Storage, вы можете настроить, использовать и управлять собственным ключом с помощью [Azure Key Vault.](../key-vault/key-vault-overview.md) Эта возможность также известна как "Принесите свой собственный ключ" (BYOK), а ваш ключ называется "ключ, управляемый клиентом".

Эта тема показывает, как настроить и указать свой собственный ключ шифрования для использования при создании ISE с помощью Logic Apps REST API. Для общих шагов по созданию ISE через Logic Apps REST API [см. Создать среду интеграционных служб (ISE) с помощью Logic Apps REST API.](../logic-apps/create-integration-service-environment-rest-api.md)

## <a name="considerations"></a>Рекомендации

* В настоящее время ключевая поддержка ISE, управляемая клиентами, доступна только в этих регионах Azure: Западная часть США 2, Восточная часть США и южная центральная часть США

* Вы можете указать ключ, управляемый *клиентом, только при создании ISE,* а не после этого. Вы не можете отключить этот ключ после создания ISE. В настоящее время не существует поддержки для ротации ключа, управляемого клиентом для ISE.

* Для поддержки ключей, управляемых клиентами, вашему ISE требуется, чтобы его [система назначила управляемое удостоверение личности.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Эта идентификация позволяет ISE аутентифицировать доступ к ресурсам в других ресурсах Active Directory (Azure AD), так что вам не придется входить в систему с вашими учетными данными.

* В настоящее время для создания ISE, который поддерживает управляемые клиентами ключи и имеет свою системную идентификацию, необходимо позвонить в Logic Apps REST API с помощью запроса HTTPS PUT.

* В течение *30 минут* после отправки запроса HTTPS PUT, который создает ваш ISE, вы должны предоставить доступ к хранилищу [ключей к установленной системой вашей системой идентификации.](#identity-access-to-key-vault) В противном случае создание ISE выходит из строя и выбрасывает ошибку разрешений.

## <a name="prerequisites"></a>Предварительные требования

* Те же [предпосылки](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) и [требования для обеспечения доступа к ВАШЕМу ISE,](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) как при создании ISE на портале Azure

* Хранилище ключей Azure с включенными свойствами **Soft Delete** и Do **Not Purge**

  Для получения дополнительной информации о включении этих свойств, см [Azure Key Vault с помощью удаленного удаления](../key-vault/key-vault-ovw-soft-delete.md) и [настройки ключей, управляемых клиентами, с помощью Azure Key Vault.](../storage/common/storage-encryption-keys-portal.md) Если вы новичок в Azure Key Vault, узнайте, [как создать хранилище ключей](../key-vault/quick-create-portal.md#create-a-vault) с помощью портала Azure или с помощью команды Azure PowerShell, [New-AzKeyVault.](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault)

* В хранилище ключей создается ключ, созданный с помощью следующих значений свойств:

  | Свойство | Значение |
  |----------|-------|
  | **Тип ключа** | RSA |
  | **Размер ключа RSA** | 2048 |
  | **Включен** | Да |
  |||

  ![Создайте ключ шифрования, управляемый клиентом](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Для получения дополнительной информации смотрите [на настройку ключей, управляемых клиентами, с помощью клавиатуры Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) или команды Azure PowerShell, [Add-AzKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey)

* Инструмент, который можно использовать для создания ISE, позвонив в API Logic Apps REST с запросом HTTPS PUT. Например, можно использовать [Postman](https://www.getpostman.com/downloads/)или создать логическое приложение, выполняя задачу.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Создание ISE с хранилищем ключей и управляемой поддержкой идентификации

Чтобы создать ISE, позвонив в Logic Apps REST API, сделайте этот запрос HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Логика Apps REST API 2019-05-01 версия требует, чтобы вы сделали свой собственный запрос HTTP PUT для разъемов ISE.

Развертывание обычно занимает в течение двух часов. Иногда развертывание может занять до четырех часов. Чтобы проверить состояние развертывания, на [портале Azure](https://portal.azure.com)на панели инструментов Azure выберите значок уведомлений, который открывает панель уведомлений.

> [!NOTE]
> Если развертывание завершится ошибкой или вы удалите среду службы интеграции, освобождение подсетей в Azure может занять до одного часа. Эта задержка означает, что вам, возможно, придется подождать, прежде чем повторно использовать эти подсети в другом ISE.
>
> Если вы удалите виртуальную сеть, Azure обычно занимает до двух часов, прежде чем выпустить ваши подсети, но эта операция может занять больше времени. 
> При удалянии виртуальных сетей убедитесь, что ресурсы по-прежнему не подключены. 
> Смотрите [Удалить виртуальную сеть](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Заголовок запроса

В заголовке запроса включите следующие свойства:

* `Content-type`: Установите это `application/json`значение свойства для .

* `Authorization`: Установите это значение свойства маркеру носителя для клиента, который имеет доступ к подписной или ресурсной группе Azure, которую вы хотите использовать.

### <a name="request-body"></a>Тело запроса

В органе запросов включите поддержку этих дополнительных элементов, предоставив их информацию в вашем определении ISE:

* Управляемая система ими ISE использует доступ к хранилищу ключей
* Ваш ключ хранилища и клиент-управляемый ключ, который вы хотите использовать

#### <a name="request-body-syntax"></a>Синтаксис текста запроса

Вот синтаксис тела запроса, который описывает свойства, которые можно использовать при создании ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Пример тела запроса

В этом корпусе запроса пример аудируется значения выборки:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Предоставление доступа к хранилищу ключей

В течение *30 минут* после отправки запроса HTTP PUT на создание ISE необходимо добавить политику доступа к хранилищу ключа для идентификации системы ISE. В противном случае, создание для вашего ISE не удается, и вы получите ошибку разрешения. 

Для этой задачи можно использовать команду Azure PowerShell [Set-AzKeyVaultAccessPolicy,](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) либо следовать этим шагам на портале Azure:

1. На [портале Azure](https://portal.azure.com)откройте хранилище ключей Azure.

1. В меню хранилища ключей выберите **политики** > доступа**Добавить политику доступа,** например:

   ![Добавление политики доступа для системно-назначенного управляемого удостоверения](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. После открытия панели **политики добавления** выполните следующие действия:

   1. Выберите следующие варианты:

      | Параметр | Значения |
      |---------|--------|
      | **Настройка из списка шаблонов (необязательный)** | Управление ключами |
      | **Ключевые разрешения** | - **Ключевые операции управления**: Получить, Список <p><p>- **Криптографические операции**: Разверните ключ, Оберните ключ |
      |||

      ![Выберите "Ключевое управление" > "Ключевые разрешения"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. Для **выбора основного**, выберите **Нет выбран.** После открытия **панели Principal,** в поле поиска, найдите и выберите ISE. Когда вы закончите, выберите **Выберите** > **Добавить**.

      ![Выберите ISE для использования в качестве основного](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Когда вы закончите с панелью **политик access,** выберите **Сохранить**.

Для получения дополнительной информации [см.](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault)

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения об [Azure Key Vault](../key-vault/key-vault-overview.md).