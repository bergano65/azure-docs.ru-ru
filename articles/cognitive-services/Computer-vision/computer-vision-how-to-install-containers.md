---
title: Установка и запуск контейнеров — API компьютерного зрения
titlesuffix: Azure Cognitive Services
description: В этом пошаговом руководстве показано, как скачивать, устанавливать и выполнять контейнеры компьютерного зрения.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: efde223061a873a57595bc4a577b7de55b1d8a46
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321461"
---
# <a name="install-and-run-recognize-text-containers"></a>Установка и запуск контейнеров распознавания текста

Служба "Распознавание текста", входящая в состав Компьютерного зрения, также доступна в виде контейнера Docker. Это позволяет определять и извлекать печатный текст с изображений различных объектов с разными поверхностями и фонами, например чеков, плакатов и визитных карточек.  
> [!IMPORTANT]
> Контейнер распознавания текста в настоящее время работает только на английском языке.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительные требования

Прежде чем использовать контейнеры Распознавания текста, необходимо выполнить следующие условия.

|Обязательно для заполнения|Цель|
|--|--|
|Модуль Docker| На [главном компьютере](#the-host-computer) должен быть установлен модуль Docker. Docker предоставляет пакеты, которые настраивают среду Docker в ОС [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) и [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Ознакомьтесь с [общими сведениями о Docker и контейнерах](https://docs.docker.com/engine/docker-overview/).<br><br> Docker нужно настроить таким образом, чтобы контейнеры могли подключать и отправлять данные о выставлении счетов в Azure. <br><br> **В ОС Windows** для Docker нужно также настроить поддержку контейнеров Linux.<br><br>|
|Опыт работы с Docker | Требуется базовое представление о понятиях Docker, включая реестры, репозитории, контейнеры и образы контейнеров, а также знание основных команд `docker`.| 
|Ресурс Компьютерное зрение |Для использования контейнера необходимо следующее:<br><br>Ресурс Azure **компьютерное зрение** и соответствующий ключ API для конечной точки. Оба значения доступны на страницах обзора и ключей для ресурса и необходимы для запуска контейнера.<br><br>**{API_KEY}** : Один из двух доступных ключей ресурсов на странице " **ключи** "<br><br>**{ENDPOINT_URI}** : Конечная точка, указанная на странице **обзора**|

## <a name="request-access-to-the-private-container-registry"></a>Запрос доступа к частному реестру контейнеров

[!INCLUDE [Request access to private preview](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Главный компьютер

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Требования к контейнеру и рекомендации

В следующей таблице описаны минимальное и рекомендуемое число ядер ЦП и памяти для каждого контейнера Распознавания текста.

| Контейнер | Минимальная | Рекомендуется |ПЛАТЫ<br>(Минимум, максимум)|
|-----------|---------|-------------|--|
|Распознавание текста|1 ядро, 8 ГБ памяти, 0,5 TPS|2 ядра, 8 ГБ памяти, 1 Техническая спецификация|0,5, 1|

* Частота каждого ядра должна быть минимум 2,6 ГГц.
* TPS — транзакций в секунду.

Ядро и память соответствуют параметрам `--cpus` и `--memory`, которые используются как часть команды `docker run`.

## <a name="get-the-container-image-with-docker-pull"></a>Получение образа контейнера с помощью `docker pull`

Доступны образы контейнеров для Распознавания текста. 

| Контейнер | Репозиторий |
|-----------|------------|
|Распознавание текста | `containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest` |

Воспользуйтесь командой [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/), чтобы скачать образ контейнера.


### <a name="docker-pull-for-the-recognize-text-container"></a>docker pull для контейнера Распознавания текста

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>Использование контейнера

После размещения контейнера на [главном компьютере](#the-host-computer) воспользуйтесь следующей процедурой для работы с ним.

1. [Запустите контейнер](#run-the-container-with-docker-run) с необходимыми настройками выставления счетов. Доступны дополнительные [примеры](computer-vision-resource-container-config.md) команды `docker run`. 
1. [Запрос конечной точки прогнозирования контейнера](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Запуск контейнера с помощью команды `docker run`

Воспользуйтесь командой [docker run](https://docs.docker.com/engine/reference/commandline/run/) для запуска контейнера. В команде используются следующие параметры:

| Placeholder | Значение |
|-------------|-------|
|{API_KEY} | Этот ключ используется для запуска контейнера и доступен на странице ключей Azure `Cognitive Services` .  |
|{ENDPOINT_URI} | Значение URI конечной точки выставления счетов. Пример:`https://westus.api.cognitive.microsoft.com/vision/v2.0`|

Необходимо добавить `vision/v2.0` маршрутизацию к универсальному коду ресурса (URI) конечной точки, как показано в следующем примере BILLING_ENDPOINT_URI.

В следующем примере команды `docker run` замените имена параметров собственными значениями.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Эта команда:

* запускает контейнер распознавания из образа контейнера;
* выделяет одно ядро ЦП и 4 ГБ памяти;
* предоставляет TCP-порт 5000 и выделяет псевдотелетайп для контейнера;
* автоматически удаляет контейнер после завершения его работы. Образ контейнера остается доступным на главном компьютере. 

Доступны дополнительные [примеры](./computer-vision-resource-container-config.md#example-docker-run-commands) команды `docker run`. 

> [!IMPORTANT]
> Для выполнения контейнера необходимо указать параметры `Eula`, `Billing` и `ApiKey`. В противном случае контейнер не запустится.  Дополнительные сведения см. в [разделе о выставлении счетов](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Запрос конечной точки прогнозирования контейнера

Контейнер предоставляет API запроса конечной точки прогнозирования на основе REST. 

Используйте узел `http://localhost:5000` для API контейнера.

### <a name="asynchronous-text-recognition"></a>Асинхронное распознавание текста

Вы можете использовать операции `POST /vision/v2.0/recognizeText` и `GET /vision/v2.0/textOperations/*{id}*` для асинхронного распознавания печатного текста на изображении подобно тому, как служба "Компьютерное зрение" использует соответствующие операции REST. В настоящее время контейнер распознавания текста определяет только печатный текст и не распознает рукописный, поэтому параметр `mode`, обычно указываемый для операции службы "Компьютерное зрение", игнорируется контейнером распознавания текста.

### <a name="synchronous-text-recognition"></a>Синхронное распознавание текста

С помощью операции `POST /vision/v2.0/recognizeTextDirect` можно синхронно распознавать печатный текст на изображении. Так как эта операция является синхронной, текст запроса для этой операции `POST /vision/v2.0/recognizeText` совпадает с текстом операции, но текст ответа для этой операции совпадает с текстом, возвращенным `GET /vision/v2.0/textOperations/*{id}*` операцией.

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Остановка контейнера

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если контейнер запускается с выходным [подключением](./computer-vision-resource-container-config.md#mount-settings) и включенным ведением журнала, контейнер создает файлы журнала, которые удобно использовать для устранения неполадок, возникающих во время запуска или работы контейнера. 


## <a name="billing"></a>Выставление счетов

Контейнеры Распознавания текста отправляют данные для выставления счетов в Azure с помощью ресурса _Распознавание текста_ в учетной записи Azure. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Дополнительные сведения об этих параметрах см. в статье [Настройка контейнеров](./computer-vision-resource-container-config.md).

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Сводка

В этой статье вы узнали основные понятия и рабочий процесс для скачивания, установки и выполнения контейнеров Распознавания текста. В разделе "Сводка" сделайте следующее.

* Распознавание текста предоставляет контейнер Linux для Docker, инкапсулируя Распознавание текста.
* Образы контейнеров скачиваются из Реестра контейнеров (Майкрософт) в Azure.
* Образы контейнеров выполняются в Docker.
* Указав узел URI контейнера, пакет SDK или REST API можно использовать для вызова операций в контейнерах Распознавания текста.
* При создании экземпляра контейнера нужно указать данные для выставления счетов.

> [!IMPORTANT]
> Контейнеры Cognitive Services не лицензируются для запуска без подключения к Azure для отслеживания использования. Клиенты должны разрешить контейнерам непрерывную передачу данных для выставления счетов в службу контроля потребления. Контейнеры Cognitive Services не отправляют в корпорацию Майкрософт данные клиента (например анализируемые изображения или тексты).

## <a name="next-steps"></a>Следующие шаги

* Ознакомьтесь со статьей о [конфигурации контейнеров](computer-vision-resource-container-config.md).
* Дополнительные сведения о распознавании печатного и рукописного текста см. в статье [Обзор компьютерного зрения](Home.md).  
* Дополнительные сведения о методах, поддерживаемых контейнером, см. в статье [API компьютерного зрения](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).
* Чтобы разрешать проблемы, связанные с функциональностью Компьютерного зрения, ознакомьтесь с [часто задаваемыми вопросами](FAQ.md).
* [Поддержка контейнеров в Azure Cognitive Services](../cognitive-services-container-support.md)
