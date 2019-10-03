---
title: Использование управляемых удостоверений Azure для создания сред в DevTest Labs | Документация Майкрософт
description: Узнайте, как использовать управляемые удостоверения в Azure для развертывания сред в лаборатории в Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: d1dd059f1a6f9ce96b27d4fe1f214978dfc06a8f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815997"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Использование управляемых удостоверений Azure для развертывания сред в лаборатории 
Как владелец лаборатории вы можете использовать управляемое удостоверение для развертывания сред в лаборатории. Эта функция полезна в сценариях, где среда содержит или содержит ссылки на ресурсы Azure, такие как хранилища ключей, общие галереи образов и сети, являющиеся внешними по отношению к группе ресурсов среды. Она позволяет создавать изолированные среды, которые не ограничиваются группой ресурсов этой среды.

> [!NOTE]
> В настоящее время для лаборатории поддерживается только одно назначенное пользователем удостоверение. 

## <a name="prerequisites"></a>Предварительные требования
- [Создание, перечисление, удаление или назначение роли назначенному пользователем управляемому удостоверению с помощью портал Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе вы, как владелец лаборатории, используете портал Azure, чтобы добавить управляемое пользователем удостоверение в лабораторию. 

1. На странице Лаборатория выберите **Конфигурация и политики**. 
1. В разделе **Параметры** выберите **удостоверение** .
1. Чтобы добавить удостоверение, назначенное пользователем, нажмите кнопку **Добавить** на панели инструментов. 
1. Выберите **удостоверение** из предварительно заполненного раскрывающегося списка.
1. Нажмите кнопку **ОК**.

    ![Добавление удостоверения, управляемого пользователем](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. В списке появится добавленное управляемое пользователем удостоверение. 

    ![Удостоверение, управляемое пользователем в списке](./media/use-managed-identities-environments/identity-in-list.png)

После сохранения лаборатория будет использовать это удостоверение при развертывании всех лабораторных сред. Вы также можете получить доступ к ресурсу удостоверений в Azure, выбрав удостоверение из списка. 

Владельцу лаборатории не нужно делать ничего особенного при развертывании среды при условии, что удостоверение, добавленное в лабораторию, имеет разрешения на доступ к внешним ресурсам, необходимым среде. 

Чтобы изменить управляемое пользователем удостоверение, которое было назначено лаборатории, сначала удалите удостоверение, присоединенное к лаборатории, а затем добавьте еще одну учетную запись в лабораторию. Чтобы удалить удостоверение, присоединенное к лаборатории, выберите **... (многоточие)** и нажмите кнопку **Удалить**. 

![Удостоверение, управляемое пользователем в списке](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Использовать API

1. После создания удостоверения запишите идентификатор ресурса этого удостоверения. Он должен выглядеть, как в следующем примере: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Выполните метод размещения HTTPS, чтобы добавить новый ресурс `ServiceRunner` в лабораторию, как показано в следующем примере. Ресурс "средство запуска службы" — это прокси-ресурс для управления управляемыми удостоверениями и управления ими в DevTest Labs. Имя средства запуска службы может быть любым допустимым именем, но рекомендуется использовать имя управляемого ресурса удостоверений. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
    }
    ```
 
    Ниже приведен пример: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
    }
    ```
 
После добавления удостоверения пользователя в лабораторию служба Azure DevTest Labs будет использовать ее при развертывании среды Azure Resource Manager. Например, если вам нужен шаблон диспетчер ресурсов для доступа к внешнему образу коллекции общих образов, убедитесь, что удостоверение, добавленное в лабораторию, имеет минимальные необходимые разрешения для ресурса коллекции общих образов. 
