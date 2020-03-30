---
title: Управляемая идентификация в задаче ACR
description: Включить управляемое удостоверение для ресурсов Azure в задаче реестра контейнеров Azure, чтобы позволить задаче получить доступ к другим ресурсам Azure, включая другие частные реестры контейнеров.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111768"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Используйте иноеудостоверения, управляемые Azure, в задачах ACR 

Включите [управляемое удостоверение для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md) в [задаче ACR,](container-registry-tasks-overview.md)чтобы задача могла получить доступ к другим ресурсам Azure без необходимости предоставлять или управлять учетными данными. Например, используйте управляемое удостоверение, чтобы шаг задачи вытягивал или перекачив изображения контейнеров в другой реестр.

В этой статье вы узнаете, как использовать Azure CLI для включения в задачу ACR назначенного пользователя или системного управления. Вы можете использовать оболочку облака Azure или локальную установку Azure CLI. Если вы хотите использовать его локально, требуется версия 2.0.68 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

Для целей иллюстрации в примере команды в этой статье используют [задачу az acr, создаваемую][az-acr-task-create] для создания основной задачи построения изображений, позволяющей управлять имитатором. Для примеров сценариев доступа к защищенным ресурсам из задачи ACR с помощью управляемого удостоверения можно просмотреть:

* [Аутентификация в разных реестрах](container-registry-tasks-cross-registry-authentication.md)
* [Доступ к внешним ресурсам с секретами, хранящимися в Хранилище ключей Azure](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Для чего нужны управляемые удостоверения?

Управляемый имитатор ресурсов Azure предоставляет выбранные службы Azure с автоматически управляемым итек в Active Directory Azure. Можно настроить задачу ACR с управляемым итеворност, чтобы задача могла получить доступ к другим защищенным ресурсам Azure, не передавая учетные данные в шагах задачи.

Управляемые удостоверения бывают двух типов:

* *Назначенные пользователем идентификаторы,* которые можно назначить нескольким ресурсам и сохранять сятвику столько, сколько хотите. Назначаемые пользователем удостоверения сейчас доступны в предварительной версии.

* Система *назначенных идентификаторов*, которая является уникальной для конкретного ресурса, таких как задача ACR и длится в течение всего срока службы этого ресурса.

Вы можете включить один или оба типа идентификации в задаче ACR. Предоставите identity доступ к другому ресурсу, как и любой принцип безопасности. При выполнении задачи он использует иноек для доступа к ресурсу в любых шагах задач, требующих доступа.

## <a name="steps-to-use-a-managed-identity"></a>Шаги по использованию управляемой идентификации

Выполните эти шаги высокого уровня, чтобы использовать управляемый итог с задачей ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Необязательно) Создание удостоверения, назначенного пользователем

Если вы планируете использовать удостоверение, назначенное пользователем, используйте существующую идентификацию или создайте иную личность с помощью Azure CLI или других инструментов Azure. Например, используйте команду [создания итога итога az.][az-identity-create] 

Если вы планируете использовать только установленную системой идентификацию, пропустите этот шаг. При создании задачи ACR создается установленная системой идентификация.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Включить идентификацию в задаче ACR

При создании задачи ACR опционально включите удостоверение, назначенное пользователем, установленное системой удостоверение личности или и то, и другое. Например, перейдите `--assign-identity` параметр при запуске задачи az [acr создать][az-acr-task-create] команду в Azure CLI.

Для включения установленного системой `--assign-identity` удостоверения, `assign-identity [system]`пройдите без значения или . В следующем примере команда создает задачу Linux из общедоступного `hello-world` репозитория GitHub, который создает изображение и позволяет управлять идентификатором, назначенному системой:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Чтобы включить удостоверение, `--assign-identity` назначенное пользователем, передайте значение *идентификатора ресурса* идентификатора идентификатора. В следующем примере команда создает задачу Linux из общедоступного `hello-world` репозитория GitHub, который создает изображение и позволяет управлять идентификатором, назначенному пользователем:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Идентификатор идентификатора идентификатора можно, запустив команду [шоу идентификации az.][az-identity-show] Идентификатор ресурса для ID *myUserAssignedIdentity* в группе ресурсов *myResourceGroup* имеет форму: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Предоставить разрешения на идентификацию для доступа к другим ресурсам Azure

В зависимости от требований задачи выдаете разрешения на использование идентаторов для доступа к другим ресурсам Azure. Примеры приведены ниже.

* Назначайте управляемое удостоверение роли с помощью раздвижных, выталкивающих и тянующих или других разрешений в реестр контейнеров-объектов в Azure. Полный список ролей реестра можно узнать о [ролях и разрешениях реестра контейнеров Azure.](container-registry-roles.md) 
* Назначайте управляемой идентификации роль для чтения секретов в хранилище ключей Azure.

Используйте [Azure CLI](../role-based-access-control/role-assignments-cli.md) или другие инструменты Azure для управления ролевой базой доступа к ресурсам. Например, выполнить [назначение роли аз создать][az-role-assignment-create] команду для присвоения роли идентификации ресурсу. 

Следующий пример присваивает управляемой идентификации разрешения вытащить из реестра контейнеров. Команда определяет *основной идентификатор* идентификатор атлетии задачи и *идентификатор ресурса* целевого реестра.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Необязательно) Добавление учетных данных в задачу

Если вашей задаче нужны учетные данные, чтобы вытащить или вытолкнуть изображения в другой пользовательский реестр или получить доступ к другим ресурсам, добавьте учетные данные в задачу. Запустите [учетные данные задачи az acr, добавляйте][az-acr-task-credential-add] команду для добавления учетных данных, и передайте `--use-identity` параметр, чтобы указать, что идентификатор может получить доступ к учетным данным. 

Например, чтобы добавить учетные данные для системного удостоверения, назначенного для `use-identity [system]`проверки подлинности с *целевым реестром*контейнеров Azure, пройдите:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Чтобы добавить учетные данные для удостоверения личности, назначенного `use-identity` пользователем, для проверки подлинности в *целевом реестре,* передайте значение *идентификатора клиента* идентификации. Пример:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Вы можете получить идентификатор клиента идентификации, запустив команду [шоу-идентификации az.][az-identity-show] Идентификатор клиента — `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`это GUID формы.

### <a name="5-run-the-task"></a>5. Выполнить задачу

После настройки задачи с управляемым имитатором запустите задачу. Например, чтобы протестировать одну из задач, созданных в этой статье, вручную запустите ее с помощью команды [выполнения задач az acr.][az-acr-task-run] Если вы настроили дополнительные, автоматизированные триггеры задач, задача выполняется при автоматическом срабатывании.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали, как включить и использовать назначенное пользователем или системное управляемое удостоверение в задаче ACR. Для сценариев для доступа к защищенным ресурсам из задачи ACR с помощью управляемого удостоверения можно просмотреть:

* [Аутентификация в разных реестрах](container-registry-tasks-cross-registry-authentication.md)
* [Доступ к внешним ресурсам с секретами, хранящимися в Хранилище ключей Azure](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
