---
title: Управление ресурсами в Azure Red Hat OpenShift (ru) Документы Майкрософт
description: Управление проектами, шаблонами, потоками изображений в кластере Azure Red Hat OpenShift
services: openshift
keywords: красная шляпа openshift проекты просит самообеспечения
author: mjudeikis
ms.author: gwallace
ms.date: 07/19/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: d4f53238951784a74e6e3fc8a73d1f112ce75608
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139119"
---
# <a name="manage-projects-templates-image-streams-in-an-azure-red-hat-openshift-cluster"></a>Управление проектами, шаблонами, потоками изображений в кластере Azure Red Hat OpenShift 

В контейнерной платформе OpenShift проекты используются для группы и изоляции связанных объектов. Как администратор, вы можете предоставить разработчикам доступ к конкретным проектам, позволить им создавать свои собственные проекты и предоставить им административные права на отдельные проекты.

## <a name="self-provisioning-projects"></a>Проекты самообеспечения

Вы можете позволить разработчикам создавать свои собственные проекты. Конечная точка API отвечает за подготовку проекта в соответствии с шаблоном, названным проект-запросом. Веб-консоль `oc new-project` и команда используют эту конечную точку, когда разработчик создает новый проект.

При отправке запроса на проект API заменяет следующие параметры в шаблоне:

| Параметр               | Описание                                    |
| ----------------------- | ---------------------------------------------- |
| PROJECT_NAME            | Имя проекта. Обязательный элемент.             |
| PROJECT_DISPLAYNAME     | Название дисплея проекта. Значение может быть пустым. |
| PROJECT_DESCRIPTION     | Описание проекта. Значение может быть пустым.  |
| PROJECT_ADMIN_USER      | Имя пользователя пользователя.       |
| PROJECT_REQUESTING_USER | Имя пользователя запрашивающего пользователя.           |

Доступ к API предоставляется разработчикам с обязательной связывающей ролью кластера самообеспечения. Эта функция доступна всем аутентифицированным разработчикам по умолчанию.

## <a name="modify-the-template-for-a-new-project"></a>Изменение шаблона для нового проекта 

1. Войти в систему `customer-admin` как пользователь с привилегиями.

2. Отсеяйте шаблон запроса проекта по умолчанию.

   ```
   oc edit template project-request -n openshift
   ```

3. Удалите шаблон проекта по умолчанию из процесса обновления Azure Red Hat OpenShift (ARO), добавив следующую аннотацию:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Шаблон запроса проекта не будет обновляться в процессе обновления ARO. Это позволяет клиентам настроить шаблон и сохранить эти настройки при обновлении кластера.

## <a name="disable-the-self-provisioning-role"></a>Отключить роль самообеспечения

Можно запретить группе пользователей, удостоверяющей подлинность, самостоятельно предоставлять новые проекты.

1. Войти в систему `customer-admin` как пользователь с привилегиями.

2. Отобразить связывание кластерной роли самообеспеченных.

   ```
   oc edit clusterrolebinding.rbac.authorization.k8s.io self-provisioners
   ```

3. Удалите роль из процесса обновления ARO, добавив `openshift.io/reconcile-protect: "true"`следующую аннотацию: .

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

4. Изменение связывания роли `system:authenticated:oauth` кластера, чтобы предотвратить создание проектов:

   ```
   apiVersion: rbac.authorization.k8s.io/v1
   groupNames:
   - osa-customer-admins
   kind: ClusterRoleBinding
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
     labels:
       azure.openshift.io/owned-by-sync-pod: "true"
     name: self-provisioners
   roleRef:
     name: self-provisioner
   subjects:
   - kind: SystemGroup
     name: osa-customer-admins
   ```

## <a name="manage-default-templates-and-imagestreams"></a>Управление шаблонами по умолчанию и изображениями

В Azure Red Hat OpenShift вы можете отключить обновления для любых `openshift` шаблонов по умолчанию и потоков изображений внутри пространства имен.
Чтобы отключить обновления для `Templates` `ImageStreams` ВСЕХ `openshift` и в пространстве имен:

1. Войти в систему `customer-admin` как пользователь с привилегиями.

2. Изменение `openshift` пространства имен:

   ```
   oc edit namespace openshift
   ```

3. Удалите `openshift` название из процесса обновления ARO, добавив следующую аннотацию:`openshift.io/reconcile-protect: "true"`

   ```
   ...
   metadata:
     annotations:
       openshift.io/reconcile-protect: "true"
   ...
   ```

   Любой отдельный `openshift` объект в пространстве имен может быть удален `openshift.io/reconcile-protect: "true"` из процесса обновления, добавив к нему аннотацию.

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте учебник:
> [!div class="nextstepaction"]
> [Создание кластера Azure Red Hat OpenShift](tutorial-create-cluster.md)
