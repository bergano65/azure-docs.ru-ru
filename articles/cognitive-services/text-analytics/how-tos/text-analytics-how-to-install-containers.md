---
title: Установка и запуск контейнеров
titleSuffix: Text Analytics -  Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры для Анализа текста.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 07fbf51f584d691b08d94f68fefa8c3f9348227f
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55294834"
---
# <a name="install-and-run-text-analytics-containers"></a>Установка и запуск контейнеров API анализа текста

Контейнеры API анализа текста предоставляют расширенную обработку необработанного текста на естественных языках и включают в себя три основные функции: анализ тональности, извлечение ключевых фраз и определение языка. Связывание сущностей в настоящее время не поддерживается в контейнере. 

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для запуска контейнеров API анализа текста вам понадобится следующее.

## <a name="preparation"></a>Подготовка

Прежде чем использовать контейнеры Анализа текста, необходимо выполнить следующие условия:

|Обязательно|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс API анализа текста |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure [_API анализа текста_](text-analytics-how-to-access-key.md), чтобы получить связанный ключ выставления счетов и URI конечной точки выставления счетов. Оба значения доступны на страницах "Обзор" и "Ключи" API анализа текста на портале Azure и необходимы для запуска контейнера.<br><br>**{BILLING_KEY}**: ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}**: пример URI конечной точки: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0`|

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Request access to private preview](../../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальные и рекомендуемые требования к ЦП (минимум 2,6 ГГц или быстрее) и памяти, в ГБ, для выделения для каждого контейнера Анализа текста.

| Контейнер | Минимальная | Рекомендуется |
|-----------|---------|-------------|
|Извлечение ключевой фразы | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |
|Распознавание языка | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |
|Анализ тональности | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Образы контейнеров для Анализа текста доступны в Реестре контейнеров Майкрософт. 

| Контейнер | Репозиторий |
|-----------|------------|
|Извлечение ключевой фразы | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Распознавание языка | `mcr.microsoft.com/azure-cognitive-services/language` |
|Анализ тональности | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера из реестра контейнеров Microsoft.

Полное описание доступных тегов для контейнеров Анализа текста см. в следующих контейнерах в центре Docker:

* [Извлечение ключевых фраз](https://go.microsoft.com/fwlink/?linkid=2018757).
* [Распознавание языка](https://go.microsoft.com/fwlink/?linkid=2018759).
* [Анализ тональности](https://go.microsoft.com/fwlink/?linkid=2018654).

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.


### <a name="docker-pull-for-the-key-phrase-extraction-container"></a>docker pull для контейнера извлечения ключевой фразы

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

### <a name="docker-pull-for-the-language-detection-container"></a>docker pull для контейнера определения языка

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/language:latest
```

### <a name="docker-pull-for-the-sentiment-container"></a>docker pull для контейнера тональности

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/sentiment:latest
```

[!INCLUDE [Tip for using docker list](../../../../includes/cognitive-services-containers-docker-list-tip.md)]


## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](../text-analytics-resource-container-config.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

| Placeholder | Значение |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера и доступен на странице API анализа текста на портале Azure.  |
|{BILLING_ENDPOINT_URI} | Значение URI конечной точки выставления счетов доступно на странице обзора API анализа текста на портале Azure.|

В следующем примере команды `docker run` замените имена параметров собственными значениями.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* запускает контейнер ключевой фразы из образа контейнера;
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](../text-analytics-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел https://localhost:5000 для API контейнера.

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](../text-analytics-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера. 

## <a name="containers-api-documentation"></a>Документация по API контейнера

[!INCLUDE [Container's API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="billing"></a>Выставление счетов

Контейнеры API анализа текста отправляют данные для выставления счетов в Azure с помощью ресурса _API анализа текста_ в учетной записи Azure. 

Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют данные клиента в корпорацию Майкрософт. 

В команде `docker run` используются следующие аргументы для выставления счетов.

| Параметр | ОПИСАНИЕ |
|--------|-------------|
| `ApiKey` | Ключ API ресурса _API анализа текста_ используется для отслеживания информации о выставлении счетов. |
| `Billing` | Конечная точка ресурса _API анализа текста_ используется для отслеживания информации о выставлении счетов.|
| `Eula` | Указывает, что вы приняли условия лицензии для контейнера.<br/>Для этого параметра следует задать значение `accept`. |

> [!IMPORTANT]
> Для всех трех параметров необходимо задать допустимые значения. В противном случае контейнер не запустится.

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Анализа текста. В разделе "Сводка" сделайте следующее.

* Анализ текста предоставляет три контейнера Linux для Docker, инкапсулируя извлечение ключевых фраз, определение языка и анализ тональности.
* Образы контейнеров скачиваются из Реестра контейнеров (Майкрософт) в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, пакет SDK или REST API можно использовать для вызова операций в контейнерах Анализа текста.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь со статьей о [конфигурации контейнеров](../text-analytics-resource-container-config.md).
* Чтобы решить проблемы, связанные с функциональностью, ознакомьтесь с [часто задаваемыми вопросами](../text-analytics-resource-faq.md).

