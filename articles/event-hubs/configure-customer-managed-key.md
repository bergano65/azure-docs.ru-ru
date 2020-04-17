---
title: Налажить свой собственный ключ для шифрования данных Azure Event Hubs в состоянии покоя
description: В этой статье содержится информация о том, как настроить свой собственный ключ для шифрования данных Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: f515d3ad832db7f78f98111ab67628a2874033ff
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459140"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Настройка управляемых клиентом ключей для шифрования данных Azure Event Hubs в состоянии покоя с помощью портала Azure
Azure Event Hubs обеспечивает шифрование данных в состоянии покоя с помощью системы хранения данных Azure (Azure SSE). Концентраторы событий полагаются на хранилище данных Azure, и по умолчанию все данные, хранящиеся в Azure Storage, шифруются с помощью ключей, управляемых Корпорацией Майкрософт. 

## <a name="overview"></a>Обзор
Концентраторы событий Azure теперь поддерживают возможность шифрования данных в состоянии покоя либо с помощью ключей, управляемых корпорацией Майкрософт, либо с ключами, управляемыми клиентом (Bring Your Own Key - BYOK). Эта функция позволяет создавать, вращать, отключать и отменять доступ к управляемым клиентом ключам, которые используются для шифрования данных Azure Event Hubs в состоянии покоя.

Включение функции BYOK — это одноразовый процесс настройки в пространстве имен.

> [!NOTE]
> Возможность BYOK поддерживается специализированными [кластерами event Hubs с одним арендатором.](event-hubs-dedicated-overview.md) Он не может быть включен для стандартных областей имен концентраторов событий.

Вы можете использовать Azure Key Vault для управления ключами и аудита использования ключей. Вы можете создать свои собственные ключи и хранить их в хранилище ключей, либо использовать AA-иносы Azure Key Vault для генерации ключей. Для получения дополнительной информации о [What is Azure Key Vault?](../key-vault/general/overview.md) Убежище ключей Azure см.

В этой статье показано, как настроить хранилище ключей с помощью ключей, управляемых клиентом, с помощью портала Azure. Чтобы узнать, как создать хранилище ключей с помощью портала Azure, [см.](../key-vault/secrets/quick-create-portal.md)

> [!IMPORTANT]
> Использование управляемых клиентами ключей с помощью концентраторов azure Event требует, чтобы хранилище ключей было настроено на два необходимых свойства. Они: **Мягкий Удалить** и **не очистить**. Эти свойства включены по умолчанию при создании нового хранилища ключей на портале Azure. Однако, если необходимо включить эти свойства в существующем хранилище ключей, необходимо использовать либо PowerShell, либо Azure CLI.

## <a name="enable-customer-managed-keys"></a>Включить ключи, управляемые клиентом
Для включения ключей, управляемых клиентами, на портале Azure выполните следующие действия:

