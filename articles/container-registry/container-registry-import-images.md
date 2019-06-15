---
title: Импорт образов контейнеров в Реестр контейнеров Azure
description: Импорт образов контейнеров в Реестр контейнеров Azure с помощью API-интерфейсов Azure без использования команд Docker.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 02/06/2019
ms.author: danlep
ms.openlocfilehash: b8a2280fe82e0f4be8e2812f5494150927642692
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827295"
---
# <a name="import-container-images-to-a-container-registry"></a>Импорт образов контейнеров в реестр контейнеров

Образы контейнеров можно легко импортировать (копировать) в Реестр контейнеров Azure, не используя команды Docker. Например, импортируйте образы из реестра разработки в реестр рабочей среды или скопируйте базовые образы из общедоступного реестра.

Реестр контейнеров Azure обрабатывает ряд распространенных сценариев для копирования образов из имеющегося реестра:

* импорт из общедоступного реестра;

* импорт из другого реестра контейнеров Azure в той же или другой подписке Azure;

* импорт из закрытого реестра контейнеров, не относящегося к Azure.

Импорт образов в Реестр контейнеров Azure имеет следующие преимущества по сравнению с использованием команд CLI Docker:

* Так как для вашей клиентской среды не требуется локальная установка Docker, импортируйте любой образ контейнера независимо от поддерживаемого типа ОС.

* При импорте образов с несколькими архитектурами (например, официальные образы Docker) копируются образы для всех архитектур и платформ, указанных в списке манифеста.

Чтобы импортировать образы контейнеров при работе с этой статьей, требуется запустить Azure CLI в Azure Cloud Shell или локально (рекомендуется версия 2.0.55 или более поздняя). Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli].

> [!NOTE]
> Если нужно распространить идентичные образы контейнеров в нескольких регионах Azure, Реестр контейнеров Azure также поддерживает [георепликацию](container-registry-geo-replication.md). Путем георепликации реестра (требуется SKU уровня "Премиум") можно обслуживать несколько регионов с одинаковыми именами образов и тегов из одного реестра.
>

## <a name="prerequisites"></a>Технические условия

Если у вас еще нет Реестра контейнеров Azure, создайте его. Действия описаны в [кратком руководстве по созданию закрытого реестра контейнеров с помощью Azure CLI](container-registry-get-started-azure-cli.md).

Чтобы импортировать образ в Реестр контейнеров Azure, необходимо обладать разрешениями на запись в целевой реестр (по крайней мере роль "Участник"). Ознакомьтесь со статьей [Роли и разрешения реестра контейнеров Azure](container-registry-roles.md). 

## <a name="import-from-a-public-registry"></a>Импорт из общедоступного реестра

### <a name="import-from-docker-hub"></a>Импорт из центра Docker

Например, используйте команду [az acr import][az-acr-import], чтобы импортировать образ `hello-world:latest` с несколькими архитектурами из центра Docker в реестр с именем *myregistry*. Так как `hello-world` — это официальный образ из центра Docker, этот образ находится в используемом по умолчанию репозитории `library`. Добавьте в значение параметра образа `--source` имя репозитория и при необходимости тег. (Вы можете дополнительно определить образ по его хэш-коду манифеста, а не по тегу, который гарантирует определенную версию образа.)
 
```azurecli
az acr import --name myregistry --source docker.io/library/hello-world:latest --image hello-world:latest
```

Вы можете проверить, что несколько манифестов связаны с этим изображением, выполнив команду `az acr repository show-manifests`:

```azurecli
az acr repository show-manifests --name myregistry --repository hello-world
```

В следующем примере выполняется импорт открытого образа из репозитория `tensorflow` в центр Docker:

```azurecli
az acr import --name myregistry --source docker.io/tensorflow/tensorflow:latest-gpu --image tensorflow:latest-gpu
```

### <a name="import-from-microsoft-container-registry"></a>Импорт из Реестра контейнеров Майкрософт

Например, импортируйте последний образ Windows Server Core из репозитория `windows` в Реестр контейнеров Майкрософт.

```azurecli
az acr import --name myregistry --source mcr.microsoft.com/windows/servercore:latest --image servercore:latest
```

## <a name="import-from-another-azure-container-registry"></a>Импорт из другого Реестра контейнеров Azure

Вы можете импортировать образ из другого Реестра контейнеров Azure, используя интегрированные разрешения Azure Active Directory.

* Необходимо обладать разрешениями Azure Active Directory на чтение из исходного реестра (роль "Читатель") и запись в целевой реестр (роль "Участник").

* Реестр может находиться в той же или другой подписке Azure в одном клиенте Active Directory.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Импорт из реестра в одной и той же подписке

Например, импортируйте образ `aci-helloworld:latest` из исходного реестра *mysourceregistry* в *myregistry* в той же подписке Azure.

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld:latest --image hello-world:latest
```

В следующем примере выполняется импорт образа по хэш-коду манифеста (хэш-код SHA-256, представленный в виде `sha256:...`), а не по тегу:

```azurecli
az acr import --name myregistry --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Импорт из реестра в другой подписке

В следующем примере *mysourceregistry* находится в другой подписке, нежели *myregistry*, и в одном и том же клиенте Active Directory. Укажите идентификатор ресурса исходного реестра с параметром `--registry`. Обратите внимание, что параметр `--source` указывает только имя исходного репозитория и образа, а не имя сервера входа в реестр.
 
```azurecli
az acr import --name myregistry --source sourcerepo/aci-helloworld:latest --image aci-hello-world:latest --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Импорт из реестра с использованием учетных данных субъекта-службы

Чтобы выполнить импорт из реестра, к которому вы не можете получить доступ с помощью разрешений Active Directory, можно использовать учетные данные субъекта-службы (если доступно). Укажите идентификатор приложения и пароль [субъекта-службы](container-registry-auth-service-principal.md) Active Directory с разрешениями на доступ ACRPull к исходному реестру. Субъект-службу можно использовать для систем сборки и других автоматических систем, чтобы импортировать образы в ваш реестр.

```azurecli
az acr import --name myregistry --source sourceregistry.azurecr.io/sourcerepo/sourceimage:tag --image targetimage:tag --username <SP_App_ID> –-password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Импорт из закрытого реестра контейнеров, не относящегося к Azure

Импортируйте образ из частного реестра, указав учетные данные, которые обеспечивают доступ к реестру с правами на извлечение данных. Например, извлеките образ из частного реестра Docker: 

```azurecli
az acr import --name myregistry --source docker.io/sourcerepo/sourceimage:tag --image sourceimage:tag --username <username> --password <password>
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали об импорте образов контейнеров в Реестр контейнеров Azure из общедоступного реестра или другого частного реестра. Сведения о дополнительных вариантах импорта см. в справочнике по командам [az acr import][az-acr-import]. 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
