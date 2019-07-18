---
title: включение файла
description: включение файла
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229288"
---
Создание Центра Интернета вещей с помощью портала Azure

1. Войдите на [портале Azure](https://portal.azure.com).

1. Выберите **Создать ресурс** > **Интернет вещей** > **Центр Интернета вещей**.

    ![Выбор параметра для установки Центра Интернета вещей](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Чтобы создать Центр Интернета вещей бесплатного уровня, используйте значения из следующей таблицы:

    | Параметр | Значение |
    | ------- | ----- |
    | Subscription | Выберите подписку Azure в раскрывающемся списке. |
    | Resource group | Создайте группу ресурсов В этом руководстве используется имя **tutorials-iot-hub-rg**. |
    | Регион | В этом руководстве используется **Западная часть США**. Вы можете выбрать ближайший к вам регион. |
    | ИМЯ | На следующем снимке экрана используется имя **tutorials-iot-hub**. При создании Центра выберите собственное уникальное имя. |

    ![Параметры Центра 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Параметр | Значение |
    | ------- | ----- |
    | Ценовая категория и категория масштабирования | Бесплатный F1. В подписке может быть только один Центр Интернета вещей категории бесплатный. |
    | Единицы Центра Интернета вещей | 1 |

    ![Параметры Центра 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Нажмите кнопку **Создать**. Создание Центра может занять несколько минут.

    ![Параметры Центра 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Запишите выбранное имя Центра Интернета вещей. Это значение используется далее в руководстве.
