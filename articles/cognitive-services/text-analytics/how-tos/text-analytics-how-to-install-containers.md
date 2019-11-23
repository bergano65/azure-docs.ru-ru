---
title: Install and run containers - Text Analytics
titleSuffix: Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры для Анализа текста.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 6e05dc2136211bcd15a9f0583358b05ccbf96f5a
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383163"
---
# <a name="install-and-run-text-analytics-containers"></a>Установка и запуск контейнеров API анализа текста

Containers enable you to run the Text Analytic APIs in your own environment and are great for your specific security and data governance requirements. The Text Analytics containers provide advanced natural language processing over raw text, and include three main functions: sentiment analysis, key phrase extraction, and language detection. Связывание сущностей в настоящее время не поддерживается в контейнере.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

To run any of the Text Analytics containers, you must have the host computer and container environments.

## <a name="preparation"></a>Подготовка

Прежде чем использовать контейнеры Анализа текста, необходимо выполнить следующие условия:

|Обязательно для заполнения|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс API анализа текста |Для использования контейнера необходимо следующее:<br><br>An Azure [Text Analytics resource](../../cognitive-services-apis-create-account.md) to get the associated API key and endpoint URI. Оба значения доступны на страницах "Обзор" и "Ключи" API анализа текста на портале Azure и необходимы для запуска контейнера.<br><br>**{API_KEY}** : One of the two available resource keys on the **Keys** page<br><br>**{ENDPOINT_URI}** : The endpoint as provided on the **Overview** page|

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и рекомендуемые требования к ЦП (минимум 2,6 ГГц или быстрее) и памяти, в ГБ, для выделения для каждого контейнера Анализа текста.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-requirements](../includes/key-phrase-extraction-container-requirements.md)]

# <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [language-detection-container-requirements](../includes/language-detection-container-requirements.md)]

# <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-requirements](../includes/sentiment-analysis-container-requirements.md)]

***

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Container images for Text Analytics are available on the Microsoft Container Registry.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [key-phrase-extraction-container-repository](../includes/key-phrase-extraction-container-repository.md)]

# <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [language-detection-container-repository](../includes/language-detection-container-repository.md)]

# <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment)

[!INCLUDE [sentiment-analysis-container-repository](../includes/sentiment-analysis-container-repository.md)]

***

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-text-analytics-containers"></a>Docker pull for the Text Analytics containers

# <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [docker-pull-key-phrase-extraction-container](../includes/docker-pull-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [docker-pull-language-detection-container](../includes/docker-pull-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment)

[!INCLUDE [docker-pull-sentiment-analysis-container](../includes/docker-pull-sentiment-analysis-container.md)]

***

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](../text-analytics-resource-container-config.md#example-docker-run-commands) команды `docker run`.
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. Refer to [Gathering required parameters](#gathering-required-parameters) for details on how to get the `{ENDPOINT_URI}` and `{API_KEY}` values.

[Examples](../text-analytics-resource-container-config.md#example-docker-run-commands) of the `docker run` command are available.

# <a name="key-phrase-extractiontabkeyphrase"></a>[Извлечение ключевых фраз](#tab/keyphrase)

[!INCLUDE [docker-run-key-phrase-extraction-container](../includes/docker-run-key-phrase-extraction-container.md)]

# <a name="language-detectiontablanguage"></a>[Распознавание языка](#tab/language).

[!INCLUDE [docker-run-language-detection-container](../includes/docker-run-language-detection-container.md)]

# <a name="sentiment-analysistabsentiment"></a>[Анализ тональности](#tab/sentiment)

[!INCLUDE [docker-run-sentiment-analysis-container](../includes/docker-run-sentiment-analysis-container.md)]

***

> [!IMPORTANT]
> Для запуска контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST.

Используйте узел `http://localhost:5000` для API контейнера.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неисправностей

Если контейнер запускается с выходным [подключением](../text-analytics-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера.

[!INCLUDE [Cognitive Services FAQ note](../../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры API анализа текста отправляют данные для выставления счетов в Azure с помощью ресурса _API анализа текста_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в разделе [Настройка контейнеров](../text-analytics-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Резюме

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Анализа текста. В разделе "Сводка" сделайте следующее.

* Text Analytics provides three Linux containers for Docker, encapsulating various capabilities:
   * *Извлечение ключевых фраз*
   * *Распознавание языка*.
   * *Анализ тональности*
* Образы контейнеров скачиваются из Реестра контейнеров (Майкрософт) в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, пакет SDK или REST API можно использовать для вызова операций в контейнерах Анализа текста.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](../text-analytics-resource-container-config.md).
* Чтобы решить проблемы, связанные с функциональностью, ознакомьтесь с [часто задаваемыми вопросами](../text-analytics-resource-faq.md).
