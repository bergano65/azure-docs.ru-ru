---
title: Ссылки на Key Vault в Службе приложений Azure | Документация Майкрософт
description: Справочник по основным понятиям и руководство по настройке ссылок на Key Vault в Службе приложений Azure и Функциях Azure.
services: app-service
author: mattchenderson
manager: jeconnoc
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/20/2018
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: f086850ecc2f15c41ab89db34b16d6d2e4a229cb
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956223"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions-preview"></a>Использование ссылок на Key Vault в Службе приложений и Функциях Azure (предварительная версия)

> [!NOTE] 
> Ссылки на Key Vault сейчас доступны в предварительной версии.

В этой статье показано, как работать с секретами из Azure Key Vault в приложении Службы приложений или Функций Azure без каких-либо изменений кода. Служба [Azure Key Vault](../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами и полный контроль над политиками доступа и журналами аудита.

## <a name="granting-your-app-access-to-key-vault"></a>Предоставление приложению доступа к Key Vault

Чтобы просматривать секреты из Key Vault, необходимо создать хранилище и предоставить приложению разрешения на доступ к нему.

1. Чтобы создать хранилище ключей, изучите [краткое руководство по Key Vault](../key-vault/quick-create-cli.md).

1. Создайте для приложения [управляемое удостоверение, назначаемое системой](overview-managed-identity.md).

   > [!NOTE] 
   > Ссылки на Key Vault сейчас поддерживают только назначаемые системой управляемые удостоверения. Вы не сможете использовать удостоверения, назначаемые пользователем.

1. Создайте [политику доступа в Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) для созданного ранее удостоверения приложения. Включите в этой политике разрешения "Get" на получение секретов. Не устанавливайте "авторизованное приложение" или параметр `appliationId`, так как он не совместим с управляемым удостоверением.

## <a name="reference-syntax"></a>Синтаксис ссылок

Ссылка на Key Vault имеет вид `@Microsoft.KeyVault({referenceString})`, где `{referenceString}` заменяется одним из следующих значений.

> [!div class="mx-tdBreakAll"]
> | Строка ссылки                                                            | Описание                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | Здесь **SecretUri** является полным URI плоскости данных секрета в Key Vault, включая версию, например: https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931.  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | Здесь **VaultName** — имя ресурса Key Vault. **SecretName** — имя целевого секрета. **SecretVersion** — используемая версия секрета. |

> [!NOTE] 
> В этой предварительной версии значение версии является обязательным. При смене секретов вам придется обновлять версию в конфигурации приложения.

Вот пример допустимой полной ссылки:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```

Еще один вариант:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Получение параметров приложения из Key Vault

Ссылки на Key Vault можно использовать в качестве значений для [параметров приложения](configure-common.md#configure-app-settings), что позволяет хранить секреты в Key Vault, а не в конфигурации сайта. Параметры приложения надежно шифруются при хранении, но возможности для управления секретами для них будут обеспечены только в Key Vault.

Чтобы использовать ссылку на Key Vault в качестве параметра приложения, укажите эту ссылку в значении этого параметра. Приложения смогут использовать секрет через ключ параметра, как обычно. Изменения кода не требуются.

> [!TIP]
> Большинство параметров приложения, выраженных в виде ссылок на Key Vault, следует пометить как параметры слота, так как для каждой среды должны быть настроены разные хранилища.

### <a name="azure-resource-manager-deployment"></a>Развертывание Azure Resource Manager

При автоматизации развертывания ресурсов с помощью шаблонов Azure Resource Manager вам может потребоваться определенный порядок размещения зависимостей, без которого эта функция не будет работать. Важно также отметить, что параметры приложения должны быть определены как собственный ресурс, без свойства `siteConfig` в определении сайта. Это связано с тем, что сначала нужно определить сайт и получить для него назначаемый системой идентификатор, и лишь затем можно применить этот идентификатор в политике доступа.

В качестве примера давайте рассмотрим следующий псевдошаблон приложения-функции:

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').tenantId]",
                        "objectId": "[reference(concat('Microsoft.Web/sites/',  variables('functionAppName'), '/providers/Microsoft.ManagedIdentity/Identities/default'), '2015-08-31-PREVIEW').principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2015-05-01-preview').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> В этом примере развертывание системы управления версиями зависит от параметров приложения. Такое поведение обычно считается небезопасным, так как обновление параметров приложения выполняется асинхронно. Но в этом примере оно будет синхронным, так как мы добавили параметр приложения `WEBSITE_ENABLE_SYNC_UPDATE_SITE`. Это означает, что развертывания системы управления версиями начнется только после того, как завершится обновление параметров приложения.
