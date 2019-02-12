---
title: Настройка правил и действий в Azure IoT Central | Документация Майкрософт
description: В этом руководстве показано, как разработчику настраивать правила и действия на основе телеметрии в своем приложении Azure IoT Central.
author: ankitscribbles
ms.author: ankitgup
ms.date: 01/28/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: d8a5ca6285624720e23a4986917ab5e715f6ebfa
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768019"
---
# <a name="tutorial-configure-rules-and-actions-for-your-device-in-azure-iot-central-new-ui-design"></a>Руководство. Настройка правил и действий для устройства в Azure IoT Central (новый дизайн пользовательского интерфейса)

*Эта статья предназначена для операторов, разработчиков и администраторов.*

В этом руководстве создается правило, которое отправляет сообщение электронной почты, когда температура на кондиционере превышает 90 &deg;F.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание правила на основе телеметрии
> * Добавление действия

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Перед началом необходимо выполнить руководство по [определению нового типа устройства в приложении](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), чтобы создать для работы шаблон устройства **Подключенный кондиционер**.

## <a name="create-a-telemetry-based-rule"></a>Создание правила на основе телеметрии

1. Чтобы добавить новое правило на основе телеметрии в приложение, в левом меню навигации выберите **шаблоны устройства**.

    ![Страница шаблонов устройств](media/tutorial-configure-rules-experimental/templatespage1.png)

    Вы увидите шаблон устройства **Подключенный кондиционер (1.0.0)**, созданный в предыдущем руководстве.

2. Чтобы настроить шаблон устройства, выберите шаблон **подключенного кондиционера**, созданный в предыдущем руководстве.

3. Чтобы добавить правило на основе телеметрии в представление **Правила**, выберите **Правила**, щелкните **+ New Rule** (+ Новое правило), а затем выберите **Телеметрия**.

    ![Представление правил](media/tutorial-configure-rules-experimental/newrule.png)

5. Чтобы определить правило, используйте информацию из следующей таблицы.

    | Параметр                                      | Значение                             |
    | -------------------------------------------- | ------------------------------    |
    | ИМЯ                                         | Оповещение о температуре кондиционера |
    | Enable rule for all devices of this template (Включить правило для всех устройств этого шаблона) | С                                |
    | Условие                                    | Температура больше 90    |
    | Агрегирование                                  | Нет                              |

    ![Условие правила температуры](media/tutorial-configure-rules-experimental/temperaturerule.png)

    Нажмите кнопку **Сохранить**.

## <a name="add-an-action"></a>Добавление действия

При определении правила необходимо также определить действие, выполняемое при соблюдении условий правила. В этом руководстве вы создадите правило с действием, которое отправляет уведомление по электронной почте.

1. Чтобы добавить **действие**, **сохраните** правило, прокрутите вниз к панели **Configure Telemetry Rule** (Настройка правила на основе телеметрии), выберите знак **+** рядом с разделом **Действия**, а потом выберите **Электронное письмо**.

    ![Действие правила, касающегося температуры](media/tutorial-configure-rules-experimental/addaction.png)

2. Чтобы определить действие, используйте информацию из следующей таблицы.

    | Параметр   | Значение                          |
    | --------- | ------------------------------ |
    | Кому        | Ваш адрес электронной почты             |
    | Примечания     | Превышено пороговое значение температуры кондиционера. |

    > [!NOTE]
    > Чтобы получить уведомление по электронной почте, адрес электронной почты должен быть [идентификатором пользователя в приложении](howto-administer-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json), а пользователь должен выполнить вход в приложение по крайней мере один раз.

    ![Действие, касающееся температуры](media/tutorial-configure-rules-experimental/temperatureaction.png)

3. Выберите команду **Сохранить**. Ваше правило будет указано на странице **Правила**.

## <a name="test-the-rule"></a>Тестирование правила

Вскоре после сохранения правила оно станет активным. При соблюдении условий, заданных в правиле, приложение отправляет сообщение на адрес электронной почты, указанный в действии.

![Действие для отправки электронного сообщения](media/tutorial-configure-rules-experimental/email.png)

> [!NOTE]
> Когда завершите тестирование, отключите правило, чтобы не получать уведомления в папке входящих сообщений.

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Создание правила на основе телеметрии
> * Добавление действия

Теперь, когда вы определили правило на основе порогового значения, предлагаемым следующим шагом является [настройка представлений оператора](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

Чтобы узнать больше о различных типах правил в Azure IoT Central и о том, как параметризовать определение правила, см:
* [Создание правила на основе телеметрии и настройка уведомлений в приложении Azure IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Создание правила на основе события и настройка уведомлений в приложении Azure IoT Central](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

<!-- Next tutorials in the sequence -->
