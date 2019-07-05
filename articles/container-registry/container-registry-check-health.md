---
title: Проверьте работоспособность реестр в реестре контейнеров Azure
description: Сведения о запуске Быстрые команды диагностики для выявления распространенных проблем при использовании реестра контейнеров Azure, включая локальные конфигурации Docker и подключение к реестру
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: danlep
ms.openlocfilehash: 3e5b5467f9fa25e23f6661c6630d346aa85e2205
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555102"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Проверьте работоспособность реестр контейнеров Azure

Если вы используете реестр контейнеров Azure, иногда могут возникнуть проблемы. Например может не появиться возможность извлечь отдельный образ контейнера из-за проблемы с Docker в локальной среде. Или проблемы в сети может не позволит подключиться к реестру. 

В качестве первого шага диагностики, запустите [проверки работоспособности az acr][az-acr-check-health] command to get information about the health of the environment and optionally access to a target registry. This command is available in Azure CLI version 2.0.67 or later. If you need to install or upgrade, see [Install Azure CLI][azure-cli].

## <a name="run-az-acr-check-health"></a>Выполните проверку работоспособности az acr

Следующие примеры демонстрируют различные способы запуска `az acr check-health` команды.

> [!NOTE]
> Если вы выполняете команду в Azure Cloud Shell, локальной среде не проверяется. Тем не менее вы можете проверить доступ к папке реестра.

### <a name="check-the-environment-only"></a>Проверьте среду только

Проверяемый локальном Docker управляющей программы версии интерфейса командной строки и конфигурации клиента Helm, выполните команду без дополнительных параметров:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Проверьте среду и конечный реестр

Чтобы проверить доступ к реестру, а также выполнять проверки в локальной среде, передайте имя реестра целевой. Пример:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Отчеты об ошибках

Команда записывает сведения в стандартный поток вывода. При обнаружении проблемы, обеспечивается код ошибки и описание. Дополнительные сведения о кодах и возможные решения см. в разделе [справочников по ошибкам](container-registry-health-error-reference.md).

По умолчанию команда останавливается при обнаружении ошибки. Чтобы на ней присутствовала выходных данных для выполнения всех проверок работоспособности, также можно выполнить команду, даже если будут обнаружены ошибки. Добавление `--ignore-errors` параметра, как показано в следующих примерах:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Пример выходных данных:

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

Дополнительные сведения о кодах ошибок, возвращаемых [проверки работоспособности az acr][az-acr-check-health] команды, см. в разделе [справочников по ошибкам проверки работоспособности](container-registry-health-error-reference.md).

См. в разделе [часто задаваемые вопросы о](container-registry-faq.md) для часто задаваемых вопросов и других известных проблемах, о реестре контейнеров Azure.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
