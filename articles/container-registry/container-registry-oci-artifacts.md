---
title: Отправка артефактов OCI в частный реестр контейнеров Azure
description: Отправка и извлечение артефактов с открытым контейнером (OCI) с помощью закрытого реестра контейнеров в Azure
services: container-registry
author: SteveLasker
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.custom: ''
ms.openlocfilehash: 69423f85aecdc3f8049a7e784888e1f71d0bc702
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182706"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Отправка и извлечение артефакта OCI с помощью реестра контейнеров Azure

Вы можете использовать реестр контейнеров Azure для хранения артефактов и управления ими [(OCI)](container-registry-image-formats.md#oci-artifacts) , а также для образов контейнеров, совместимых с DOCKER и DOCKER.

Чтобы продемонстрировать эту возможность, в этой статье показано, как использовать средство [реестра OCI как хранилище (Орас)](https://github.com/deislabs/oras) для отправки примера артефакта — текстового файла в реестр контейнеров Azure. Затем извлекать артефакт из реестра. Вы можете управлять множеством артефактов OCI в реестре контейнеров Azure, используя различные программы командной строки, подходящие для каждого артефакта.

## <a name="prerequisites"></a>Предварительные требования

* **Реестр контейнеров Azure.** Создайте реестр контейнеров в своей подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* **Средство Орас** . Скачайте и установите текущий выпуск Орас для вашей операционной системы из [репозитория GitHub](https://github.com/deislabs/oras/releases). Средство выпускается как сжатый tarball (`.tar.gz` файл). Извлеките и установите файл, используя стандартные процедуры для вашей операционной системы.
* **Azure Active Directory субъекта-службы (необязательно)** . для проверки подлинности непосредственно с помощью Орас создайте [субъект-службу](container-registry-auth-service-principal.md) для доступа к реестру. Убедитесь, что субъекту-службе назначена роль, например Акрпуш, чтобы она обладала разрешениями на принудительную отправку и извлечение артефактов.
* **Azure CLI (необязательно)** . чтобы использовать индивидуальное удостоверение, требуется локальная установка Azure CLI. Рекомендуется использовать версию 2.0.71 или более позднюю. Выполните `az --version `команду, чтобы найти версию. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).
* **DOCKER (необязательно)** . чтобы использовать индивидуальное удостоверение, необходимо также установить DOCKER на локальном компьютере для проверки подлинности в реестре. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в системе под управлением [macOS][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Вход в реестр

В этом разделе показаны два предлагаемых рабочего процесса для входа в реестр в зависимости от используемого удостоверения. Выберите метод, подходящий для вашей среды.

### <a name="sign-in-with-oras"></a>Вход с помощью Орас

Используя [субъект-службу](container-registry-auth-service-principal.md) с правами Push, выполните `oras login` команду, чтобы войти в реестр с помощью идентификатора и пароля приложения субъекта-службы. Укажите полное имя реестра (все строчные), в данном случае *myregistry.azurecr.IO*. Идентификатор приложения субъекта-службы передается в переменную `$SP_APP_ID`среды и пароль в переменной. `$SP_PASSWD`

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Чтобы прочитать пароль из stdin, используйте `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Вход с помощью Azure CLI

[Войдите](/cli/azure/authenticate-azure-cli) в Azure CLI с помощью удостоверения, чтобы отправить артефакты из реестра контейнеров и извлечь их из него.

Затем используйте команду Azure CLI [AZ контроля учетных записей](/cli/azure/acr?view=azure-cli-latest#az-acr-login) для доступа к реестру. Например, для проверки подлинности в реестре с именем *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`использует клиент DOCKER для установки маркера Azure Active Directory в `docker.config` файле. Для выполнения отдельного потока проверки подлинности необходимо установить и запустить клиент DOCKER.

## <a name="push-an-artifact"></a>Отправка артефакта

Создайте текстовый файл в локальном рабочем каталоге с примером текста. Например, в оболочке bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

`oras push` Используйте команду, чтобы отправить этот текстовый файл в реестр. В следующем примере текстовый файл отправляется в `samples/artifact` репозиторий. Реестр определен с полным именем реестра *myregistry.azurecr.IO* (все строчные буквы). Артефакт помечается `1.0`тегом. По умолчанию артефакт имеет неопределенный тип, который определяется строкой *типа носителя* после имени файла `artifact.txt`. Дополнительные типы см. в статье о [артефактах OCI](https://github.com/opencontainers/artifacts) . 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

Выходные данные для успешной отправки push-уведомлений похожи на следующие:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Для управления артефактами в реестре, если вы используете Azure CLI, выполните стандартные `az acr` команды для управления образами. Например, получите атрибуты артефакта с помощью команды [AZ][az-acr-repository-show] запись в репозитории:

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

Результат аналогичен приведенному ниже:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Извлечение артефакта

`oras pull` Выполните команду, чтобы извлечь артефакт из реестра.

Сначала удалите текстовый файл из локального рабочего каталога:

```bash
rm artifact.txt
```

Выполните `oras pull` команду, чтобы извлечь артефакт, и укажите тип носителя, используемый для принудительной отправки артефакта:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Убедитесь, что извлечение прошло успешно:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Удалить артефакт (необязательно)

Чтобы удалить артефакт из реестра контейнеров Azure, используйте команду AZ запись в [репозиторий удаления][az-acr-repository-delete] . Следующий пример удаляет артефакт, который вы сохранили:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о [библиотеке Орас](https://github.com/deislabs/oras/tree/master/docs), включая настройку манифеста для артефакта
* Справочные сведения о новых типах артефактов см. в репозитории [артефактов OCI](https://github.com/opencontainers/artifacts) .



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
