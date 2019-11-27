---
title: Роль администратора кластера Azure Red Hat OpenShift | Документация Майкрософт
description: Назначение и использование роли администратора кластера Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 0cb875122c63be18f7c39cdfea7986d705ed434e
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74539275"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Роль администратора клиента Azure Red Hat OpenShift

Вы являетесь администратором кластера Azure Red Hat OpenShift. Ваша учетная запись обладает повышенными разрешениями и доступом ко всем создаваемым пользователями проектам.

Если с учетной записью связана роль авторизации "клиент-Администратор-кластер", она может автоматически управлять проектом.

> [!Note] 
> Роль кластера "клиент-Администратор-кластер" отличается от роли кластера "Администратор кластера".


Например, можно выполнять действия, связанные с набором глаголов (`create`), для работы с набором имен ресурсов (`templates`). Чтобы просмотреть подробные сведения об этих ролях и их наборах команд и ресурсов, выполните следующую команду:

`$ oc get clusterroles customer-admin-cluster -o yaml`

Имена команд не обязательно сопоставляются непосредственно с командами `oc`. Они более широко соответствуют типам операций CLI, которые можно выполнять. 

Например, наличие глагола `list` означает, что можно отобразить список всех объектов имени ресурса (`oc get`). Команда `get` означает, что можно отобразить сведения об определенном объекте, если известно его имя (`oc describe`).

## <a name="configure-the-customer-administrator-role"></a>Настройка роли администратора клиента

Вы можете настроить роль кластера "клиент-Администратор-кластер" только во время создания кластера, указав флаг `--customer-admin-group-id`. Сведения о настройке Azure Active Directory и группы администраторов см. в статье [интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md).

## <a name="next-steps"></a>Следующие шаги

Настройте роль "клиент-Администратор-кластер кластера":
> [!div class="nextstepaction"]
> [Интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md)
