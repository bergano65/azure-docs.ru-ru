---
title: Настройте свой ключ для шифрования данных Azure Service Bus в состоянии покоя
description: В этой статье содержится информация о том, как настроить свой собственный ключ для шифрования данных Azure Service Bus.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: aschhab
ms.openlocfilehash: aeb9a9730ddc61793e49c9e042906457e0068d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624093"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Настройка ключей, управляемых клиентом, для шифрования данных Azure Service Bus в состоянии покоя с помощью портала Azure
Служба обслуживания Azure Premium обеспечивает шифрование данных в состоянии покоя с помощью шифрования службы хранения данных Azure (Azure SSE). Сервис Bus Premium полагается на Хранилище Azure для хранения данных, и по умолчанию все данные, хранящиеся в Azure Storage, шифруются с помощью ключей, управляемых Корпорацией Майкрософт. 

## <a name="overview"></a>Обзор
Теперь Azure Service Bus поддерживает возможность шифрования данных в состоянии покоя либо с помощью ключей, управляемых корпорацией Майкрософт, либо с ключами, управляемыми клиентом (Bring Your Own Key - BYOK). эта функция позволяет создавать, вращать, отключать и отменять доступ к управляемым клиентом ключам, которые используются для шифрования Azure Service Bus в состоянии покоя.

Включение функции BYOK — это одноразовый процесс настройки в пространстве имен.

> [!NOTE]
> Есть некоторые предостережения к клиенту управляемый ключ для шифрования стороне службы. 
>   * Эта функция поддерживается уровнем [Azure Service Bus Premium.](service-bus-premium-messaging.md) Он не может быть включен для стандартных областей имен service Bus.
>   * Шифрование может быть включено только для новых или пустых областей имен. Если пространство имен содержит данные, то операция шифрования выйдет из строя.

Вы можете использовать Azure Key Vault для управления ключами и аудита использования ключей. Вы можете создать свои собственные ключи и хранить их в хранилище ключей, либо использовать AA-иносы Azure Key Vault для генерации ключей. Для получения дополнительной информации о [What is Azure Key Vault?](../key-vault/key-vault-overview.md) Убежище ключей Azure см.

В этой статье показано, как настроить хранилище ключей с помощью ключей, управляемых клиентом, с помощью портала Azure. Чтобы узнать, как создать хранилище ключей с помощью портала Azure, [см.](../key-vault/quick-create-portal.md)

> [!IMPORTANT]
> Использование управляемых клиентами ключей с azure Service Bus требует, чтобы хранилище ключей было настроено на два необходимых свойства. Они: **Мягкий Удалить** и **не очистить**. Эти свойства включены по умолчанию при создании нового хранилища ключей на портале Azure. Однако, если необходимо включить эти свойства в существующем хранилище ключей, необходимо использовать либо PowerShell, либо Azure CLI.

## <a name="enable-customer-managed-keys"></a>Включить ключи, управляемые клиентом
Для включения ключей, управляемых клиентами, на портале Azure выполните следующие действия:

