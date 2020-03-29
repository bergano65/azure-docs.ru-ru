---
title: Включить управляемые идентификаторы на ваших лабораторных VMs в Лабораториях Azure DevTest Labs
description: В этой статье показано, как владелец лаборатории может включить управляемые идентификаторы, назначенные пользователем, на виртуальных машинах лаборатории.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2020
ms.author: spelluru
ms.openlocfilehash: 5d70f83babcf53249f581230e72326d99a0533d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75692671"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>Включить назначенные пользователем управляемые идентификаторы на лабораторных виртуальных машинах в Лабораториях Azure DevTest Labs
Как владелец лаборатории, вы можете включить назначенные пользователем управляемые идентификаторы на лабораторных виртуальных машинах (VM) в Azure DevTest Labs.

Управляемый имитальдля может использоваться для проверки подлинности любой службы, поддерживающей проверку подлинности Active Directory (AD), включая Key Vault, без передачи учетных данных в коде. Более подробную информацию об управляемых идентификаторах можно узнать на примере [идентификаторов Для ресурсов Azure?.](../active-directory/managed-identities-azure-resources/overview.md)

С помощью этой функции пользователи лаборатории могут совместно с ресурсами Azure, такими как база данных Azure S'L, в контексте лаборатории. Аутентификация ресурса проверяется самой идентификацией. После настройки каждая существующая/вновь созданная лаборатория VM будет включена с этой идентификацией. Пользователи лаборатории могут получить доступ к ресурсам после входа в свои машины.

> [!NOTE]
> Можно добавить несколько назначенных пользователем управляемых идентификаторов, которые будут включены в ваши vMs-интерфейсы лаборатории.

## <a name="use-azure-portal"></a>Использование портала Azure
Чтобы добавить пользователю, назначенному управляемой идентификации для лабораторных VMs, выполните следующие действия:

1. [Создание управляемой итог, назначенной пользователем, в подписке](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Перейдите на страницу конфигурации и политик для вашей **лаборатории.**
2. Выберите **идентификацию (Предварительный просмотр)** в левом меню.
3. Выберите вкладку **Виртуальная машина.**
4. Выберите **Добавить,** чтобы выбрать существующую идентификацию из предварительно заселенного списка выпадающих данных. 

    ![Добавление кнопки идентификации](./media/enable-managed-identities-lab-vms/add-identity-button.png)
5. Выберите существующую **идентификацию, управляемую пользователем,** из списка отсевших и выберите **OK.** 

    ![Добавление удостоверения](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>Использование API

1.  После создания идентификатора обратите внимание на идентификатор ресурса идентификатора. Он должен выглядеть следующим примером: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Запустите метод PUT HTTPS, чтобы добавить в лабораторию новый ресурс **ServiceRunner,** как показано в следующем примере. 

    Ресурс бегуна служб — это прокси-ресурс для управления и управления управляемыми идентификаторами в DevTest Labs. Имя бегуна службы может быть любым действительным именем, но мы рекомендуем вам использовать имя управляемого ресурса идентификации.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше об управляемых идентификаторах, [см.](../active-directory/managed-identities-azure-resources/overview.md)







