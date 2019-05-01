---
title: Установка и запуск контейнеров
titlesuffix: Face - Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры для API распознавания лиц.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: a3087718a3eece1e19f05757ace64ba00aa0dc58
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682682"
---
# <a name="install-and-run-face-containers"></a>Установка и запуск контейнеров лиц

API распознавания лиц предоставляет стандартный контейнер Linux для Docker с именем Face, который обнаруживает человеческие лица на изображениях и определяет методом машинного прогнозирования их характеристики, например черты лица (нос, глаза и т. д.), пол, возраст и многое другое. Кроме обнаружения лиц, API может сравнивать два лица на одном или на разных изображениях, используя оценку достоверности, а также искать по базе данных похожие или идентичные лица. Схожие лица можно упорядочивать в группы по близким визуальным характеристикам.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Технические условия

Прежде чем использовать контейнеры API распознавания лиц, необходимо выполнить следующие условия.

|Обязательно для заполнения|Назначение|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Azure `Cognitive Services` ресурсов |Для использования контейнера необходимо следующее:<br><br>Объект _Cognitive Services_ ресурсов Azure и соответствующее выставление счетов ключа выставления счетов URI конечной точки. Оба значения доступны на страницах Обзор и ключи для ресурса и необходимые для запуска контейнера. Необходимо добавить `face/v1.0` маршрутизации URI конечной точки, как показано в следующем примере BILLING_ENDPOINT_URI. <br><br>**{BILLING_KEY}**: ключ ресурса<br><br>**{BILLING_ENDPOINT_URI}**: пример URI конечной точки: `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]


### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое число ядра ЦП и памяти для каждого контейнера API распознавания лиц.

| Контейнер | Минимальная | Рекомендуется | ТРАНЗАКЦИЙ В СЕКУНДУ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|API распознавания лиц | 1 ядро, 2 ГБ памяти | 1 ядро, 4 ГБ памяти |10, 20|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* Транзакций в Секунду - транзакций в секунду

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для API распознавания лиц. 

| Контейнер | Репозиторий |
|-----------|------------|
| API распознавания лиц | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>docker pull для контейнера распознавания лиц

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска любого из трех контейнеров. В команде используются следующие параметры:

| Placeholder | Value |
|-------------|-------|
|{BILLING_KEY} | Этот ключ используется для запуска контейнера, а также доступна в Azure `Cognitive Services` страницы "ключи".  |
|{BILLING_ENDPOINT_URI} | Выставления счетов значение URI конечной точки можно найти в Azure `Cognitive Services` странице "Обзор". Ниже приведен пример: `https://westus.api.cognitive.microsoft.com/face/v1.0`|

Необходимо добавить `face/v1.0` маршрутизации URI конечной точки, как показано в приведенном выше примере BILLING_ENDPOINT_URI. 

В следующем примере команды `docker run` замените имена параметров собственными значениями.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Эта команда:

* запускает контейнер распознавания лиц из образа контейнера;
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](./face-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `https://localhost:5000` для API контейнера.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](./face-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера. 


## <a name="billing"></a>Выставление счетов

Контейнеры API распознавания лиц отправляют данные для выставления счетов в Azure с помощью ресурса _API распознавания лиц_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./face-resource-container-config.md).

## <a name="summary"></a>Сводка

В этой статье мы рассмотрели основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров API распознавания лиц. В разделе "Сводка" сделайте следующее.

* API распознавания лиц предоставляет три контейнера Linux для Docker, инкапсулируя извлечение ключевых фраз, определение языка и анализ тональности.
* Образы контейнеров скачиваются из Реестра контейнеров (Майкрософт) в Azure.
* Образы контейнеров выполняются в Docker.
* Зная URI узла контейнера, вы можете применить пакет SDK или REST API для вызова операций из контейнера API распознавания лиц.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например, анализируемые изображения или тексты).

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со статьей о [конфигурации контейнеров](face-resource-container-config.md).
* Изучите описание [API распознавания лиц](Overview.md), чтобы получить дополнительные сведения об обнаружении и идентификации лиц.  
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [об API распознавания лиц](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Воспользуйтесь [дополнительными контейнерами Cognitive Services](../cognitive-services-container-support.md)
