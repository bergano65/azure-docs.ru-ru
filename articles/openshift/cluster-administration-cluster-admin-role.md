---
title: Роль администратора кластера Azure Red Hat OpenShift | Документация Майкрософт
description: Назначение и использование роли администратора кластера Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 1c676b2671b73084a2b4ae8908acb83c23a59b7b
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936930"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Роль администратора клиента Azure Red Hat OpenShift

Вы являетесь администратором кластера Azure Red Hat OpenShift. Ваша учетная запись обладает повышенными разрешениями и доступом ко всем создаваемым пользователями проектам.

Если с учетной записью связана роль авторизации OSA-Customer-Admin, она может автоматически управлять проектом.

> [!Note] 
> Роль кластера "OSA — клиент-Администратор" отличается от роли кластера "Администратор кластера".


Например, можно выполнить действия, связанные с набором глаголов (`create`), для работы с набором имен ресурсов (`templates`). Чтобы просмотреть подробные сведения об этих ролях и их наборах команд и ресурсов, выполните следующую команду:

`$ oc describe clusterrole/osa-customer-admin`

Имена команд не обязательно сопоставляются непосредственно с командами `oc`. Они более широко соответствуют типам операций CLI, которые можно выполнять. 

Например, наличие глагола `list` означает, что можно отобразить список всех объектов имени ресурса (`oc get`). Команда `get` означает, что можно отобразить сведения об определенном объекте, если известно его имя (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Настройка роли администратора клиента

Роль "Администратор клиента" можно настроить только во время создания кластера, указав флаг `--customer-admin-group-id`. Сведения о настройке Azure Active Directory и группы администраторов см. в статье [интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Следующие шаги

Настройте роль OSA-клиент-Администратор:
> [!div class="nextstepaction"]
> [Интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md)
