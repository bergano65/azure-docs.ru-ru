---
title: Рецепты для контейнеров Docker
titleSuffix: Azure Cognitive Services
description: Используйте этими рецептами контейнеров для создания Cognitive Services контейнеры, можно использовать повторно. Контейнеры могут быть построены с некоторые или все параметры конфигурации, таким образом, чтобы они не нужны при запуске контейнера. Когда имеется новый слой контейнера (с параметрами), и после проверки локально, можно хранить контейнера в реестре контейнеров. При запуске контейнера, его потребуется только те параметры, которые в настоящее время не хранятся в контейнере.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8a9d0d9f35c30636f4b9150c63b64098071e12e9
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271488"
---
# <a name="create-containers-for-reuse"></a>Создание контейнеров для повторного использования

Используйте этими рецептами контейнеров для создания Cognitive Services контейнеры, можно использовать повторно. Контейнеры могут быть построены с некоторые или все параметры конфигурации, так что они _не_ необходимы при запуске контейнера.

Когда имеется новый слой контейнера (с параметрами), и после проверки локально, можно хранить контейнера в реестре контейнеров. При запуске контейнера, его потребуется только те параметры, которые в настоящее время не хранятся в контейнере. Частный реестр контейнеров поле конфигурации можно передать эти параметры на.

## <a name="docker-run-syntax"></a>Синтаксис "run" docker

Любой `docker run` примерах в этом документе предполагается консоли Windows с `^` символ продолжения строки. Учитывайте следующее в собственных целях.

* Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.
* Если используется операционная система Windows или консоли, отличного от консоли Windows, используйте правильный консоли/терминалов, синтаксис папку для подключений, а символ продолжения строки для консоли и системы.  Так как контейнер Cognitive Services — это операционная система Linux, целевой объект подключения использует синтаксис папку стиле Linux.
* `docker run` Примеры использования каталога `c:` диска, чтобы избежать конфликтов разрешение на Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker.

## <a name="store-no-configuration-settings-in-image"></a>Store никакой дополнительной настройки в образ

Пример `docker run` команд для каждой службы не сохраняются все параметры конфигурации в контейнере. Когда вы запускаете контейнер из службы консоли или реестра, эти параметры конфигурации необходимо передать. Частный реестр контейнеров поле конфигурации можно передать эти параметры на.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Повторно использовать рецепта: сохранить все параметры конфигурации с контейнером

Чтобы сохранить все параметры конфигурации, создать `Dockerfile` с этими параметрами.

Проблемы при таком подходе:

* Новый контейнер имеет отдельное имя и тег из исходного контейнера.
* Чтобы изменить эти параметры, необходимо изменить значения по Dockerfile, перестройка образа и повторно опубликовать в реестр.
* Если кто-нибудь получит доступ к реестру контейнеров или локального узла, их можно запустить контейнер и использовать конечные точки по Cognitive Services.
* Если службе Cognitive не требует ввода подключение, не добавляйте `COPY` строки в dockerfile.

Создайте файл Dockerfile, извлечение из существующий контейнер Cognitive Services, который вы хотите использовать, а затем использовать команды docker в Dockerfile для задания или извлечения сведений о контейнера требуется.

Этот пример.

* Задает конечную точку выставления счетов, `{BILLING_ENDPOINT}` с узла используя ключ среды `ENV`.
* Наборы выставления счетов ключ API, `{ENDPOINT_KEY}` с узла используя ключ среды "ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Повторно использовать рецепта: хранения параметров выставления счетов с контейнером

В этом примере показано, как создать контейнер тональности текстовой аналитики из файла Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Сборка и запуск контейнера [локально](#how-to-use-container-on-your-local-host) или от вашего [частного реестра контейнеров](#how-to-add-container-to-private-registry) при необходимости.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Повторно использовать рецепта: хранения выставления счетов и параметры с контейнером подключения

В этом примере показано, как использовать Language Understanding, сохранение выставления счетов и моделей из файла Dockerfile.

* Копирует файл модели Language Understanding (LUIS) на узле файловой системы с помощью `COPY`.
* LUIS контейнеров поддерживает более одной модели. Если все модели хранятся в той же папке, все требуется один `COPY` инструкции.
* Запустите файл docker из относительного родительского узла входного каталога модели. В следующем примере, запустите `docker build` и `docker run` команды из относительного родительского узла `/input`. Первый `/input` на `COPY` команда является каталогом главного компьютера. Второй `/input` — каталог контейнера.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Сборка и запуск контейнера [локально](#how-to-use-container-on-your-local-host) или от вашего [частного реестра контейнеров](#how-to-add-container-to-private-registry) при необходимости.

## <a name="how-to-use-container-on-your-local-host"></a>Как использовать контейнер на локальном узле

Чтобы создать файл Docker, замените `<your-image-name>` с новым именем образа, используйте:

```console
docker build -t <your-image-name> .
```

Чтобы запустить образ и удалите его после остановки контейнера (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Как добавить в частный реестр контейнеров

Выполните следующие действия для использования Dockerfile и разместите новый образ в закрытом реестре контейнеров.  

1. Создание `Dockerfile` с текстом из рецепта повторного использования. Объект `Dockerfile` не имеет расширения.

1. Замените все значения в угловых скобках собственными значениями.

1. Поместить файл в образ, в командной строке или терминале, выполнив следующую команду. Замените значения в угловых скобках, `<>`, с именем контейнера и тега.  

    Параметр тега `-t`, — это способ добавления сведений о были изменены для контейнера. Например, имя контейнера из `modified-LUIS` указывает, были многоуровневой исходного контейнера. Имя тега для `with-billing-and-model` показывает, как был изменен в контейнер Language Understanding (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Войдите в Azure CLI с помощью консоли. Эта команда открывает в браузере и требует проверки подлинности. После прохождения проверки подлинности, можно закрыть браузер и продолжить работу в консоли.

    ```azure-cli
    az login
    ```

1. Войдите в закрытый реестр с помощью Azure CLI с помощью консоли.

    Замените значения в угловых скобках, `<my-registry>`, именем своего реестра.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Вы можете также войти со учетными данными для docker при назначении субъекта-службы.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Тег контейнер с помощью частного реестра. Замените значения в угловых скобках, `<my-registry>`, именем своего реестра. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Если вы не используете имени тега `latest` подразумевается.

1. Отправка нового образа в частный реестр контейнеров. При просмотре в частный реестр контейнеров, имя контейнера, используемого в следующую команду интерфейса командной строки будет использоваться имя репозитория.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание и использование экземпляра контейнера Azure](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->