---
title: Docker compose рецепты контейнера
titleSuffix: Azure Cognitive Services
description: Узнайте, как развертывание нескольких контейнеров Cognitive Services. Эта процедура показано, как координировать несколько образов контейнеров Docker с помощью Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445796"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Использовать несколько контейнеров в частной сети с помощью Docker Compose

Узнайте, как развертывание нескольких контейнеров Cognitive Services. Эта процедура показано, как координировать несколько образов контейнеров Docker с помощью Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) — это средство для определения и выполнения многоконтейнерных приложений Docker. Решение Compose используется файл YAML для настройки служб приложения. Затем с помощью одной команды, создания и запуска всех служб из конфигурации.

При необходимости, выполняя несколько образов контейнеров на одном компьютере может оказаться привлекательной. В этой статье мы будем объединяют службы распознавания текста и распознаватель формы.

## <a name="prerequisites"></a>Технические условия

Для выполнения этой процедуры необходимо установить и запустить несколько средств локально.

* Используйте подписку Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.
* [Модуль Docker](https://www.docker.com/products/docker-engine), для которого нужно проверить работоспособность Docker CLI в окне консоли.
* Ресурс Azure с правильной ценовой категорией. Не все ценовые категории поддерживают этот контейнер.
  * **Компьютерное зрение** только на уровнях ресурс с F0 или стандартных цен.
  * **Форме распознаватель** только на уровнях ресурс с F0 или стандартных цен.
  * Ресурс **Cognitive Services** с ценовой категорией S0.

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Заполнить и отправить [форму запроса контейнеры речи Cognitive Services](https://aka.ms/speechcontainerspreview/) чтобы запросить доступ к контейнеру. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Файл docker compose

Файл YAML определяет все службы должны быть развернуты. Эти службы используют либо `DockerFile` или имеющийся образ контейнера, в данном случае мы будем использовать два изображения предварительного просмотра. Скопируйте и вставьте следующий yaml-файл и сохраните его как *docker-compose.yaml*. Обеспечивает соответствующее _apikey_, _выставления счетов_, и _URI конечной точки_ значения в _docker-compose.yml_ ниже файла.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Создайте каталоги на хост-компьютере, которые указаны в разделе `volumes` узла. Это необходимо, как каталоги должен существовать прежде чем подключение ISO-образа с привязками тома.

## <a name="start-the-configured-docker-compose-services"></a>Начало настроенных docker compose служб

Docker compose файл включает управление все определенные службы жизненного цикла; Запуск и остановку и перестроение служб, просмотр состояния службы и потоковая передача журналов. Откройте интерфейс командной строки из каталога проекта (где *docker-compose.yaml* файл документа).

> [!NOTE]
> Чтобы избежать ошибок, убедитесь, что хост-компьютере правильно совместное использование дисков с помощью **подсистема Docker**. Например если *e:\publicpreview* используется в качестве каталога в *docker-compose.yaml* совместно использовать *диска E* с помощью docker.

Из интерфейса командной строки, выполните следующую команду для запуска (или перезапуска) всех служб, определенных в *docker-compose.yaml*:

```console
docker-compose up
```

Первого выполнения время `docker-compose up` команду с этой конфигурацией **Docker** будет извлекать образы, настроенные в разделе `services` узла — Загрузка/подключение их:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Изображения загружаются, то изображение службы запущены.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Проверьте доступность службы

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Ниже приведен пример вывода:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Проверка контейнера текста recognize

Откройте браузер на хост-компьютере и перейдите к `localhost` с указанный порт из *docker-compose.yaml*, например `http://localhost:5021/swagger/index.html`. Можно использовать Try, его функции API-интерфейса для проверки конечной точки текста recognize.

![Распознавание текста Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Проверка контейнера распознаватель формы

Откройте браузер на хост-компьютере и перейдите к `localhost` с указанный порт из *docker-compose.yaml*, например `http://localhost:5010/swagger/index.html`. Можно использовать Try, его функции API-интерфейса для проверки конечной точки распознаватель формы.

![Форма распознаватель Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Контейнеры Cognitive Services](../cognitive-services-container-support.md)