1. Перейдите к вашему названию Сервисного автобуса Premium.
2. На странице **«Настройки»** в пространстве имен S-Sbus выберите **шифрование.**
3. Выберите **шифрование ключей, управляемое Заказчиком, в состоянии покоя,** как показано на следующем изображении.

    ![Включить управляемый ключом клиента](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Настройка хранилища ключей с ключами

После включения ключей, управляемых клиентом, необходимо связать управляемый ключ омичи клиента с пространством имен Azure Service Bus. Сервисный автобус поддерживает только Azure Key Vault. Если в предыдущем разделе **шифрование с помощью ключевого** опции с помощью клиента, необходимо импортировать ключ в Хранилище ключей Azure. Кроме того, ключи должны иметь **soft Delete** и Do **Not Purge** настроенный для ключа. Эти настройки можно настроить с помощью [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) или [CLI.](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)

1. Чтобы создать новое хранилище ключей, следуйте за [quickstart](../key-vault/key-vault-overview.md)Key Vault Azure. Для получения дополнительной информации об импорте существующих ключей, [см.](../key-vault/about-keys-secrets-and-certificates.md)
1. Чтобы включить как мягкое удаление и очистку защиты при создании хранилища, используйте [команду создания клавиатуры az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Чтобы добавить защиту от очистки в существующее хранилище (у которого уже есть мягкое удаление), используйте команду [обновления az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Создавайте ключи следующим образом:
    1. Чтобы создать новый ключ, выберите **Создать или импортировать** из меню **Ключи** в разделе **Параметры**.
        
        ![Выберите кнопку «Создание/импорт»](./media/configure-customer-managed-key/select-generate-import.png)

    1. Задайте **Параметры**, чтобы **Создать** и присвоить имя для ключа.

        ![Создание ключа](./media/configure-customer-managed-key/create-key.png) 

    1. Теперь вы можете выбрать этот ключ, чтобы связать с пространством имен Service Bus для шифрования из списка выпадающих. 

        ![Выберите ключ из хранилища ключей](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > При избыточности можно добавить до 3 клавиш. В случае, если срок действия одного из ключей истек или недоступен, другие ключи будут использоваться для шифрования.
        
    1. Заполните детали для ключа и нажмите **Выберите**. Это позволит шифрование данных в состоянии покоя на пространстве имен с клиентом управляемых ключей. 


    > [!IMPORTANT]
    > Если вы хотите использовать управляемый клиентом ключ вместе с восстановлением последствий аварии Geo, пожалуйста, просмотрите ниже - 
    >
    > Для обеспечения шифрования в состоянии покоя с помощью управляемого ключом клиента на данном Azure KeyVault настроена [политика доступа](../key-vault/key-vault-secure-your-key-vault.md) для управляемой идентификации автобуса службы. Это обеспечивает контролируемый доступ к Azure KeyVault из пространства имен Azure Service Bus.
    >
    > Из-за этого:
    > 
    >   * Если [аварийно-восстановительное решение Geo](service-bus-geo-dr.md) уже включено для пространства имен Service Bus и вы хотите включить управляемый ключ омило способом обслуживания клиентов, 
    >     * Перерыв спаривания
    >     * [Настройка политики доступа](../key-vault/managed-identity.md) для управляемого идентификатора для основных и вторичных областей имен в хранилище ключей.
    >     * Настройка шифрования в основном пространстве имен.
    >     * Повторно ежка первичных и вторичных областей имен.
    > 
    >   * Если вы хотите включить Geo-DR в пространстве имен Service Bus, где уже настроен ключ, управляемый клиентом, затем -
    >     * [Настройка политики доступа](../key-vault/managed-identity.md) для управляемого итог для вторичного пространства имен в хранилище ключей.
    >     * Соедините первичные и вторичные пространства имен.


## <a name="rotate-your-encryption-keys"></a>Поверните ключи шифрования

Вы можете повернуть ключ в хранилище ключей с помощью механизма вращения ключей Azure Vaults. Для получения дополнительной [информации см.](../key-vault/key-vault-key-rotation-log-monitoring.md) Даты активации и истечения также могут быть установлены для автоматизации вращения ключей. Сервис Service Bus обнаружит новые ключевые версии и начнет использовать их автоматически.

## <a name="revoke-access-to-keys"></a>Отмена доступа к ключам

Отмена доступа к ключам шифрования не будет удавлять данные из Service Bus. Однако доступ к данным не доступен из пространства имен Service Bus. Вы можете отозвать ключ шифрования через политику доступа или удаляя ключ. Узнайте больше о политиках доступа и защите хранилища ключей от [безопасного доступа к хранилищу ключей.](../key-vault/key-vault-secure-your-key-vault.md)

После отзыва ключа шифрования служба Service Bus в зашифрованном пространстве имен станет неработоспособной. Если доступ к ключу включен или удаленный ключ будет восстановлен, служба Service Bus подберет ключ, чтобы вы могли получить доступ к данным из зашифрованного пространства имен Service Bus.

## <a name="use-resource-manager-template-to-enable-encryption"></a>Используйте шаблон менеджера ресурсов для включения шифрования
В этом разделе показано, как выполнять следующие задачи с помощью **шаблонов Azure Resource Manager.** 

1. Создайте пространство имен **премиум-сервиса** с **управляемой идентификацией службы.**
2. Создайте **хранилище ключей** и предоставите идентификационным данным службы доступ к хранилищу ключей. 
3. Обновление пространства имен Service Bus с информацией о хранилище ключей (ключ/значение). 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Создание пространства имен премиум-службы с управляемой идентификацией сервиса
В этом разделе показано, как создать пространство имен Azure Service Bus с управляемой идентификатором службы с помощью шаблона Менеджера ресурсов Azure и PowerShell. 

1. Создайте шаблон менеджера ресурсов Azure для создания пространства для премиум-уровня сервиса с управляемой идентификацией службы. Название файла: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Создайте файл параметра шаблона под названием: **CreateServiceBusPremiumSpaceSpaceParams.json**. 

    > [!NOTE]
    > Измените следующие значения: 
    > - `<ServiceBusNamespaceName>`- Название пространства имен вашего сервисного автобуса
    > - `<Location>`- Расположение пространства имен вашего сервисного автобуса

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Запустите следующую команду PowerShell для развертывания шаблона для создания пространства имен премиум-класса Service Bus. Затем изыскните идентификатор пространства имен Service Bus, чтобы использовать его позже. Замените `{MyRG}` имя группы ресурсов перед запуском команды.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Доступ к идентификационным данным Grant Service Bus к хранилищу ключей

1. Запустите следующую команду, чтобы создать хранилище ключей с **защитой от очистки** и **с включенным мягким удалением.** 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (ИЛИ)
    
    Выполнить следующую команду для обновления **существующего хранилища ключей.** Указать значения для группы ресурсов и имен хранилища ключей перед запуском команды. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Установите политику доступа к хранилищу ключей таким образом, чтобы управляемое удостоверение пространства имен Service Bus мог получить доступ к ключевой ценности в хранилище ключей. Используйте идентификатор пространства имен Service Bus из предыдущего раздела. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Шифрование данных в пространстве имен Service Bus с помощью ключа, управляемого клиентом, из хранилища ключей
Вы сделали следующие шаги до сих пор: 

1. Создано премиальное пространство имен с управляемой идентификацией.
2. Создайте хранилище ключей и предоставите управляемый доступ к хранилищу ключей. 

На этом этапе вы обновите пространство имен Service Bus с информацией о хранилище ключей. 

1. Создайте файл JSON под названием **UpdateServiceBusNamespaceWithEncryption.json** со следующим содержанием: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Создайте файл параметров шаблона: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Измените следующие значения: 
    > - `<ServiceBusNamespaceName>`- Название пространства имен вашего сервисного автобуса
    > - `<Location>`- Расположение пространства имен вашего сервисного автобуса
    > - `<KeyVaultName>`- Имя вашего хранилища ключей
    > - `<KeyName>`- Имя ключа в хранилище ключей  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Выполнить следующую команду PowerShell для развертывания шаблона менеджера ресурсов. Замените `{MyRG}` имя группы ресурсов перед запуском команды. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>Дальнейшие действия
См. следующие статьи:
- [Обзор сервисного автобуса](service-bus-messaging-overview.md)
- [Обзор Key Vault](../key-vault/key-vault-overview.md)


