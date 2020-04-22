---
title: Включить управляемое удостоверение в группе контейнеров
description: Узнайте, как включить управляемые иждивенцы в экземплярах контейнеров Azure, которые могут аутентифицироваться с помощью других служб Azure
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 19d2ab22eea15278c7753046f9222c7856fbf5ef
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685645"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Использование управляемых удостоверений для службы "Экземпляры контейнеров Azure"

Используйте [управляемые удостоверения для ресурсов Azure](../active-directory/managed-identities-azure-resources/overview.md), чтобы выполнять в службе "Экземпляры контейнеров Azure" код, который взаимодействует с другими службами Azure, без обработки секретов или учетных данных в коде. Эта функция позволяет создать развертывание службы "Экземпляры контейнеров Azure" с автоматическим управлением удостоверениями через Azure Active Directory.

Из этой статьи вы узнаете, как использовать управляемые удостоверения в службе "Экземпляры контейнеров Azure" и выполнять следующие действия:

> [!div class="checklist"]
> * применение к группе контейнеров удостоверения, назначаемого пользователем или системой;
> * Предоставить доступ к хранилищу ключей Azure
> * Используйте управляемое удостоверение для доступа к хранилищу ключей из работающего контейнера

Вы можете адаптировать эти примеры, чтобы применить собственные удостоверения в службе "Экземпляры контейнеров Azure" для доступа к другим службам Azure. Это интерактивные примеры. Но на практике для доступа к службам Azure в образах контейнеров будет выполняться код.

> [!NOTE]
> Сейчас нельзя использовать управляемое удостоверение в группе контейнеров, развернутой в виртуальной сети.

## <a name="why-use-a-managed-identity"></a>Для чего нужны управляемые удостоверения?

Управляемое удостоверение в запущенном контейнере можно использовать для проверки подлинности в любой [службе, которая поддерживает проверку подлинности Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), без необходимости управлять учетными данными в коде. Для служб, не поддерживающих аутентификацию AD, можно хранить секреты в хранилище ключей Azure и использовать управляемый идентификатор для доступа к хранилищу ключей для получения учетных данных. Дополнительные сведения об использовании управляемых удостоверений см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии. В настоящее время управляемые идентификаторы в контейнерных инстанциях Azure поддерживаются только контейнерами Linux, а еще не с контейнерами Windows.
>  

### <a name="enable-a-managed-identity"></a>Включение управляемого удостоверения

 Служба "Экземпляры контейнеров Azure" поддерживает управляемые удостоверения для ресурсов Azure, начиная с REST API версии 2018-10-01 и соответствующих версий пакетов SDK и средств. При создании группы контейнеров вы можете включить одно или несколько управляемых удостоверений, задав свойство [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity). Вы также можете включить или обновить управляемые идентификаторы после запуска группы контейнеров - либо действие приводит к перезапуску группы контейнеров. Настроить удостоверения для новой или существующей группы контейнеров можно с помощью Azure CLI, шаблона Resource Manager или файла YAML. 

Служба "Экземпляры контейнеров Azure" поддерживает оба типа управляемых удостоверений Azure: назначаемые пользователем и назначаемые системой. Для группы контейнеров вы можете указать назначаемое системой удостоверение, одно или несколько назначаемых пользователей удостоверений или даже удостоверения обоих типов. 

* **Назначаемое пользователем** управляемое удостоверение создается как отдельный ресурс Azure в клиенте Azure AD, который является доверенным для используемой подписки. После создания удостоверения вы можете назначить его одному или нескольким ресурсам Azure (в службе "Экземпляры контейнеров Azure" или других службах Azure). Управление жизненным циклом назначаемого пользователем удостоверения осуществляется отдельно от жизненного цикла группы контейнеров или других ресурсов, для которых оно назначено. Это особенно полезно при использовании в службе "Экземпляры контейнеров Azure". Так как удостоверение не ограничено временем существования группы контейнеров, вы можете повторно использовать его в сочетании с набором стандартных параметров, чтобы легко создавать копии развертываний групп контейнеров.

* **Назначаемое системой** управляемое удостоверение настраивается непосредственно для группы контейнеров в службе "Экземпляры контейнеров Azure". Если включен такой режим, Azure создает удостоверение для группы в клиенте Azure AD, который является доверенным в подписке этого экземпляра. Учетные данные созданного удостоверения подготавливаются в каждом контейнере, входящем в группу контейнеров. Жизненный цикл назначаемого системой удостоверения напрямую связан с группой контейнеров, для которой оно включено. При удалении группы Azure автоматически удаляет учетные данные и удостоверение из Azure AD.

