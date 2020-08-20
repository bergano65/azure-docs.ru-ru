---
title: Импорт образов контейнеров
description: Импорт образов контейнеров в Реестр контейнеров Azure с помощью API-интерфейсов Azure без использования команд Docker.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 66c3a8b19e2288c1f8720dd4fe79f348a11f052e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660501"
---
# <a name="import-container-images-to-a-container-registry"></a>Импорт образов контейнеров в реестр контейнеров

Образы контейнеров можно легко импортировать (копировать) в Реестр контейнеров Azure, не используя команды Docker. Например, импортируйте образы из реестра разработки в реестр рабочей среды или скопируйте базовые образы из общедоступного реестра.

Реестр контейнеров Azure обрабатывает ряд распространенных сценариев для копирования образов из имеющегося реестра:

* Импорт из общедоступного реестра

* импорт из другого реестра контейнеров Azure в той же или другой подписке Azure;

* Импорт из закрытого реестра контейнеров, не относящегося к Azure

Импорт образов в Реестр контейнеров Azure имеет следующие преимущества по сравнению с использованием команд CLI Docker:

* Так как в клиентской среде не требуется локальная установка DOCKER, импортируйте любой образ контейнера независимо от поддерживаемого типа ОС.

* При импорте образов с несколькими архитектурами (например, официальные образы Docker) копируются образы для всех архитектур и платформ, указанных в списке манифеста.

* Доступ к исходному и целевому реестрам не обязательно должен использовать общедоступные конечные точки реестра.

Чтобы импортировать образы контейнеров при работе с этой статьей, требуется запустить Azure CLI в Azure Cloud Shell или локально (рекомендуется версия 2.0.55 или более поздняя). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

> [!NOTE]
> Если нужно распространить идентичные образы контейнеров в нескольких регионах Azure, Реестр контейнеров Azure также поддерживает [георепликацию](container-registry-geo-replication.md). При георепликации реестра (требуется уровень служб Premium) можно обслуживать несколько регионов с одинаковыми именами образов и тегов из одного реестра.
>

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет Реестра контейнеров Azure, создайте его. Инструкции см. [в разделе Краткое руководство. создание закрытого реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md).

Чтобы импортировать образ в реестр контейнеров Azure, удостоверение должно иметь разрешения на запись в целевой реестр (по крайней мере роль участника или пользовательскую роль, которая разрешает действие Импортимаже). Ознакомьтесь со статьей [Роли и разрешения реестра контейнеров Azure](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Импорт из общедоступного реестра

### <a name="import-from-docker-hub"></a>Импорт из центра Docker

Например, используйте команду [az acr import][az-acr-import], чтобы импортировать образ `hello-world:latest` с несколькими архитектурами из центра Docker в реестр с именем *myregistry*. Так как `hello-world` — это официальный образ из центра Docker, этот образ находится в используемом по умолчанию репозитории `library`. Добавьте в значение параметра образа `--source` имя репозитория и при необходимости тег. (Вы можете дополнительно определить образ по его хэш-коду манифеста, а не по тегу, который гарантирует определенную версию образа.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Вы можете проверить, что несколько манифестов связаны с этим изображением, выполнив команду `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

В следующем примере выполняется импорт открытого образа из репозитория `tensorflow` в центр Docker:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Импорт из Реестра контейнеров Майкрософт

Например, импортируйте `ltsc2019` образ Windows Server Core из `windows` репозитория в реестре контейнеров Microsoft.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-another-azure-container-registry"></a>Импорт из другого Реестра контейнеров Azure

Вы можете импортировать образ из другого Реестра контейнеров Azure, используя интегрированные разрешения Azure Active Directory.

* Удостоверение должно иметь Azure Active Directory разрешения на чтение из исходного реестра (роль читателя) и импорт в целевой реестр (роль участника или [пользовательскую роль](container-registry-roles.md#custom-roles) , которая разрешает действие импортимаже).

* Реестр может находиться в той же или другой подписке Azure в одном клиенте Active Directory.

* [Общий доступ](container-registry-access-selected-networks.md#disable-public-network-access) к исходному реестру может быть отключен. Если общий доступ отключен, укажите исходный реестр по ИДЕНТИФИКАТОРу ресурса, а не по имени сервера входа в реестр.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Импорт из реестра в одной и той же подписке

Например, импортируйте образ `aci-helloworld:latest` из исходного реестра *mysourceregistry* в *myregistry* в той же подписке Azure.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

В следующем примере показано, как импортировать `aci-helloworld:latest` образ в *myregistry* из исходного реестра *мисаурцерегистри* , в котором доступ к общедоступной конечной точке реестра отключен. Укажите идентификатор ресурса исходного реестра с параметром `--registry`. Обратите внимание, что `--source` параметр указывает только исходный репозиторий и тег, а не имя сервера входа в реестр.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

В следующем примере выполняется импорт образа по хэш-коду манифеста (хэш-код SHA-256, представленный в виде `sha256:...`), а не по тегу:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Импорт из реестра в другой подписке

В следующем примере *mysourceregistry* находится в другой подписке, нежели *myregistry*, и в одном и том же клиенте Active Directory. Укажите идентификатор ресурса исходного реестра с параметром `--registry`. Обратите внимание, что `--source` параметр указывает только исходный репозиторий и тег, а не имя сервера входа в реестр.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Импорт из реестра с использованием учетных данных субъекта-службы

Чтобы выполнить импорт из реестра, к которому вы не можете получить доступ с помощью разрешений Active Directory, можно использовать учетные данные субъекта-службы (если доступно). Укажите идентификатор приложения и пароль [субъекта-службы](container-registry-auth-service-principal.md) Active Directory с разрешениями на доступ ACRPull к исходному реестру. Субъект-службу можно использовать для систем сборки и других автоматических систем, чтобы импортировать образы в ваш реестр.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Импорт из закрытого реестра контейнеров, не относящегося к Azure

Импортируйте образ из частного реестра, указав учетные данные, которые обеспечивают доступ к реестру с правами на извлечение данных. Например, извлеките образ из частного реестра Docker: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об импорте образов контейнеров в Реестр контейнеров Azure из общедоступного реестра или другого частного реестра. Сведения о дополнительных вариантах импорта см. в справочнике по командам [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
