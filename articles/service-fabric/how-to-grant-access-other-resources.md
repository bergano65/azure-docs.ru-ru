---
title: Service Fabric Azure. предоставление приложению Service Fabric доступа к другим ресурсам Azure | Документация Майкрософт
description: В этой статье объясняется, как предоставить управляемому удостоверению приложения Service Fabric доступ к другим ресурсам Azure, поддерживающим проверку подлинности на основе Azure Active Directory.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: b6e1108ffee13f1583d920947404963a69616788
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68958425"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Предоставление управляемому удостоверению приложения Service Fabric доступ к ресурсам Azure

Прежде чем приложение сможет использовать управляемое удостоверение для доступа к другим ресурсам, необходимо предоставить этому удостоверению доступ к защищенному ресурсу Azure. Предоставление разрешений обычно является действием по управлению на плоскости управления службы Azure, владеющей защищенным ресурсом, находящимся через Azure Resource Manager, который обеспечивает все применимые проверки доступа на основе ролей.

Точная последовательность шагов будет зависеть от типа ресурса Azure, к которому осуществляется доступ, а также от языка и клиента, используемого для предоставления разрешений. Оставшаяся часть статьи предполагает, что назначенное приложению удостоверение назначено пользователю и включает несколько типичных примеров для вашего удобства, но не является исчерпывающей ссылкой на этот раздел; Ознакомьтесь с документацией соответствующих служб Azure, чтобы получить последние инструкции по предоставлению разрешений.  

## <a name="granting-access-to-azure-storage"></a>Предоставление доступа к службе хранилища Azure
Для получения данных из большого двоичного объекта службы хранилища Azure можно использовать управляемое удостоверение приложения Service Fabric (назначаемое пользователем в этом случае). Предоставьте удостоверению необходимые разрешения в портал Azure, выполнив следующие действия.

1. Перейдите к учетной записи хранения.
2. На левой панели щелкните ссылку Управление доступом (IAM).
3. используемых Проверить существующий доступ: выберите системное или назначенное пользователем управляемое удостоверение в элементе управления "найти"; Выбор соответствующего удостоверения из списка «исходящие результаты»
4. Щелкните + Добавить назначение ролей в верхней части страницы, чтобы добавить новое назначение роли для удостоверения приложения.
В разделе роль в раскрывающемся списке выберите модуль чтения данных BLOB-объекта хранилища.
5. В следующем раскрывающемся списке в разделе Назначение доступа к выберите `User assigned managed identity`.
6. Затем убедитесь, что в раскрывающемся списке подписка указана правильная подписка, а затем установите для группы ресурсов значение все группы ресурсов.
7. В разделе Выбрать выберите УАИ, соответствующий приложению Service Fabric, а затем нажмите кнопку Сохранить.

Поддержка назначенных системе Service Fabric управляемых удостоверений не включает интеграцию в портал Azure; Если приложение использует назначенное системой удостоверение, необходимо сначала найти идентификатор клиента для удостоверения приложения, а затем повторить описанные выше действия, но выбрать `Azure AD user, group, or service principal` параметр в элементе управления Find.

## <a name="granting-access-to-azure-key-vault"></a>Предоставление доступа к Azure Key Vault
Аналогично, при доступе к хранилищу можно использовать управляемое удостоверение приложения Service Fabric для доступа к хранилищу ключей Azure. Действия по предоставлению доступа в портал Azure похожи на те, которые перечислены выше, и не будут повторяться здесь. Различия см. на рисунке ниже.

![Политика доступа к Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

В следующем примере показано предоставление доступа к хранилищу с помощью развертывания шаблона. Добавьте фрагмент кода ниже в качестве другой записи в `resources` элементе шаблона.

```json
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

Дополнительные сведения см. в разделе [хранилища — Политика доступа для обновления](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Следующие шаги

* [Развертывание приложения Service Fabric Azure с управляемым удостоверением, назначенным системой](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Развертывание приложения Service Fabric Azure с помощью управляемого удостоверения, назначенного пользователем](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)

## <a name="related-articles"></a>Связанные статьи

* Проверка [поддержки управляемого удостоверения](./concepts-managed-identity.md) в Azure Service Fabric

* [Развертывание нового](./configure-new-azure-service-fabric-enable-managed-identity.md) Кластер Azure Service Fabric с поддержкой управляемых удостоверений 

* [Включение управляемого удостоверения](./configure-existing-cluster-enable-managed-identity-token-service.md) в существующем кластере Azure Service Fabric

* Использование управляемого удостоверения приложения Service Fabric [из исходного кода](./how-to-managed-identity-service-fabric-app-code.md)

* Просмотрите список [служб Azure, поддерживающих проверку подлинности Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md) .