1. Перейдите к кластеру, посвященному концентрам событий.
1. Выберите пространство имен, на котором вы хотите включить BYOK.
1. На странице **«Настройки»** в пространстве имен событий **—сявра**— выберите шифрование — 
1. Выберите **шифрование ключей, управляемое Заказчиком, в состоянии покоя,** как показано на следующем изображении. 

    ![Включить управляемый ключом клиента](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Настройка хранилища ключей с ключами
После включения ключей, управляемых клиентом, необходимо связать управляемый ключ омичи клиента с пространством имен Azure Event Hubs. Концентраторы событий поддерживают только Убежище ключей Azure. Если в предыдущем разделе **шифрование с помощью ключевого** опции с помощью клиента, необходимо импортировать ключ в Хранилище ключей Azure. Кроме того, ключи должны иметь **soft Delete** и Do **Not Purge** настроенный для ключа. Эти настройки можно настроить с помощью [PowerShell](../key-vault/general/soft-delete-powershell.md) или [CLI.](../key-vault/general/soft-delete-cli.md#enabling-purge-protection)

1. Чтобы создать новое хранилище ключей, следуйте за [quickstart](../key-vault/general/overview.md)Key Vault Azure. Для получения дополнительной информации об импорте существующих ключей, [см.](../key-vault/about-keys-secrets-and-certificates.md)
1. Чтобы включить как мягкое удаление и очистку защиты при создании хранилища, используйте [команду создания клавиатуры az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create)

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Чтобы добавить защиту от очистки в существующее хранилище (у которого уже есть мягкое удаление), используйте команду [обновления az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update)

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Создавайте ключи следующим образом:
    1. Чтобы создать новый ключ, выберите **Создать или импортировать** из меню **Ключи** в разделе **Параметры**.
        
        ![Выберите кнопку «Создание/импорт»](./media/configure-customer-managed-key/select-generate-import.png)
    1. Задайте **Параметры**, чтобы **Создать** и присвоить имя для ключа.

        ![Создание ключа](./media/configure-customer-managed-key/create-key.png) 
    1. Теперь вы можете выбрать этот ключ, чтобы связать с пространством имен Event Hubs для шифрования из списка выпадающих. 

        ![Выберите ключ из хранилища ключей](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Заполните детали для ключа и нажмите **Выберите**. Это позволит шифрование данных в состоянии покоя на пространстве имен с клиентом управляемых ключей. 


## <a name="rotate-your-encryption-keys"></a>Поверните ключи шифрования
Вы можете повернуть ключ в хранилище ключей с помощью механизма вращения ключей Azure Vaults. Для получения дополнительной [информации см.](../key-vault/secrets/key-rotation-log-monitoring.md) Даты активации и истечения также могут быть установлены для автоматизации вращения ключей. Служба концентраторов событий обнаружит новые ключевые версии и начнет использовать их автоматически.

## <a name="revoke-access-to-keys"></a>Отмена доступа к ключам
Отмена доступа к ключам шифрования не будет удавлять данные из концентраторов событий. Однако доступ к данным из пространства имен концентраторов событий неможет быть получен. Вы можете отозвать ключ шифрования через политику доступа или удаляя ключ. Узнайте больше о политиках доступа и защите хранилища ключей от [безопасного доступа к хранилищу ключей.](../key-vault/general/secure-your-key-vault.md)

После отзыва ключа шифрования служба Event Hubs в зашифрованном пространстве имен станет неработоспособной. Если доступ к ключу включен или ключ удаления восстановлен, служба событий концентраторов подберет ключ, чтобы вы могли получить доступ к данным из зашифрованного пространства имен концентраторов событий.

## <a name="set-up-diagnostic-logs"></a>Настройка журналов диагностики 
Настройка диагностических журналов для именных пространств с включенным BYOK дает необходимую информацию об операциях, когда пространство имен зашифровано с помощью ключей, управляемых клиентом. Эти журналы могут быть включены и позже передаваться в концентратор событий или анализироваться с помощью аналитики журналов или передаваться в хранилище для выполнения индивидуальной аналитики. Чтобы узнать больше о диагностических журналах, смотрите [Обзор журналов Azure Diagnostic.](../azure-monitor/platform/platform-logs-overview.md)

## <a name="enable-user-logs"></a>Включить журналы пользователей
Выполните следующие действия, чтобы включить журналы для ключей, управляемых клиентом.

1. На портале Azure перейдите к пространству имен с включенным BYOK.
1. Выберите **диагностические настройки** под **мониторингом**.

    ![Выберите параметры диагностики](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Выберите **диагностическую настройку «Добавить».** 

    ![Выберите диагностическую настройку добавления](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Предоставьте **имя** и выберите, куда вы хотите передавать журналы.
1. Выберите **CustomerManagedKeyUserLogs** и **сохранить**. Это действие позволяет журналы для BYOK в пространстве имен.

    ![Выберите опцию ключевых журналов пользователей, управляемых клиентом](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Схема журнала 
Все журналы хранятся в формате JSON (нотация объектов JavaScript). Каждая запись имеет строчные поля, в которых используется формат, описанный в следующей таблице. 

| Имя | Описание |
| ---- | ----------- | 
| TaskName | Описание задачи, завершившейся сбоем. |
| ActivityId | Внутренний идентификатор, который используется для отслеживания. |
| категория | Определяет классификацию задачи. Например, если ключ из хранилища ключей отключен, то это будет информационная категория или если ключ не может быть развернут, он может попасть под ошибку. |
| resourceId | Идентификатор ресурса Azure Resource Manager |
| keyVault | Полное имя хранилища ключей. |
| ключ | Ключевое имя, используемое для шифрования пространства имен Event Hubs. |
| version | Версия используемого ключа. |
| операции | Операция, выполняемая на ключе в хранилище ключа. Например, отключить/включить ключ, обернуть или развернуть |
| код | Код, связанный с операцией. Пример: Код ошибки, 404 означает, что ключ не был найден. |
| message | Любое сообщение об ошибке, связанное с операцией |

Вот пример журнала для управляемого ключом клиента:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Используйте шаблон менеджера ресурсов для включения шифрования
В этом разделе показано, как выполнять следующие задачи с помощью **шаблонов Azure Resource Manager.** 

1. Создайте **пространство имен event Hubs** с именем управляемого сервиса.
2. Создайте **хранилище ключей** и предоставите идентификационным данным службы доступ к хранилищу ключей. 
3. Обновление пространства имен событий концентраторов с информацией о хранилище ключей (ключ/значение). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Создание кластера концентратов событий и пространства имен с помощью управляемой идентификации службы
В этом разделе показано, как создать пространство имен Azure Event Hubs с управляемой идентификатором службы с помощью шаблона Azure Resource Manager и PowerShell. 

1. Создайте шаблон менеджера ресурсов Azure для создания пространства имен событий Концентраторов с именем управляемой службы. Название файла: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Создайте файл параметров шаблона под названием: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Измените следующие значения: 
    > - `<EventHubsClusterName>`- Название кластера концентраторов событий    
    > - `<EventHubsNamespaceName>`- Название пространства имен event Hubs
    > - `<Location>`- Расположение пространства имен концентраторов событий

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Выполнить следующую команду PowerShell для развертывания шаблона для создания пространства имен событий концентраторов. Затем извлеките идентификатор пространства имен событий, чтобы использовать его позже. Замените `{MyRG}` имя группы ресурсов перед запуском команды.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Доступ к идентификационным пространствам событий Grant Event Hubs к хранилищу ключей

1. Запустите следующую команду, чтобы создать хранилище ключей с **защитой от очистки** и **с включенным мягким удалением.** 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (ИЛИ)    
    
    Выполнить следующую команду для обновления **существующего хранилища ключей.** Указать значения для группы ресурсов и имен хранилища ключей перед запуском команды. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Установите политику доступа к хранилищу ключей таким образом, чтобы управляемое удостоверение пространства имен событий было доступно для ключевого значения в хранилище ключей. Используйте идентификатор пространства имен событий концентраторов из предыдущего раздела. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Шифрование данных в пространстве имен Event Hubs с помощью ключа, управляемого клиентом, из хранилища ключей
Вы сделали следующие шаги до сих пор: 

1. Создано премиальное пространство имен с управляемой идентификацией.
2. Создайте хранилище ключей и предоставите управляемый доступ к хранилищу ключей. 

На этом этапе вы обновите пространство имен концентров событий с информацией о хранилище ключей. 

1. Создайте файл JSON под названием **CreateEventHubClusterAndNamespace.json** со следующим содержанием: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. Создайте файл параметров шаблона: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Измените следующие значения: 
    > - `<EventHubsClusterName>`- Название кластера концентраторов событий.        
    > - `<EventHubsNamespaceName>`- Название пространства имен event Hubs
    > - `<Location>`- Расположение пространства имен концентраторов событий
    > - `<KeyVaultName>`- Имя вашего хранилища ключей
    > - `<KeyName>`- Имя ключа в хранилище ключей

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>Диагностика
В качестве наилучшей практики всегда включайте журналы, как показано в предыдущем разделе. Это помогает отслеживать действия, когда шифрование BYOK включено. Он также помогает в отслеживании вниз проблемы.

Ниже приведены общие коды ошибок, которые следует искать при включении шифрования BYOK.

| Действие | Код ошибки | В результате состояние данных |
| ------ | ---------- | ----------------------- | 
| Удалить разрешение на обертку/распаковку из хранилища ключей | 403 |    Нет доступа |
| Удалить членство в роли AAD от принципала AAD, который выдал разрешение на обертку/распаковку | 403 |  Нет доступа |
| Удалить ключ шифрования из хранилища ключей | 404 | Нет доступа |
| Удалить хранилище ключей | 404 | Недоступный (предполагает, что мягкое удаление включено, что является необходимой настройкой.) |
| Изменение срока действия ключа шифрования должно быть таким, чтобы срок его действия истек | 403 |   Нет доступа  |
| Изменение NBF (не раньше) так, что ключ шифрования не активен | 403 | Нет доступа  |
| Выбор опции **Allow MSFT Services** для брандмауэра хранилища ключей или иным образом блокирование доступа к сети к хранилищу ключей с ключом с ключом шифрования | 403 | Нет доступа |
| Перемещение хранилища ключей другому арендатору | 404 | Нет доступа |  
| Прерывистая проблема сети или сбой DNS/AAD/MSI |  | Доступно с помощью ключа шифрования кэшированных данных |

> [!IMPORTANT]
> Для включения Geo-DR в пространстве имен, использующем шифрование BYOK, вторичное пространство имен для сопряжения должно находиться в выделенном кластере и должно иметь систему, назначенную управляемой идентификацией, включенной на него. Чтобы узнать больше, смотрите [управляемые идентификаторы для ресурсов Azure.](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="next-steps"></a>Следующие шаги
См. следующие статьи:
- [Общие сведения о Центрах событий](event-hubs-about.md)
- [Обзор Key Vault](../key-vault/general/overview.md)




