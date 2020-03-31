---
title: Используйте ссылки Key Vault
description: Узнайте, как настроить службу приложений Azure и функции Azure, чтобы использовать ссылки Azure Key Vault. Сделайте секреты Key Vault доступными для кода приложения.
author: mattchenderson
ms.topic: article
ms.date: 10/09/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 7fdb7c980a278e2dcd4b64a4b70de50721d0b72a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280343"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>Используйте ссылки Key Vault для службы приложений и функций Azure

В этой статье показано, как работать с секретами из Azure Key Vault в приложении Службы приложений или Функций Azure без каких-либо изменений кода. Служба [Azure Key Vault](../key-vault/key-vault-overview.md) обеспечивает централизованное управление секретами и полный контроль над политиками доступа и журналами аудита.

## <a name="granting-your-app-access-to-key-vault"></a>Предоставление приложению доступа к Key Vault

Чтобы просматривать секреты из Key Vault, необходимо создать хранилище и предоставить приложению разрешения на доступ к нему.

1. Чтобы создать хранилище ключей, изучите [краткое руководство по Key Vault](../key-vault/quick-create-cli.md).

1. Создайте для приложения [управляемое удостоверение, назначаемое системой](overview-managed-identity.md).

   > [!NOTE] 
   > Ссылки на Key Vault сейчас поддерживают только назначаемые системой управляемые удостоверения. Вы не сможете использовать удостоверения, назначаемые пользователем.

1. Создайте [политику доступа в Key Vault](../key-vault/key-vault-secure-your-key-vault.md#key-vault-access-policies) для созданного ранее удостоверения приложения. Включите в этой политике разрешения "Get" на получение секретов. Не устанавливайте "авторизованное приложение" или параметр `applicationId`, так как он не совместим с управляемым удостоверением.

    > [!NOTE]
    > Ссылки Key Vault в настоящее время не в состоянии разрешать секреты, хранящиеся в хранилище ключей с [сетевыми ограничениями.](../key-vault/key-vault-overview-vnet-service-endpoints.md)

## <a name="reference-syntax"></a>Синтаксис ссылок

Ссылка на Key Vault имеет вид `@Microsoft.KeyVault({referenceString})`, где `{referenceString}` заменяется одним из следующих значений.

> [!div class="mx-tdBreakAll"]
> | Строка ссылки                                                            | Описание                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | Здесь **SecretUri** является полным URI плоскости данных секрета в Key Vault, включая версию, например: https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931.  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | Здесь **VaultName** — имя ресурса Key Vault. **SecretName** — имя целевого секрета. **SecretVersion** — используемая версия секрета. |

Например, полная ссылка с версией будет выглядеть следующим образом:

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931)
```
Еще один вариант:

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
```


## <a name="source-application-settings-from-key-vault"></a>Получение параметров приложения из Key Vault

Ссылки Key Vault могут быть использованы в качестве значений для [настроек приложений,](configure-common.md#configure-app-settings)что позволяет хранить секреты в Key Vault вместо конфигурации сайта. Настройки приложений надежно зашифрованы в состоянии покоя, но если вам нужны секретные возможности управления, они должны перейти в Key Vault.

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

## <a name="troubleshooting-key-vault-references"></a>Ссылки на устранение неполадок в ключи Убежища

Если ссылка не будет решена должным образом, вместо этого будет использоваться эталонное значение. Это означает, что для настроек приложения создается `@Microsoft.KeyVault(...)` переменная среды, значение которой имеет синтаксис. Это может привести к тому, что приложение будет бросать ошибки, так как ожидало секрет определенной структуры.

Чаще всего это происходит из-за неправильной настройки [политики доступа Key Vault.](#granting-your-app-access-to-key-vault) Тем не менее, это также может быть связано с тайной больше не существует или синтаксис ошибки в самой ссылке.

Если синтаксис правильный, можно просмотреть другие причины ошибки, проверив текущее состояние разрешения на портале. Перейдите к настройкам приложения и выберите "Edit" для ссылки. Ниже настройки конфигурации следует видеть информацию о состоянии, включая любые ошибки. Отсутствие этих означает, что референтный синтаксис является недействительным.

Вы также можете использовать один из встроенных детекторов для получения дополнительной информации.

### <a name="using-the-detector-for-app-service"></a>Использование детектора для службы приложений

1. На портале перейдите к приложению.
2. Выберите колонку **Диагностика и решение проблем**.
3. Выберите **Доступность и Производительность** и выберите **веб-приложение вниз.**
4. Найти **ключевые Настройки Настройки Убежища Диагностика** и нажмите **Подробнее .**


### <a name="using-the-detector-for-azure-functions"></a>Использование детектора для функций Azure

1. На портале перейдите к приложению.
2. Перейдите к **функциям Платформы.**
3. Выберите колонку **Диагностика и решение проблем**.
4. Выберите **Доступность и Производительность** и выберите **приложение функции вниз или отчетности ошибок.**
5. Нажмите на **диагностику параметров приложений Key Vault.**
