---
title: Рецепты контейнеров Docker
titleSuffix: Azure Cognitive Services
description: Узнайте, как создавать, тестировать и хранить контейнеры с некоторыми или всеми настройками конфигурации для развертывания и повторного использования.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 97342f1dd4f6ce343626ba6c294f09dabe3db5c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717200"
---
# <a name="create-containers-for-reuse"></a>Создание контейнеров для повторного использования

Используйте эти рецепты контейнеров для создания контейнеров Cognitive Services, которые могут быть использованы повторно. Контейнеры могут быть построены с некоторыми или всеми настройками конфигурации, так что они _не_ нужны при начале запуска контейнера.

После того как у вас есть этот новый слой контейнера (с настройками), и вы проверили его локально, вы можете хранить контейнер в контейнере реестра. Когда контейнер запускается, ему понадобятся только те параметры, которые в настоящее время не хранятся в контейнере. Частный контейнер реестра предоставляет пространство конфигурации для вас, чтобы пройти эти настройки дюйма

## <a name="docker-run-syntax"></a>Докер запустить синтаксис

Любые `docker run` примеры в этом документе предполагают консоль Windows с символом `^` продолжения строки. Рассмотрим следующее для собственного использования:

* Не изменяйте порядок аргументов, если вы не являетесь уверенным пользователем контейнеров Docker.
* Если вы используете операционную систему, кроме Windows, или консоль, кроме консоли Windows, используйте правильную консоль/терминал, синтаксис папок для креплений и символ продолжения строки для консоли и системы.  Поскольку контейнер Cognitive Services — это операционная система Linux, целевое крепление использует синтаксис в стиле папки в стиле Linux.
* `docker run`примеры используют каталог `c:` с диска, чтобы избежать конфликтов разрешений в Windows. Если вам нужен конкретный каталог для входных данных, может потребоваться предоставить соответствующие разрешения службе Docker.

## <a name="store-no-configuration-settings-in-image"></a>Хранение настройки конфигурации в изображении не

В `docker run` примере команды для каждой службы не хранят в контейнере настройки конфигурации. При запуске контейнера с консоли или службы регистрации эти настройки конфигурации должны быть пройдены. Частный контейнер реестра предоставляет пространство конфигурации для вас, чтобы пройти эти настройки дюйма

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Рецепт повторного использования: храните все настройки конфигурации с помощью контейнера

Для того, чтобы сохранить все `Dockerfile` настройки конфигурации, создайте с этими настройками.

Проблемы с этим подходом:

* Новый контейнер имеет отдельное название и тег от исходного контейнера.
* Для того, чтобы изменить эти настройки, вам придется изменить значения Dockerfile, восстановить изображение и переиздать в реестре.
* Если кто-то получает доступ к вашему реестру контейнеров или локальному хостам, он может запустить контейнер и использовать конечные точки Cognitive Services.
* Если ваша когнитивная служба не требует ввода, не `COPY` добавляйте строки в dockerfile.

Создайте Dockerfile, потянув из существующего контейнера Когнитивные Услуги вы хотите использовать, а затем использовать докер команды в Dockerfile установить или тянуть в информации контейнер анимируется.

В этом примере:

* Устанавливает конечную точку `{BILLING_ENDPOINT}` выставления счетов с `ENV`помощью ключа среды хоста.
* Устанавливает API-ключ выставления счетов `{ENDPOINT_KEY}` из ключа среды хоста с помощью 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Рецепт повторного использования: настройки выставления счетов с контейнером

В этом примере показано, как создать контейнер настроений text Analytics из Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Создавайте и запускайте контейнер [локально](#how-to-use-container-on-your-local-host) или из [частного реестра контейнера](#how-to-add-container-to-private-registry) по мере необходимости.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Рецепт повторного использования: настройки выставления счетов и установки с контейнером

В этом примере показано, как использовать понимание языка, сохранение биллинга и моделей из Dockerfile.

* Копирует файл модели Language Understanding (LUIS) из файловой системы хоста с помощью. `COPY`
* Контейнер LUIS поддерживает более одной модели. Если все модели хранятся в одной папке, всем требуется одна `COPY` выписка.
* Выполнить файл докера из относительного родительского каталога ввода модели. В следующем примере `docker build` запустите и `docker run` команды от `/input`относительного родителя . Первым `/input` в `COPY` команде является каталог хостатного компьютера. Во-вторых, `/input` каталог контейнера.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Создавайте и запускайте контейнер [локально](#how-to-use-container-on-your-local-host) или из [частного реестра контейнера](#how-to-add-container-to-private-registry) по мере необходимости.

## <a name="how-to-use-container-on-your-local-host"></a>Как использовать контейнер на локальном узлах

Чтобы создать файл Docker, замените `<your-image-name>` новое имя изображения, затем используйте:

```console
docker build -t <your-image-name> .
```

Для запуска изображения и удалить его,`--rm`когда контейнер останавливается ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Как добавить контейнер в частный реестр

Выполните следующие действия, чтобы использовать Dockerfile и поместить новое изображение в свой частный реестр контейнеров.  

1. Создайте `Dockerfile` текст из рецепта повторного использования. У `Dockerfile` A нет расширения.

1. Замените любые значения в угловых скобках своими собственными значениями.

1. Создайте файл в изображение на командной строке или терминале, используя следующую команду. Замените значения в угловых `<>`скобках, с вашим собственным именем контейнера и тегом.  

    Параметр тегов, `-t`это способ добавить информацию о том, что вы изменили для контейнера. Например, имя контейнера `modified-LUIS` указывает на то, что исходный контейнер был слоистым. Название тега `with-billing-and-model` показывает, как был изменен контейнер Language Understanding (LUIS).

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Воссоединиться с Azure CLI с консоли. Эта команда открывает браузер и требует проверки подлинности. После проверки подлинности можно закрыть браузер и продолжить работу в консоли.

    ```azurecli
    az login
    ```

1. Войти в свой частный реестр с Azure CLI с консоли.

    Замените значения в угловых `<my-registry>`скобках, с вашим собственным именем реестра.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Вы также можете войти в систему с помощью входа в docker, если вам назначен директор службы.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Отметьте контейнер с местом расположения частного реестра. Замените значения в угловых `<my-registry>`скобках, с вашим собственным именем реестра. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Если вы не используете имя `latest` тега, подразумевается.

1. Нажмите новое изображение в свой частный реестр контейнеров. При просмотре вашего частного реестра контейнеров имя контейнера, используемое в следующей команде CLI, будет названием репозитория.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание и использование экземпляра контейнеров Azure](azure-container-instance-recipe.md)

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