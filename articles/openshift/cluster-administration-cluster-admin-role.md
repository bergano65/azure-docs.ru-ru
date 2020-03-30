---
title: Роль администратора кластера Azure Red Hat OpenShift (ru) Документы Майкрософт
description: Назначение и использование роли администратора кластера Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: ae9a421a165d6c8bda688819c5233ae5bb1a8562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139102"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Роль администратора администратора администратора Azure Red Hat OpenShift

Вы являетесь администратором кластера кластера Azure Red Hat OpenShift. Ваша учетная запись увеличила число разрешений и доступа ко всем созданным пользователями проектам.

Когда ваша учетная запись имеет роль авторизации клиента-админ-кластера, она может автоматически управлять проектом.

> [!Note] 
> Роль кластера клиента-админ-кластера не совпадает с ролью кластера кластера кластера.

Например, можно выполнить действия, связанные`create`с набором глаголов ()`templates`для работы на наборе имен ресурсов (). Чтобы просмотреть детали этих ролей и наборы глаголов и ресурсов, запустите следующую команду:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Имена глаголов не обязательно все карты `oc` непосредственно к командам. Они в целом приравниваются к типам операций CLI, которые можно выполнять. 

Например, наличие `list` глагола означает, что вы можете отобразить список всех объектов имени ресурса ().`oc get` Глагол `get` означает, что вы можете отображать детали конкретного объекта, если вы знаете его имя ().`oc describe`

## <a name="configure-the-customer-administrator-role"></a>Настройка роли администратора клиента

Настраивать роль кластера клиента-админ-кластера можно только при создании `--customer-admin-group-id`кластера, предоставляя флаг. Это поле в настоящее время не настраивается на портале Azure. Чтобы узнать, как настроить активный каталог Azure и группу администраторов, перечное [внедрение Active Directory Azure для Azure Red Hat OpenShift.](howto-aad-app-configuration.md)

## <a name="confirm-membership-in-the-customer-administrator-role"></a>Подтверждение членства в роли администратора клиента

Чтобы подтвердить свое членство в группе админ-клиентов, попробуйте команды OpenShift CLI `oc get nodes` или `oc projects`. `oc get nodes`будет отображаться список узлов, если у вас есть роль клиента-админ-кластера, и ошибка разрешения, если у вас есть только роль клиента-админ-проекта. `oc projects`покажет все проекты в кластере, а не только проекты, в которые вы работаете.

Для дальнейшего изучения ролей и разрешений [`oc policy who-can <verb> <resource>`](https://docs.openshift.com/container-platform/3.11/admin_guide/manage_rbac.html#managing-role-bindings) в кластере можно использовать команду.

## <a name="next-steps"></a>Дальнейшие действия

Настройка роли кластера клиента-админ-кластера:
> [!div class="nextstepaction"]
> [Интеграция Active Directory Azure для Azure Red Hat OpenShift](howto-aad-app-configuration.md)
