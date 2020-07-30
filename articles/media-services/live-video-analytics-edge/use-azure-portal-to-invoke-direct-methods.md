---
title: Как использовать портал Azure для вызова прямых методов
description: Эта статья содержит общие сведения об использовании портала Azure для вызова прямых методов.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: 763dd82c8263a5e180468f9fbd7f86526295a80d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87279293"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Как использовать портал Azure для вызова прямых методов

Центр Интернета вещей позволяет вам вызывать [прямые методы](/azure/iot-hub/iot-hub-devguide-direct-methods#method-invocation-for-iot-edge-modules) на пограничных устройствах из облака. Модуль "Аналитика видеотрансляций в IoT Edge" (LVA) предоставляет несколько [прямых методов](/azure/media-services/live-video-analytics-edge/direct-methods), которые можно использовать для определения, развертывания и создания различных рабочих процессов для анализа видеотрансляций.

Из этой статьи вы узнаете, как вызывать прямые методы в модуле "Аналитика видеотрансляций для IoT Edge" через портал Azure.

## <a name="prerequisites"></a>Предварительные требования

* На пограничном устройстве выполняется модуль "Аналитика видеотрансляций в IoT Edge", запущенный с помощью одного из методов из статьи [Краткое руководство. Аналитика видеотрансляций в IoT Edge](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) или через [портал](/azure/media-services/live-video-analytics-edge/deploy-iot-edge-device).

* Вы изучили принципы работы с [Аналитикой видеотрансляций](/azure/media-services/live-video-analytics-edge/overview) и [концепцию графов мультимедиа](/azure/media-services/live-video-analytics-edge/media-graph-concept).

## <a name="invoking-direct-methods-via-azure-portal"></a>Вызов прямых методов через портал Azure

Все [прямые методы](/azure/media-services/live-video-analytics-edge/direct-methods), предоставляемые модулем LVA, можно вызвать через портал Azure. Ниже приведены шаги для одного прямого метода. Другие методы можно вызвать, используя аналогичные шаги. Но помните, что для каждого прямого метода требуется текст в формате JSON.

Используйте вызов метода `GraphTopologyList` для получения списка всех топологий графов, которые в настоящее время развернуты в модуле "Аналитика видеотрансляций в IoT Edge". Вызовите этот прямой метод с помощью следующих шагов:

1. Войдите на портал Azure.
1. Найдите нужную группу ресурсов на домашней странице портала, чтобы найти свой Центр Интернета вещей или сразу выберите его.
    ![Группа ресурсов на домашней странице портала](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. На странице Центра Интернета вещей выберите IoT Edge в разделе "Автоматическое управление устройствами", чтобы получить список различных идентификаторов устройств. Выберите соответствующий идентификатор устройства, чтобы вывести список модулей, выполняющихся на устройстве.
    ![Страница Центра Интернета вещей](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Выберите модуль "Аналитика видеотрансляций в IoT Edge", чтобы открыть его страницу конфигурации.<br><br>
    ![Выбор модуля "Аналитика видеотрансляций в IoT Edge", чтобы открыть его страницу конфигурации](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Выберите в меню параметр "Прямой метод". <br><br>
    ![Щелчок на параметре меню "Прямой метод"](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Вам может потребоваться добавить значение в разделы "Строка подключения", как можно видеть на текущей странице. Вам не нужно скрывать или изменять что-либо в разделе "Имя параметра". Его можно оставить общедоступным.

1. Введите *GraphTopologyList* в поле **Имя метода**.
1. Скопируйте и вставьте код JSON ниже в поле **Полезные данные**.
    ```json
    {
    "@apiVersion":
    }
    ```
1. Нажмите кнопку **Вызвать метод** в верхней части страницы.<br><br>
    ![Кнопка "Вызвать метод"](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. В области **Результат** должно отобразиться сообщение состояния 200.<br><br>
    ![Истечение времени ожидания подключения](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Ответы

| Условие             | Код состояния | Подробный код ошибки |
|-----------------------|-------------|---------------------|
| Успешно               | 200         | Недоступно                 |
| Общие ошибки пользователя   | Ошибки диапазона 400   |                     |
| Общие ошибки сервера | Ошибки диапазона 500   |                     |

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные прямые методы можно найти на странице [Прямые методы](/azure/media-services/live-video-analytics-edge/direct-methods).

> [!NOTE]
> Экземпляр графа создает определенную топологию, поэтому перед созданием экземпляра графа убедитесь, что ваша топология правильна.

[Краткое руководство. Обнаружение движения и события порождения](/azure/media-services/live-video-analytics-edge/get-started-detect-motion-emit-events-quickstart) содержит сведения о точной последовательности вызова прямых методов.