### <a name="use-a-managed-identity"></a>Использование управляемого удостоверения

Для использования управляемой идентификации иждивенец должен быть первоначально предоставлен доступ к одному или несколько ресурсам службы Azure (например, веб-приложению, хранилищу ключей или учетной записи хранилища) в подписке. Для доступа к ресурсам Azure из запущенного контейнера следует из кода получить *маркер доступа* от конечной точки Azure AD. Затем код отправляет маркер доступа в вызов службы, которая поддерживает проверку подлинности Azure AD. 

Использование управляемого удостоверения в запущенном контейнере по сути ничем не отличается от использования удостоверений на виртуальной машине Azure. Ознакомьтесь с документацией по виртуальным машинам, где описано использование [маркера](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell или Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) либо [пакетов SDK для Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей вам понадобится Azure CLI 2.0.49 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Создание хранилища ключей Azure

Примеры в этой статье используют управляемый имитизм в экземплярах контейнеров Azure для доступа к секрету хранилища ключей Azure. 

Для начала создайте группу ресурсов с именем *myResourceGroup* в регионе *eastus* с помощью следующей команды [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Используйте команду [создания клавиатуры az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) для создания хранилища ключей. Обязательно укажите уникальное имя хранилища ключей. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Храните секрет образца в хранилище ключей, используя секретную команду [набора ключей az:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Продолжить следующие примеры для доступа к хранилищу ключей, используя либо назначенный пользователем, либо назначенный системой управляемый интимент в Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Пример 1: Используйте удостоверение, назначенное пользователем, для доступа к хранилищу ключей Azure

### <a name="create-an-identity"></a>Создание удостоверения

Первым делом создайте в подписке удостоверение, используя команду [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). Можно использовать ту же группу ресурсов, что и создать хранилище ключей, или использовать другую.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Чтобы применить это удостоверение на следующих шагах, выполните команду [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) для сохранения идентификатора субъекта-службы и идентификатора ресурса в переменных.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Применение к группе контейнеров удостоверения, назначаемого пользователем

Выполнить следующий [аз-контейнер для](/cli/azure/container?view=azure-cli-latest#az-container-create) создания экземпляра `azure-cli` контейнера на основе изображения Майкрософт. В этом примере предоставляется группа с одним контейнером, которую можно использовать в интерактивном режиме для запуска Azure CLI для доступа к другим службам Azure. В этом разделе используется только базовая операционная система Ubuntu. 

Параметр `--assign-identity` передает группе назначаемое пользователем управляемое удостоверение. Длительно выполняющаяся команда поддерживает выполнение контейнера. В этом примере используется та же группа ресурсов, используемая для создания хранилища ключей, но можно указать другой.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Через несколько секунд вы должны получить ответ из интерфейса командной строки Azure, указывающий, что развертывание завершено. Проверьте его состояние с помощью команды [шоу-шоу контейнеров az.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Раздел `identity` в выходных данных выглядит примерно так, как показано ниже, и подтверждает, что удостоверение настроено в группе контейнеров. Параметр `principalID` под `userAssignedIdentities` обозначает субъект-службу удостоверения, которое вы создали в Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Предоставление пользовательского доступа к хранилищу ключей

Запустите следующую команду [набора ключов azvault,](/cli/azure/keyvault?view=azure-cli-latest) чтобы установить политику доступа в хранилище ключей. Следующий пример позволяет назначенному пользователеу удостоверению получить секреты из хранилища ключей:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Используйте удостоверение пользователя, назначенное пользователем, чтобы получить секрет из хранилища ключей

Теперь можно использовать управляемую идентификацию в экземпляре бегущего контейнера для доступа к хранилищу ключей. Первый запуск оболочки Баш в контейнере:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Выполните приведенные ниже команды в этой оболочке bash в контейнере. Чтобы получить маркер доступа, чтобы использовать Active Directory Azure для проверки подлинности хранилища ключей, запустите следующую команду:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Выходные данные:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Чтобы сохранить маркер доступа в переменной для проверки подлинности в последующих командах, выполните такую команду:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Теперь используйте токен доступа для проверки подлинности в хранилище ключей и прочитайте секрет. Не забудьте заменить имя вашего ключа хранилище в URL *(https:\// mykeyvault.vault.azure.net/ ...*

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Ответ с полученным значением секрета выглядит примерно так: Для получения секрета в коде необходимо выполнить синтаксический анализ этих выходных данных. Далее этот секрет применяется в последующих операциях для доступа к другому ресурсу Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Пример 2: Используйте установленное системой удостоверение для доступа к хранилищу ключей Azure

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Применение к группе контейнеров удостоверения, назначаемого системой

Выполнить следующий [аз-контейнер для](/cli/azure/container?view=azure-cli-latest#az-container-create) создания экземпляра `azure-cli` контейнера на основе изображения Майкрософт. В этом примере предоставляется группа с одним контейнером, которую можно использовать в интерактивном режиме для запуска Azure CLI для доступа к другим службам Azure. 

Параметр `--assign-identity` без дополнительных значений включает для группы назначаемое системой управляемое удостоверение. Идентификация отослана к группе ресурсов контейнерной группы. Длительно выполняющаяся команда поддерживает выполнение контейнера. В этом примере используется та же группа ресурсов, используемая для создания хранилища ключей, но можно указать другой.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Через несколько секунд вы должны получить ответ из интерфейса командной строки Azure, указывающий, что развертывание завершено. Проверьте его состояние с помощью команды [шоу-шоу контейнеров az.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Раздел `identity` в выходных данных выглядит примерно так, как показано ниже, и подтверждает создание назначаемого системой удостоверения в Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Присвойте переменной значение `principalId` (идентификатор субъекта-службы), чтобы использовать его в последующих шагах.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Грант контейнерной группы доступ к хранилищу ключей

Запустите следующую команду [набора ключов azvault,](/cli/azure/keyvault?view=azure-cli-latest) чтобы установить политику доступа в хранилище ключей. Следующий пример позволяет управляемой системой идентификации, чтобы получить секреты из хранилища ключей:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Используйте идентификацию группы контейнеров, чтобы получить секрет из хранилища ключей

Теперь можно использовать управляемый итог для доступа к хранилищу ключей в экземпляре бегущего контейнера. Первый запуск оболочки Баш в контейнере:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Выполните приведенные ниже команды в этой оболочке bash в контейнере. Первый вход в Azure CLI с помощью управляемой идентификации:

```bash
az login --identity
```

Из бегущего контейнера извлекаем секрет из хранилища ключей:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Значение секрета извлекается:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Включение управляемого удостоверения с помощью шаблона Resource Manager

Чтобы включить управляемое удостоверение для группы контейнеров с помощью [шаблона Resource Manager](container-instances-multi-container-group.md), присвойте свойству `identity` объекта `Microsoft.ContainerInstance/containerGroups` значение объекта `ContainerGroupIdentity`. В следующих фрагментах кода показана настройка свойства `identity` для разных сценариев. Подробные сведения см. в [справочнике по шаблонам Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Укажите `apiVersion` минимум `2018-10-01`.

### <a name="user-assigned-identity"></a>Назначаемое пользователем удостоверение

Назначаемое пользователем удостоверение — это идентификатор ресурса в следующем формате:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Вы можете указать одно или несколько назначаемых пользователем удостоверений.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Назначаемое системой удостоверение

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Удостоверения, назначаемые системой и пользователями

Вы можете одновременно включить для группы контейнеров назначаемое системой удостоверение и одно или несколько назначаемых пользователей удостоверений.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Включение управляемого удостоверения с помощью YAML-файла

Чтобы включить управляемое удостоверение в группе контейнеров, развернутой с помощью [YAML-файла](container-instances-multi-container-yaml.md), добавьте следующий YAML-файл.
Укажите `apiVersion` минимум `2018-10-01`.

### <a name="user-assigned-identity"></a>Назначаемое пользователем удостоверение

Назначаемое пользователем удостоверение — это идентификатор ресурса в следующем формате: 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Вы можете указать одно или несколько назначаемых пользователем удостоверений.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Назначаемое системой удостоверение

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Удостоверения, назначаемые системой и пользователями

Вы можете одновременно включить для группы контейнеров назначаемое системой удостоверение и одно или несколько назначаемых пользователей удостоверений.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Следующие шаги

Из этой статьи вы узнали, как использовать управляемые удостоверения в службе "Экземпляры контейнеров Azure" и выполнять следующие действия:

> [!div class="checklist"]
> * применение к группе контейнеров удостоверения, назначаемого пользователем или системой;
> * Предоставить доступ к хранилищу ключей Azure
> * Используйте управляемое удостоверение для доступа к хранилищу ключей из работающего контейнера

* Дополнительные сведения см. в статье [Использование управляемых удостоверений в Службе приложений и Функциях Azure](/azure/active-directory/managed-identities-azure-resources/).

* Ознакомьтесь с [примером использования](https://medium.com/@samkreter/c98911206328) управляемого имита для доступа к своду ключей из экземпляров контейнеров Azure.
