---
title: Нажмите и потяните артефакт OCI
description: Нажмите и вытяните артефакты Open Container Initiative (OCI) с помощью частного реестра контейнеров в Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371058"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Нажмите и вытяните артефакт OCI с помощью реестра контейнеров Azure

Для хранения и управления [артефактами Open Container Initiative (OCI),](container-registry-image-formats.md#oci-artifacts) а также изображения контейнеров, совместимых с Docker и Docker, можно использовать реестр контейнеров Azure.

Чтобы продемонстрировать эту возможность, в этой статье показано, как использовать реестр OCI в качестве инструмента [хранения (ORAS)](https://github.com/deislabs/oras) для нажатия артефакта образца - текстового файла - в реестр контейнеров Azure. Затем вытащите артефакт из реестра. Вы можете управлять различными артефактами OCI в реестре контейнеров Azure, используя различные инструменты командной строки, подходящие для каждого артефакта.

## <a name="prerequisites"></a>Предварительные требования

* **Реестр контейнеров Azure** - Создание реестра контейнеров в подписке Azure. Это можно сделать на [портале Azure](container-registry-get-started-portal.md) или с помощью [Azure CLI](container-registry-get-started-azure-cli.md).
* **Инструмент ORAS** - Скачать и установить текущий релиз ORAS для операционной системы из [репо GitHub](https://github.com/deislabs/oras/releases). Инструмент выпускается в виде сжатого tarball (файл).`.tar.gz` Извлеките и установите файл с помощью стандартных процедур для операционной системы.
* **Основной директор службы Azure Active Directory (необязательно)** - Чтобы проверить подлинность непосредственно с ORAS, создайте [принцип службы](container-registry-auth-service-principal.md) для доступа к вашему реестру. Убедитесь, что директору службы присваивается такая роль, как AcrPush, чтобы у него были разрешения на нажатие и вытягивание артефактов.
* **Azure CLI (необязательно)** - Для использования индивидуальной идентификации необходима локальная установка Azure CLI. Рекомендуется версия 2.0.71 или позже. Бегите, `az --version `чтобы найти версию. Если вам нужно установить или обновить, [см.](/cli/azure/install-azure-cli)
* **Docker (необязательно)** - Для использования индивидуального удостоверения личности, вы также должны иметь Docker установлен локально, для проверки подлинности с реестром. Docker предоставляет пакеты, которые позволяют быстро настроить Docker в любой системе: [macOS][docker-mac], [Windows][docker-windows] или [Linux][docker-linux].


## <a name="sign-in-to-a-registry"></a>Войти в реестр

В этом разделе показаны два предлагаемых рабочих процесса для включения в реестр, в зависимости от используемой идентификации. Выберите метод, подходящий для вашей среды.

### <a name="sign-in-with-oras"></a>Вопиюте с ORAS

Используя [директор службы](container-registry-auth-service-principal.md) с правами push, запустите `oras login` команду для вступления в реестр, используя идентификатор основного приложения службы и пароль. Укажите полностью квалифицированное название реестра (все нижние), в данном случае *myregistry.azurecr.io*. Идентификатор основного приложения `$SP_APP_ID`службы передается в `$SP_PASSWD`переменной среды, а пароль в переменной .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Чтобы прочитать пароль от Stdin, используйте `--password-stdin`.

### <a name="sign-in-with-azure-cli"></a>Вход с помощью Azure CLI

[Вопийте в](/cli/azure/authenticate-azure-cli) Azure CLI с вашей личностью, чтобы выталкивать и вытаскивать артефакты из реестра контейнеров.

Затем используйте команду Azure CLI [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) для доступа к реестру. Например, для проверки подлинности *myregistry*в реестр ежерегистр:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`использует клиент Docker для установки маркера Active `docker.config` Directory Azure в файле. Клиент Docker должен быть установлен и запущен для завершения индивидуального потока аутентификации.

## <a name="push-an-artifact"></a>Нажмите артефакт

Создайте текстовый файл в локальном рабочем рабочем каталоге с некоторым примером текста. Например, в оболочке Bash:

```bash
echo "Here is an artifact!" > artifact.txt
```

Используйте `oras push` команду, чтобы продвинуть этот текстовый файл в свой реестр. Следующий пример толкает пример текстового файла к `samples/artifact` репо. Реестр идентифицируется с полностью квалифицированным названием реестра *myregistry.azurecr.io* (все нижние регистры). Артефакт `1.0`помечен. Артефакт имеет неопределенный тип, по умолчанию идентифицированный строкой `artifact.txt` *типа мультимедиа* после имени файла. См [артефакты OCI](https://github.com/opencontainers/artifacts) для дополнительных типов. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Выход для успешного толчка аналогичен следующему:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Для управления артефактами в реестре, если вы используете `az acr` Azure CLI, запустите стандартные команды для управления изображениями. Например, получить атрибуты артефакта с помощью [команды-репозитория az acr:][az-acr-repository-show]

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

## <a name="pull-an-artifact"></a>Вытяните артефакт

Запустите `oras pull` команду, чтобы вытащить артефакт из реестра.

Сначала удалите текстовый файл из локального рабочего каталога:

```bash
rm artifact.txt
```

Выполнить, `oras pull` чтобы вытащить артефакт, и указать тип мультимедиа, используемый для нажатия артефакта:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Убедитесь, что притяжение было успешным:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Удалите артефакт (необязательно)

Чтобы удалить артефакт из реестра контейнеров Azure, используйте команду [удаления репозитория az acr.][az-acr-repository-delete] Следующий пример удаляет артефакт, который вы там хранили:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [библиотеке ORAS,](https://github.com/deislabs/oras/tree/master/docs)в том числе о том, как настроить манифест для артефакта
* Посетите РЕПО [OCI Artifacts](https://github.com/opencontainers/artifacts) для получения справочной информации о новых типах артефактов



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
