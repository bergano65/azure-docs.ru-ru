---
title: Роль администратора кластера Azure Red Hat OpenShift | Документация Майкрософт
description: Назначение и использование роли администратора кластера Azure Red Hat OpenShift
services: container-service
author: mjudeikis
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 6d7aacea5e34e21513452880448227a1ca5f9b67
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709950"
---
# <a name="azure-red-hat-openshift-customer-administrator-role"></a>Роль администратора клиента Azure Red Hat OpenShift

Как администратор кластера АТО (Azure Red Hat OpenShift) в кластере OpenShift, ваша учетная запись обладает повышенными разрешениями и доступом ко всем создаваемым пользователями проектам.

Если с учетной записью связана роль авторизации OSA-Customer-администраторы, она может автоматически управлять проектом.

> [!Note] 
> OSA-Customer-Admin клустерроле не совпадает с параметром Cluster-Admin клустерроле


Например, можно выполнить действия, связанные с набором глаголов (`create`), для работы с набором имен ресурсов (`templates`). Чтобы просмотреть подробные сведения об этих ролях и их наборах команд и ресурсов, выполните следующую команду:

`$ oc describe clusterrole/osa-customer-admin`

Имена команд не обязательно сопоставляются непосредственно с командами OC, а в общем случае — для типов операций CLI, которые можно выполнять. Например, наличие глагола `list` означает, что можно отобразить список всех объектов заданного имени ресурса (`oc get`), а глагол `get` означает, что можно отобразить сведения об определенном объекте, если известно его имя (`oc describe`).

## <a name="how-to-configure-customer-administrator-role"></a>Настройка роли администратора клиента

Роль "Администратор клиента" может быть настроена только во время создания кластера, предоставляя флаг `--customer-admin-group-id`. Инструкции по настройке группы Azure Active Directory и администраторов см. в разделе руководство. [Интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md)

## <a name="next-steps"></a>Следующие шаги

Настройка роли администратора OSA-Customer:
> [!div class="nextstepaction"]
> [Интеграция Azure Active Directory для Azure Red Hat OpenShift](howto-aad-app-configuration.md)
