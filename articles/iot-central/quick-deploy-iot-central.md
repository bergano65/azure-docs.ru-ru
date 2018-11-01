---
title: Создание приложения Azure IoT Central | Документация Майкрософт
description: Создание нового приложения Azure IoT Central для управления охлаждаемыми торговыми автоматами. Просмотр данных телеметрии, созданных в имитированном устройстве.
author: tbhagwat3
ms.author: tanmayb
ms.date: 10/12/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: c6ed1f0feaa9b8b20d291be7929228707281cf9b
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158097"
---
# <a name="create-an-azure-iot-central-application"></a>Создание приложения Azure IoT Central

С помощью пользовательского интерфейса Azure IoT Central _конструктор_ может определить свое приложение Microsoft Azure IoT Central. В этом кратком руководстве объясняется, как создать приложение Azure IoT Central, которое содержит пример _шаблона устройства_ и _имитированные устройства_.

## <a name="create-the-application"></a>Создание приложения

Для выполнения инструкций в этом кратком руководстве необходимо создать приложение Azure IoT Central с помощью шаблона приложения **Образец Contoso**.

Перейдите на страницу [диспетчера приложений](https://aka.ms/iotcentral) в Azure IoT Central. Затем введите адрес электронной почты и пароль, которые вы используете для доступа к подписке Azure:

![Ввод учетной записи организации](media/quick-deploy-iot-central/sign-in.png)

Чтобы начать создание нового приложения Azure IoT Central, выберите **Новое приложение**:

![Страница диспетчера приложений Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Чтобы создать приложение Azure IoT Central, сделайте следующее:

1. Выберите план оплаты **Free Trial Application** (Бесплатная пробная версия приложения).
1. Выберите понятное имя для приложения, например **Contoso IoT**. Azure IoT Central создаст уникальный префикс URL-адреса. Вы можете изменить этот префикс на что-то более запоминающееся.
1. Выберите шаблон приложения **Sample Contoso** (Образец Contoso).
1. Щелкните **Создать**.

![Страница создания приложения Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Дополнительная информация

В рамках этого краткого руководства вы создали предварительно заполненное приложение Azure IoT Central, которое содержит шаблон устройства **Охлаждаемый торговый автомат** и имитированные устройства. Дополнительные сведения о том, как конструктор может определить шаблоны устройств см. в [этой статье](tutorial-define-device-type.md).
