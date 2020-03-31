---
title: Используйте управляемые идентификаторы Azure для создания сред в DevTest Labs Документы Майкрософт
description: Узнайте, как использовать управляемые идентификаторы в Azure для развертывания сред в лаборатории в Лабораториях Azure DevTest Labs.
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
ms.openlocfilehash: a4ba4206c01e492f2ae980c5806de1e72c7051c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931154"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Используйте управляемые идентификаторы Azure для развертывания сред в лаборатории 
Как владелец лаборатории, вы можете использовать управляемый итог для развертывания сред в лаборатории. Эта функция полезна в сценариях, где среда содержит или имеет ссылки на ресурсы Azure, такие как хранилища ключей, общие галереи изображений и сети, которые являются внешними для группы ресурсов среды. Это позволяет создавать среды песочницы, которые не ограничиваются группой ресурсов этой среды.

> [!NOTE]
> В настоящее время в одной лаборатории поддерживается единая идентификация, назначенная пользователем. 

## <a name="prerequisites"></a>Предварительные требования
- [Создание, список, удаление или присвоение роли управляемому ипоименному удостоверению пользователя с помощью портала Azure.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 

## <a name="use-azure-portal"></a>Использование портала Azure
В этом разделе вы, как владелец лаборатории, используете портал Azure для добавления в лабораторию удостоверения, управляемого пользователем. 

1. На странице лаборатории выберите **Конфигурацию и политики.** 
1. Выберите **идентификацию** в разделе **Настройки.**
1. Чтобы добавить назначенную пользователю идентификацию, выберите **Добавить** на панели инструментов. 
1. Выберите **идентификацию** из предварительно заселенного списка выпадающих.
1. Нажмите кнопку **ОК**.

    ![Добавление удостоверения, управляемого пользователем](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. В списке можно увидеть добавленную идентификацию, управляемую пользователем. 

    ![Идентификационная личность, управляемая пользователем в списке](./media/use-managed-identities-environments/identity-in-list.png)

После сохранения лаборатория будет использовать эту идентификацию при развертывании всех лабораторных сред. Вы также можете получить доступ к ресурсу идентификации в Azure, выбрав идентификационную личность из списка. 

Владельцу лаборатории не нужно делать ничего особенного при развертывании среды до тех пор, пока личность, добавленная в лабораторию, имеет разрешения на внешние ресурсы, к которым необходимо получить доступ среде. 

Чтобы изменить удостоверение, установленное пользователем, назначенное в лабораторию, сначала удалите личность, прикрепленную к лаборатории, а затем добавьте еще одну в лабораторию. Чтобы удалить личность прилагается к лаборатории, выберите **... (эллипсис)**, и нажмите **Удалить**. 

![Идентификационная личность, управляемая пользователем в списке](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>Использование API

1. После создания идентификатора обратите внимание на идентификатор ресурса этой идентификации. Он должен выглядеть следующим примером: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Выполните метод PUT Https, `ServiceRunner` чтобы добавить новый ресурс в лабораторию, аналогичный следующему примеру. Ресурс бегуна служб — это прокси-ресурс для управления и управления управляемыми идентификаторами в DevTest Labs. Имя бегуна службы может быть любым действительным именем, но мы рекомендуем использовать имя управляемого ресурса идентификации. 
 
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
        "properties":{
            "identityUsageType":"Environment"
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
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
После добавления в лабораторию удостоверения пользователя служба Labs Labs будет использовать ее при развертывании сред управления ресурсами Azure. Например, если вам нужен шаблон Resource Manager для доступа к внешнему общему изображению галереи изображений, убедитесь, что идентификатор, добавленный в лабораторию, имеет минимально необходимые разрешения для ресурса общей галереи изображений. 
