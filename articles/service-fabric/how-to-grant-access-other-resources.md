---
title: Предоставить приложение доступ к другим ресурсам Azure
description: В этой статье объясняется, как предоставить приложению service Service Fabric с поддержкой управляемой идентификации доступ к другим ресурсам Azure, поддерживающим проверку подлинности Active Directory На основе Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 3b1feab1e67e993df771564a1a7c1aba4236b2c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614799"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Предоставление управляемого доступа к идентификационным данным приложения Service Fabric к ресурсам Azure (предварительный просмотр)

Прежде чем приложение сможет использовать управляемое удостоверение для доступа к другим ресурсам, необходимо предоставить разрешения этому идентатору на защищенном ресурсе Azure. Выдача разрешений обычно является действием управления на плоскости управления службой Azure, владеющей защищенным ресурсом, маршрутизированным через Azure Resource Manager, который будет обеспечивать соблюдение любой применимой проверки доступа на основе ролей.

Точная последовательность шагов будет зависеть от типа ресурса Azure, доступ к которому, а также языка/клиента, используемого для выдачи разрешений. Остальная часть статьи предполагает присвоенное приложениеудостоверение, назначенное пользователем, и включает в себя несколько типичных примеров для вашего удобства, но это никоим образом не является исчерпывающей ссылкой на эту тему; ознакомиться с документацией соответствующих служб Azure для получения актуальных инструкций по выдаче разрешений.  

## <a name="granting-access-to-azure-storage"></a>Предоставление доступа к хранилищам Azure
Для извлечения данных из капли хранения Azure можно использовать управляемый итог приложения Service Fabric (назначенный пользователем в данном случае). Предоставить удостоверение личности требуемых разрешений на портале Azure следующими шагами:

1. Перейдите к учетной записи хранилища
2. Щелкните ссылку Управление доступом (IAM) на панели слева.
3. (необязательно) Проверить существующий доступ: выберите управляемую изначную итог системы или пользователя в элементе управления «Найти»; выбрать соответствующую идентификацию из последующего списка результатов
4. Нажмите кнопку «Добавить ролевые задания» в верхней части страницы, чтобы добавить новое назначение роли для идентификации приложения.
В поле Роль из раскрывающегося списка выберите Модуль чтения данных BLOB-объектов хранилища.
5. В следующем отсечке, под `User assigned managed identity`назначайте доступ к, выберите .
6. Убедитесь, что нужная подписка присутствует в раскрывающемся списке Подписка, и установите для параметра Группа ресурсов значение Все группы ресурсов.
7. При выборе выберите UAI, соответствующий приложению Service Fabric, а затем нажмите Кнопка Сохранить.

Поддержка системных идентификаторов управляемых системных системных тканей не включает интеграцию в портал Azure; если ваше приложение использует установленную системой личность, вам придется сначала найти идентификатор клиента удостоверения `Azure AD user, group, or service principal` приложения, а затем повторить вышеприведенные шаги, но выбрав опцию в управлении Поиска.

## <a name="granting-access-to-azure-key-vault"></a>Предоставление доступа к Убежище ключей Azure
Аналогичным образом при доступе к хранилищам можно использовать управляемую идентификацию приложения Service Fabric для доступа к хранилищу ключей Azure. Шаги для предоставления доступа на портале Azure аналогичны перечисленным выше и не будут повторяться здесь. Обратитесь к изображению ниже для различий.

![Политика доступа к Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

Следующий пример иллюстрирует предоставление доступа к хранилищу через развертывание шаблонов; добавить фрагмент (ы) ниже, как `resources` еще одна запись под элементшаблона. Выборка демонстрирует предоставление доступа как для назначенных пользователем, так и для системных типов идентификации, соответственно - выберите применимый.

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
А для системных управляемых идентификаторов:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Для получения более подробной информации, пожалуйста, смотрите [Убежища - Обновление политики доступа](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Дальнейшие действия
* [Развертывание приложения Azure Service Fabric с системой, назначенной управляемой личностью](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Развертывание приложения Azure Service Fabric с помощью управляемого удостоверения пользователя](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)