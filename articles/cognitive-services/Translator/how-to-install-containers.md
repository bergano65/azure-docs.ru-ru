---
title: Установка и запуск контейнеров — Перевод текстов
titleSuffix: Azure Cognitive Services
description: Как скачать, установить и запустить контейнеры для Перевод текстов Analytics в этом пошаговом руководстве.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7b2b07f71d00e6da66062d1826f8c5c88bb6be7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507883"
---
# <a name="install-and-run-translator-text-containers"></a>Установка и запуск контейнеров Перевод текстов

<!--
    ACOM forward link:
    https://docs.microsoft.com/azure/cognitive-services/translator/howto-install-containers
-->

Контейнеры позволяют запускать Перевод текстов API в собственной среде и прекрасно подходят для конкретных требований к безопасности и управлению данными.

## <a name="prerequisites"></a>Технические условия

Перед использованием Перевод текстов контейнеров необходимо выполнить следующие предварительные требования:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). См. [общие сведения о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.|

## <a name="request-access-to-the-container-registry"></a>Запрос доступа к реестру контейнеров

Чтобы запросить доступ к контейнеру, необходимо сначала завершить и отправить [форму запроса перевод текстовных контейнеров](https://aka.ms/translatorcontainerform) .

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

### <a name="the-host-computer"></a>Главный компьютер

Узел — это компьютер на базе x64 с ОС Linux, на которой выполняется контейнер DOCKER. Это может быть компьютер в локальной среде или служба размещения Docker в Azure, включая следующие решения:

* Служба Kubernetes Azure.
* Службы "экземпляры контейнеров Azure".
* Кластер [Kubernetes](https://kubernetes.io/) , развернутый в Azure Stack.

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и Рекомендуемые ядра ЦП (не менее 2,6 ГГц) или быстрее, а также память в гигабайтах (ГБ), выделяемая для каждого контейнера Перевод текстов.

| Контейнер | Минимальная | Языковая пара |
|-----------|---------|---------------|
| Перевод текста | 4 ядра, 4 ГБ памяти | 4\. |

Для каждой пары языков рекомендуется использовать 1 ГБ памяти. По умолчанию контейнер Перевод текстов содержит 3 или 4 пары языков в зависимости от выполняемой `<image-tag>`. Дополнительные сведения см. в разделе [Поддерживаемые языки и перевод](#supported-languages-and-translation) . Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются в составе команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Образы контейнеров для Перевод текстов доступны в следующем репозитории контейнеров. Таблица также сопоставляет Теги образа контейнера с соответствующими поддерживаемыми языками.

| Контейнер | Тег Image | Поддерживаемые языки |
|-----------|-----------|---------------------|
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `ar_de_en_ru_zh_1.0.0` | `ar-SA`, `zh-CN`, `de-DE` и `ru-RU` |
| `containerpreview.azurecr.io/microsoft/cognitive-services-translator-text` | `de_en_es_fr_1.0.0` | `de-DE`, `fr-FR` и `es-ES`. |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-translator-text-container"></a>Извлечение DOCKER для контейнера Перевод текстов

Чтобы выполнить команду [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) , сначала необходимо получить доступ к реестру контейнеров. В Azure CLI можно войти в реестр контейнеров Azure с помощью команды [`az acr login`](https://docs.microsoft.com/cli/azure/acr?view=azure-cli-latest#az-acr-login) .

```azureinteractive
az acr login --name containerpreview.azurecr.io
```

Эта команда выполнит проверку подлинности текущего пользователя в соответствующем реестре контейнеров Azure. Теперь вы можете выполнить команду `docker pull`.

> [!NOTE]
> В зависимости от того, какая языковая поддержка вам нужна, укажите соответствующие `<image-tag>`.

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:<image-tag>
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run). Доступны дополнительные [примеры](translator-text-container-config.md#example-docker-run-commands) команды `docker run`.
1. [Запросите конечную точку преобразования контейнера](#query-the-containers-translate-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. Доступны [примеры](translator-text-container-config.md#example-docker-run-commands) команды `docker run`.

### <a name="run-container-example-of-docker-run-command"></a>Пример запуска контейнера команды запуска DOCKER

```Docker
docker run --rm -it -p 5000:80 --memory 4g --cpus 4 \
containerpreview.azurecr.io/microsoft/cognitive-services-translator-text:ar_de_en_ru_zh_1.0.0 \
Eula=accept
```

Эта команда:

* Запускает контейнер Перевод текстов из образа контейнера.
* Выделяет 4 ядра ЦП и 4 гигабайт (ГБ) памяти
* Предоставление TCP-порта 5000 и выделение псевдотелетайпа для контейнера.
* Принимает соглашение конечного пользователя (EULA)
* автоматически удаляет контейнер после завершения его работы. Образ контейнера по-прежнему доступен на главном компьютере

### <a name="how-to-collect-docker-logs"></a>Как получить журналы DOCKER

В целях устранения неполадок может быть полезно просмотреть журналы DOCKER из выполнения контейнера. Сначала выполните команду [DOCKER PS](https://docs.docker.com/engine/reference/commandline/ps/) с флагом форматирования, чтобы ограничить сведения, отображаемые для всех контейнеров.

```Docker
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.Status}}"

CONTAINER ID    IMAGE                                                                       STATUS
ed6f115697f3    containerpreview.azurecr.io/microsoft/cognitive-services-translator-text    Up 4 minutes
```

Затем выполните команду [DOCKER Logs](https://docs.docker.com/engine/reference/commandline/logs/) с `<Container ID>` для соответствующего контейнера, чтобы просмотреть его журналы.

```Docker
docker logs <Container ID> --timestamps --since=4h | grep Error
```

В приведенной выше команде DOCKER Logs будут собраны журналы ошибок за последние четыре часа.

## <a name="supported-languages-and-translation"></a>Поддерживаемые языки и перевод

Метод `POST /translate` поддерживает следующие языки преобразования: переход с *английского* на целевой язык и наоборот. Обратите внимание, что хотя вы можете переходить на английский язык с помощью одного из перечисленных языков, вы *не сможете* переходить с одного языка на *другой, отличный* *от английского.*

> [!NOTE]
> Для оптимального качества потребители должны отправить только одно предложение на запрос.

| Преобразование языка | Преобразование языка ISO | Теги изображений |
|--|--|:--|
| Английский: left_right_arrow: Китайский | `en-US`: left_right_arrow: `zh-CN` | `ar_de_en_ru_zh_1.0.0` |
| Английский: left_right_arrow: Русский | `en-US`: left_right_arrow: `ru-RU` | `ar_de_en_ru_zh_1.0.0` |
| Английский: left_right_arrow: Арабский | `en-US`: left_right_arrow: `ar-SA` | `ar_de_en_ru_zh_1.0.0` |
| Английский: left_right_arrow: Немецкий | `en-US`: left_right_arrow: `de-DE` | `ar_de_en_ru_zh_1.0.0` и `de_en_es_fr_1.0.0` |
| Английский: left_right_arrow: Испанский | `en-US`: left_right_arrow: `es-ES` | `de_en_es_fr_1.0.0` |
| Английский: left_right_arrow: французский | `en-US`: left_right_arrow: `fr-FR` | `de_en_es_fr_1.0.0` |

> [!IMPORTANT]
> Для запуска контейнера необходимо указать `Eula`. в противном случае контейнер не запускается.

## <a name="query-the-containers-translate-endpoint"></a>Запрос конечной точки преобразования контейнера

Контейнер предоставляет API конечной точки перевода на основе RESTFUL. Ниже приведены несколько примеров использования этой конечной точки.

# <a name="curltabcurl"></a>[cURL](#tab/curl)


Выполните следующую команду в виде фигурной команды из нужной CLI.

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en-US&to=de-DE"
    -H "Content-Type: application/json" -d "[{'Text':'hello, how are you'}]"
```

> [!TIP]
> Если вы попытаетесь отправить сообщение в виде парной записи перед тем, как контейнер будет готов, вы получите ответ "служба временно недоступна". Просто дождитесь готовности контейнера и повторите попытку.

На консоль будут выведены следующие выходные данные.

```console
"translations": [
    {
        "text": "hallo, wie geht es dir",
        "to": "de-DE"
    }
]
```

# <a name="swaggertabswagger"></a>[Swagger](#tab/Swagger)

Перейдите на страницу Swagger http://localhost:5000/swagger/index.html

1. Выберите **POST/транслате**
1. Выберите **попробовать**
1. Введите параметр **from** в качестве `en-US`
1. Введите в **параметре значение `de-DE`**
1. Введите параметр **API-Version** как `3.0`
1. В разделе **тексты**замените `string` на следующий JSON.
    ```json
    [
        {
            "text": "hello, how are you"
        }
    ]
    ```
1. Выберите **выполнить**, в результате выходные данные будут выведены в **тексте ответа**. Следует рассчитывать примерно следующее:
    ```json
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

# <a name="net-coretabnetcore"></a>[.NET Core](#tab/netcore)

1. Запустите Visual Studio и создайте новое консольное приложение.
1. Измените файл `*.csproj`, чтобы добавить `<LangVersion>7.1</LangVersion>` узел — в этом случае C# указывается 7,1.
1. Добавьте пакет NuGet [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json/) версии 11.0.2.
1. В `Program.cs`замените весь существующий код следующим:
    ```csharp
    using Newtonsoft.Json;
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    namespace TranslateContainer
    {
        class Program
        {
            const string ApiHostEndpoint = "http://localhost:5000";
            const string TranslateApi = "/translate?api-version=3.0&from=en-US&to=de-DE";

            static async Task Main(string[] args)
            {
                var textToTranslate = "hello, how are you";
                var result = await TranslateTextAsync(textToTranslate);

                Console.WriteLine(result);
                Console.ReadLine();
            }

            static async Task<string> TranslateTextAsync(string textToTranslate)
            {
                var body = new object[] { new { Text = textToTranslate } };
                var requestBody = JsonConvert.SerializeObject(body);

                var client = new HttpClient();
                using (var request =
                    new HttpRequestMessage
                    {
                        Method = HttpMethod.Post,
                        RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                        Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                    })
                {
                    // Send the request and await a response.
                    var response = await client.SendAsync(request);

                    return await response.Content.ReadAsStringAsync();
                }
            }
        }
    }
    ```
1. Нажмите клавишу **F5** для запуска программы.
1. На консоль будут выведены следующие выходные данные.
    ```console
    "translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de-DE"
      }
    ]
    ```

***

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](translator-text-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для загрузки, установки и запуска контейнеров Перевод текстов. Краткая сводка.

* Перевод текстов предоставляет несколько контейнеров Linux для DOCKER, инкапсулирующий различные пары языков.
* Образы контейнеров загружаются из реестра предварительной версии контейнера.
* Образы контейнеров, которые выполняются в Docker.
* Вы можете использовать REST API или пакет SDK для вызова операций в контейнерах Перевод текстов, указав URI узла контейнера.

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [параметрах конфигурации](translator-text-container-config.md).
* Чтобы устранить проблемы, связанные с функциональностью, см. раздел часто задаваемые [вопросы о контейнерах](../containers/container-faq.md) .
