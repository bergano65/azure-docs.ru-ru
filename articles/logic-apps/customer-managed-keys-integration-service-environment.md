---
title: Настройка ключей, управляемых клиентом, для шифрования неактивных данных в Исес
description: Создавайте собственные ключи шифрования и управляйте ими для защиты неактивных данных в средах служб Integration Services (Исес) в Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127650"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Настройка ключей, управляемых клиентом, для шифрования неактивных данных в средах службы интеграции (Исес) в Azure Logic Apps

Azure Logic Apps использует хранилище Azure для хранения и автоматического [шифрования неактивных данных](../storage/common/storage-service-encryption.md). Это шифрование защищает ваши данные и помогает удовлетворить ваши обязательства по обеспечению безопасности и соответствия требованиям Организации. По умолчанию служба хранилища Azure использует ключи, управляемые корпорацией Майкрософт, для шифрования данных. Дополнительные сведения о том, как работает шифрование службы хранилища Azure, см. в статье [Шифрование службы хранилища Azure для неактивных данных](../storage/common/storage-service-encryption.md) и [Шифрование неактивных данных Azure](../security/fundamentals/encryption-atrest.md).

При создании [среды службы интеграции (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) для размещения приложений логики и необходимости более эффективного управления ключами шифрования, используемыми службой хранилища Azure, можно настроить, использовать и управлять собственным ключом с помощью [Azure Key Vault](../key-vault/key-vault-overview.md). Эта возможность также называется "создание собственных ключей" (BYOK), а ключ называется "ключом, управляемым клиентом".

В этом разделе показано, как настроить и указать собственный ключ шифрования для использования при создании интегрированной среды сценариев с помощью REST API Logic Apps. Общие действия по созданию интегрированной среды сценариев с помощью Logic Apps REST API см. [в разделе Создание окружения службы интеграции (ISE) с помощью Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Рекомендации

* В настоящее время поддержка управляемых клиентом ключей для интегрированной среды сценариев доступна только в следующих регионах Azure: Западная часть США 2, Восточная часть США и Юго-Центральный регион США.

* Ключ, управляемый клиентом, можно указать *только при создании интегрированной среды сценариев*, а не впоследствии. Этот ключ нельзя отключить после создания интегрированной среды сценариев. В настоящее время не существует поддержки для смены ключа, управляемого клиентом, для интегрированной среды сценариев.

* Для поддержки ключей, управляемых клиентом, для интегрированной среды сценариев требуется включить [управляемое удостоверение, назначенное системой](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) . Это удостоверение позволяет интегрированной среде сценариев выполнить проверку подлинности доступа к ресурсам в других клиентах Azure Active Directory (Azure AD), чтобы не выполнять вход с использованием учетных данных.

* В настоящее время для создания интегрированной среды сценариев, поддерживающей управляемые клиентом ключи, и для которой включено удостоверение, назначенное системой, необходимо вызвать REST API Logic Apps с помощью запроса HTTPS-размещения.

* В течение *30 минут* после отправки запроса HTTPS-размещения, который создает интегрированную среду сценариев, необходимо [предоставить Key Vault доступ к удостоверению, назначенному системой ISE](#identity-access-to-key-vault). В противном случае создание интегрированной среды сценариев завершается сбоем и вызывает ошибку разрешений.

## <a name="prerequisites"></a>предварительные требования

* Те же [условия](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) и [требования, необходимые для обеспечения доступа к интегрированной среде сценариев](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) , как при создании интегрированной среды сценариев в портал Azure

* Хранилище ключей Azure, в котором включены **обратимое удаление** и **не** удаляются свойства.

  Дополнительные сведения о включении этих свойств см. в разделах [обзор Azure Key Vault обратимого удаления](../key-vault/key-vault-ovw-soft-delete.md) и [Настройка ключей, управляемых клиентом, с помощью Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Если вы не знакомы с Azure Key Vault, Узнайте, [как создать хранилище ключей](../key-vault/quick-create-portal.md#create-a-vault) с помощью портал Azure или с помощью команды Azure PowerShell [New-азкэйваулт](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* В хранилище ключей — ключ, который создается со следующими значениями свойств:

  | Свойство | Значение |
  |----------|-------|
  | **Тип ключа** | RSA |
  | **Размер ключа RSA** | 2048 |
  | **Enabled** | Да |
  |||

  ![Создание ключа шифрования, управляемого клиентом](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Дополнительные сведения см. в статьях [Настройка ключей, управляемых клиентом, с помощью Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) или команды Azure PowerShell [Add-азкэйваулткэй](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Средство, которое можно использовать для создания интегрированной среды сценариев путем вызова REST API Logic Apps с запросом HTTPS-размещения. Например, можно использовать [POST](https://www.getpostman.com/downloads/)или создать приложение логики, которое будет выполнять эту задачу.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Создание интегрированной среды сценариев с хранилищем ключей и поддержкой управляемых удостоверений

Чтобы создать интегрированную среду сценариев, вызвав REST API Logic Apps, сделайте запрос HTTPS to:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Для Logic Apps REST API версии 2019-05-01 необходимо сделать собственный запрос HTTP-размещения для соединителей ISE.

Для завершения развертывания обычно требуется два часа. Иногда развертывание может занять до четырех часов. Чтобы проверить состояние развертывания, в [портал Azure](https://portal.azure.com)на панели инструментов Azure щелкните значок уведомления, чтобы открыть панель уведомлений.

> [!NOTE]
> Если развертывание завершается сбоем или вы удаляете интегрированную среду сценариев, то перед освобождением подсетей Azure может пройти до часа. Эта задержка означает, что может потребоваться подождать, прежде чем повторно использовать эти подсети в другой интегрированной среде сценариев.
>
> При удалении виртуальной сети Azure, как правило, занимает до двух часов, прежде чем выпустить подсети, но эта операция может занять больше времени. 
> При удалении виртуальных сетей убедитесь, что все ресурсы не подключены. 
> См. раздел [Удаление виртуальной сети](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Заголовок запроса

В заголовок запроса включите следующие свойства:

* `Content-type`: задайте для этого свойства значение `application/json`.

* `Authorization`. Задайте для этого свойства токен носителя для клиента, который имеет доступ к подписке Azure или группе ресурсов, которые вы хотите использовать.

### <a name="request-body"></a>Тело запроса

В тексте запроса включите поддержку этих дополнительных элементов, предоставив сведения в определении интегрированной среды сценариев:

* Назначенное системой управляемое удостоверение, используемое средой ISE для доступа к хранилищу ключей
* Ваше хранилище ключей и управляемый клиентом ключ, который вы хотите использовать.

#### <a name="request-body-syntax"></a>Синтаксис текста запроса

Ниже приведен синтаксис текста запроса, описывающий свойства, используемые при создании интегрированной среды сценариев.

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

#### <a name="request-body-example"></a>Пример текста запроса

В этом примере текста запроса показаны примеры значений:

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

В течение *30 минут* после отправки запроса HTTP-размещения для создания интегрированной среды сценариев необходимо добавить в хранилище ключей политику доступа для назначенного системой удостоверения. В противном случае создание интегрированной среды сценариев завершится ошибкой, и вы получите ошибку разрешений. 

Для выполнения этой задачи можно использовать команду Azure PowerShell [Set-азкэйваултакцессполици](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) . также можно выполнить следующие действия в портал Azure.

1. В [портал Azure](https://portal.azure.com)откройте хранилище ключей Azure.

1. В меню хранилища ключей выберите **политики доступа** > **Добавить политику доступа**, например:

   ![Добавление политики доступа для управляемого удостоверения, назначенного системой](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. После открытия области **Добавить политику доступа** выполните следующие действия.

   1. Выберите следующие параметры:

      | Параметр | Значения |
      |---------|--------|
      | **Настроить в списке шаблон (необязательно)** | Управление ключами |
      | **Основные разрешения** | **операции управления ключами**- : Get, List <p><p>- **криптографические операции**: разобертывание ключа, ключа переноса |
      |||

      ![Выберите "Управление ключами" > "разрешения ключа".](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. В **параметре выбрать субъект**выберите **нет выбрано**. После открытия области **участника** в поле поиска найдите и выберите интегрированную среду сценариев. Когда все будет готово, нажмите кнопку **выбрать** > **Добавить**.

      ![Выберите интегрированную среду сценариев для использования в качестве участника](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Завершив работу с областью **политики доступа** , нажмите кнопку **сохранить**.

Дополнительные сведения см. в статье [предоставление Key Vault проверки подлинности с помощью управляемого удостоверения](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Дальнейшие действия

* См. дополнительные сведения об [Azure Key Vault](../key-vault/key-vault-overview.md).