---
title: Управление обменом сообщениями между устройством и облаком Центра Интернета вещей Azure с помощью Cloud Explorer для Visual Studio | Документация Майкрософт
description: Узнайте, как использовать Cloud Explorer для Visual Studio для мониторинга сообщений между облаком и устройством в Центре Интернета вещей Azure.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: bc955db4e851c1f59012dc1375170850e1b0604c
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54109509"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Использование Cloud Explorer для Visual Studio для обмена сообщениями между устройством и Центром Интернета вещей

![Комплексная схема](media/iot-hub-get-started-e2e-diagram/2.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) — это полезное расширение для Visual Studio. Оно позволяет просматривать ресурсы Azure, проверять их свойства и выполнять основные действия разработчика в среде Visual Studio. В этой статье объясняется, как использовать Cloud Explorer для обмена сообщениями между устройством и Центром Интернета вещей.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Новые знания

Вы узнаете, как использовать Cloud Explorer для Visual Studio для мониторинга сообщений между облаком и устройством. В сообщениях, отправляемых с устройства в облако, могут содержаться данные датчиков, которые устройство собирает и отправляет в Центр Интернета вещей. Сообщения, отправляемые из облака на устройство, могут содержать команды, которые Центр Интернета вещей отправляет на устройство. Например, для активации светодиодного индикатора, подключенного к устройству.

## <a name="what-you-will-do"></a>Выполняемая задача

- Мониторинг сообщений, отправляемых с устройства в облако, с помощью Cloud Explorer для Visual Studio.
- Мониторинг сообщений, отправляемых из облака на устройство, с помощью Cloud Explorer для Visual Studio.

## <a name="what-you-need"></a>Необходимые элементы

- Активная подписка Azure.
- Центр Интернета вещей Azure в подписке.
- Microsoft Visual Studio 2017 с обновлением 8 или более поздней версии.
- Компонент Cloud Explorer из Visual Studio Installer (по умолчанию выбран вместе с рабочей нагрузкой Azure).

## <a name="update-cloud-explorer-to-latest-version"></a>Обновление Cloud Explorer до последней версии

Компонент Cloud Explorer из Visual Studio Installer поддерживает только мониторинг сообщений между устройством и облаком (в обоих направлениях). Для отправки сообщений на устройство или в облако скачайте и установите последнюю версию [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Вход в систему для получения доступа к Центру Интернета вещей

1. В Visual Studio в окне **Cloud Explorer** щелкните значок управления учетными записями. Вы можете открыть окно Cloud Explorer с помощью меню **Вид** > **Cloud Explorer**.

    ![Выбор "Управление учетными записями"](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)

1. Щелкните **Управление учетными записями** в Cloud Explorer.
1. Выберите **Добавить учетную запись...** в новом окне при первом входе в Azure.
1. После входа появится список подписок Azure. Выберите подписки Azure, которые вы хотите просмотреть, и нажмите кнопку **Применить**.
1. Разверните узлы **Ваша подписка** > **IoT Hubs**(Центры Интернета вещей) > **Ваш Центр Интернета вещей**, после чего в узле Центра Интернета вещей появится список устройств.

    ![Список устройств](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Отслеживание сообщений, отправляемых с устройства в облако

Чтобы отслеживать сообщения, отправляемые в Центр Интернета вещей с устройства, выполните следующие действия.

1. Щелкните правой кнопкой мыши Центр Интернета вещей или устройство и выберите **Start Monitoring D2C Message** (Начать мониторинг сообщений D2C).

    ![Действие Start Monitoring D2C Message (Начать мониторинг сообщений D2C)](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

1. Отслеживаемые сообщения будут отображаться в области вывода **Центра Интернета вещей**.

    ![Результаты мониторинга сообщений D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

1. Чтобы остановить мониторинг, щелкните правой кнопкой мыши любой Центр Интернета вещей или любое устройство и выберите **Stop Monitoring D2C Message** (Остановить мониторинг сообщений D2C).

## <a name="send-cloud-to-device-messages"></a>Отправка сообщений из облака на устройство

Чтобы отправить сообщение из Центра Интернета вещей на устройство, выполните следующие действия.

1. Щелкните правой кнопкой мыши свое устройство и выберите **Send C2D Message** (Отправить сообщение C2D).

    ![Действие Send C2D Message (Отправить сообщение C2D)](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

1. Введите сообщение в поле ввода.
1. Результаты будут отображаться в области вывода **Центра Интернета вещей**.

    ![Результаты отправки сообщения C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Дополнительная информация

Вы узнали, как отслеживать сообщения, отправляемые из устройства Интернета вещей в облако Центра Интернета вещей, и отправлять сообщения из этого облака на устройство.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]