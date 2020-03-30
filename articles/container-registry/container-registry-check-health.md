---
title: Проверка работоспособности реестра
description: Узнайте, как запустить быструю диагностическую команду для выявления общих проблем при использовании реестра контейнеров Azure, включая локальную конфигурацию Docker и подключение к реестру
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: ea4432c9e92c4a0380517e39678814e2d1cb3bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456408"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Проверьте работоспособность реестра контейнеров Azure

При использовании реестра контейнеров Azure иногда могут возникнуть проблемы. Например, вы не сможете вытащить изображение контейнера из-за проблемы с Docker в локальной среде. Или сетевая проблема может помешать вам подключиться к реестру. 

В качестве первого диагностического шага запустите команду [проверки здоровья az acr,][az-acr-check-health] чтобы получить информацию о здоровье окружающей среды и опциональный доступ к целевому реестру. Эта команда доступна в версии Azure CLI 2.0.67 или позже. Если вам нужно установить или обновить, [см.][azure-cli]

## <a name="run-az-acr-check-health"></a>Выполнить az acr проверки здоровья

В последующих примерах отображается различные `az acr check-health` способы выполнения команды.

> [!NOTE]
> Если вы запустите команду в Azure Cloud Shell, локальная среда не проверяется. Тем не менее, вы можете проверить доступ к целевому реестру.

### <a name="check-the-environment-only"></a>Проверка только среды

Чтобы проверить локальную конфигурацию Docker daemon, CLI и клиентскую конфигурацию Helm, запустите команду без дополнительных параметров:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Проверка окружающей среды и целевого реестра

Чтобы проверить доступ к реестру, а также выполнить локальные проверки окружающей среды, передайте название целевого реестра. Пример:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Отчеты об ошибках

Команда регистрирует информацию в стандартном выходе. Если проблема обнаружена, она предоставляет код ошибки и описание. Для получения дополнительной информации о кодах [error reference](container-registry-health-error-reference.md)и возможных решениях см.

По умолчанию команда останавливается всякий раз, когда обнаруживает ошибку. Вы также можете запустить команду таким образом, чтобы она обеспечивает выход для всех проверок работоспособности, даже если ошибки найдены. Добавьте `--ignore-errors` параметр, как показано в следующих примерах:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Образец вывода:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Дальнейшие действия

Для получения подробной информации о кодах ошибок, возвращенных командой [проверки аз-акр,][az-acr-check-health] см. [Health check error reference](container-registry-health-error-reference.md)

Ознакомьтесь с [часто](container-registry-faq.md) задаваемыми вопросами и другими известными вопросами о реестре контейнеров Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
