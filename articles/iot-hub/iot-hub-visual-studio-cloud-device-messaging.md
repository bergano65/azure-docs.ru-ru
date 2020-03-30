---
title: Используйте VS Cloud Explorer для управления сообщениями устройств Azure IoT Hub
description: Узнайте, как использовать Cloud Explorer для Visual Studio для мониторинга сообщений между облаком и устройством в Центре Интернета вещей Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079478"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Использование Cloud Explorer для Visual Studio для обмена сообщениями между устройством и Центром Интернета вещей

![Комплексная схема](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) — это полезное расширение для Visual Studio. Оно позволяет просматривать ресурсы Azure, проверять их свойства и выполнять основные действия разработчика в среде Visual Studio. В этой статье основное внимание уделяется использованию Cloud Explorer для отправки и получения сообщений между устройством и концентратором.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Что вы узнаете

В этой статье вы узнаете, как использовать Cloud Explorer для Visual Studio для мониторинга сообщений от устройства к облаку и отправки сообщений от облака до устройства. В сообщениях, отправляемых с устройства в облако, могут содержаться данные датчиков, которые устройство собирает и отправляет в Центр Интернета вещей. Сообщения, отправляемые из облака на устройство, могут содержать команды, которые Центр Интернета вещей отправляет на устройство. Например, для активации светодиодного индикатора, подключенного к устройству.

## <a name="what-you-do"></a>Что нужно сделать

В этой статье вы выполняете следующие задачи:

- Мониторинг сообщений, отправляемых с устройства в облако, с помощью Cloud Explorer для Visual Studio.

- Мониторинг сообщений, отправляемых из облака на устройство, с помощью Cloud Explorer для Visual Studio.

## <a name="what-you-need"></a>Необходимые элементы

Вам потребуется сделать следующее:

- Активная подписка Azure.

- Центр Интернета вещей Azure в подписке.

- Microsoft Visual Studio 2017 Обновление 9 или более поздней версии. Эта статья использует [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Компонент Cloud Explorer от Visual Studio Installer, который по умолчанию выбирается с помощью рабочей нагрузки Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Обновление Cloud Explorer до последней версии

Компонент Cloud Explorer от Visual Studio Installer for Visual Studio 2017 поддерживает только мониторинг сообщений от устройства к облаку и облачным устройствам. Чтобы использовать Visual Studio 2017, загрузите и установите новейший [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Войти на вход, чтобы получить доступ к концентратору

Чтобы получить доступ к концентратору, выполните следующие действия:

1. В Visual Studio выберите **View** > **Cloud Explorer,** чтобы открыть Cloud Explorer.

1. Выберите значок управления учетной записью, чтобы показать подписку.

    ![Значок управления счетами](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Если вы вписались в Azure, отображаются учетные записи. Чтобы войти в Azure в первый раз, **выберите учетную запись**.

1. Выберите подписи Azure, которые вы хотите использовать, и выберите **Apply.**

1. Расширьте подписку, а затем расширьте **концентраторы IoT.**  Под каждым концентратором можно увидеть устройства для этого концентратора.

    ![Список устройств](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Отслеживание сообщений, отправляемых с устройства в облако

Чтобы отслеживать сообщения, отправляемые в Центр Интернета вещей с устройства, выполните следующие действия.

1. Щелкните правой кнопкой мыши Центр Интернета вещей или устройство и выберите **Start Monitoring D2C Message** (Начать мониторинг сообщений D2C).

    ![Действие Start Monitoring D2C Message (Начать мониторинг сообщений D2C)](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Контролируемые сообщения отображаются под **выходом**.

    ![Результаты мониторинга сообщений D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Чтобы остановить мониторинг, щелкните правой кнопкой мыши любой Центр Интернета вещей или любое устройство и выберите **Stop Monitoring D2C Message** (Остановить мониторинг сообщений D2C).

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство

Чтобы отправить сообщение из Центра Интернета вещей на устройство, выполните следующие действия.

1. Щелкните правой кнопкой мыши свое устройство и выберите **Send C2D Message** (Отправить сообщение C2D).

1. Введите сообщение в поле ввода.

    ![Действие Send C2D Message (Отправить сообщение C2D)](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Результаты отображаются под **выходом**.

    ![Результаты отправки сообщения C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Дальнейшие действия

Вы узнали, как отслеживать сообщения, отправляемые из устройства Интернета вещей в облако Центра Интернета вещей, и отправлять сообщения из этого облака на устройство.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]