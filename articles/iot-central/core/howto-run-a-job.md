---
title: Создание и запуск заданий в приложении Azure IoT Central | Документация Майкрософт
description: Задания Azure IoT Central позволяют использовать массовые функции управления устройствами, такие как обновление свойства устройства, настройка или выполнение команды.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2a18cfabdecf930f45ca10ae25e9be44cf3f1a5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953152"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Создание и выполнение задания в приложении IoT Central Azure

Вы можете использовать Microsoft Azure IoT Central для управления подключенными устройствами в масштабе с использованием заданий. Задания позволяют выполнять групповые обновления свойств, параметров и команд устройства. В этой статье описывается, как приступить к работе с заданиями в собственном приложении.

> [!NOTE] 
> Задания для Azure IoT Edge устройств в настоящее время не поддерживаются. 

## <a name="create-and-run-a-job"></a>Создание и запуск заданий

В этом разделе показано, как создать и запустить задание. В нем показано, как увеличить скорость вентилятора для нескольких рефрижератедных компьютеров.

1. В области навигации перейдите к заданиям.

2. Выберите **+ создать** , чтобы создать новое задание.

    ![Создание задания](./media/howto-run-a-job/createnewjob.png)

3. Введите имя и описание, чтобы указать создаваемое задание.

4. Выберите набор устройств, к которому должно применяться задание. После выбора набора устройств вы увидите правую часть, заполненную устройствами в наборе устройств. Если выбрать неработающий набор устройств, устройства не отображаются и отображается сообщение о том, что набор устройств поврежден.

5. Затем выберите тип задания для определения (параметр, свойство или команда). Выберите **+** рядом с выбранным типом задания и добавьте операции.

    ![Настройка задания](./media/howto-run-a-job/configurejob.png)

6. На правой стороне выберите устройства, на которых нужно запустить задание. Устанавливая верхний флажок, все устройства выбираются во всем наборе устройств. Установив флажок рядом с полем **имя**, будут выбраны все устройства на текущей странице.

7. Выбрав устройства, нажмите кнопку **выполнить** или **сохранить**. Теперь задание отображается на главной странице **заданий** . В этом представлении можно увидеть текущее выполняемое задание и историю всех ранее выполненных заданий. Выполняемое задание всегда отображается в верхней части списка. Сохраненное задание можно снова открыть в любое время, чтобы продолжить редактирование или запуск.

    ![Просмотр задания](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Вы можете просмотреть историю ранее выполненных заданий в течение 30 дней.

8. Чтобы получить общие сведения о задании, выберите задание для просмотра из списка. Этот обзор содержит сведения о задании, устройствах и состоянии устройств. В этом обзоре можно также выбрать команду **скачать сведения о задании** , чтобы скачать CSV-файл сведений о задании, включая устройства и их значения состояния. Эти сведения могут быть полезны для устранения неполадок.

    ![Просмотр состояния устройства](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Остановка выполнения задания

Чтобы прерывать выполняющееся задание, выберите его и нажмите кнопку " **Закрыть** " на панели. Состояние задания изменится на отражать, что задание остановлено.

   ![Остановка задания](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Запуск остановленного задания

Чтобы запустить задание, которое в данный момент остановлено, выберите остановленное задание. На панели выберите **выполнить** . Состояние задания изменится, чтобы отразить задание, теперь оно снова запускается.

   ![Возобновление выполнения задания](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Копирование задания

Чтобы скопировать созданное задание, выберите его на странице главные задания и щелкните **Копировать**. Откроется новая копия конфигурации задания для редактирования. Вы можете сохранить или запустить новое задание. Если в выбранном наборе устройств были внесены какие либо изменения, они отражаются в этом скопированном задании для изменения.

   ![Задание копирования](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Просмотр состояния задания

После создания задания в столбце **состояние** обновляется Последнее сообщение о состоянии задания. В следующей таблице перечислены возможные значения состояния.

| Сообщение о состоянии       | Значение состояния                                          |
| -------------------- | ------------------------------------------------------- |
| Завершено            | Это задание было выполнено на всех устройствах.              |
| Failed               | Это задание завершилось сбоем и выполнено не полностью на устройствах.  |
| Ожидает              | Это задание еще не начала выполняться на устройствах.         |
| Выполнение              | Это задание в данный момент выполняется на устройствах.             |
| Остановлена              | Это задание вручную остановлено пользователем.           |

После сообщения о состоянии следует обзор устройств в задании. В следующей таблице перечислены возможные значения состояния устройства.

| Сообщение о состоянии       | Значение состояния                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Число устройств, на которых задание было успешно выполнено.       |
| Failed               | Число устройств, на которых не удалось выполнить задание.       |

### <a name="view-the-device-status"></a>Просмотр состояния устройства

Чтобы просмотреть состояние задания и всех затронутых устройств, выберите задание. Чтобы скачать CSV-файл, содержащий сведения о задании, включая список устройств и их значения состояния, выберите **загрузить сведения о задании**. Рядом с каждым именем устройства отображается одно из следующих сообщений о состоянии:

| Сообщение о состоянии       | Значение состояния                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Завершено            | Это задание было выполнено на этом устройстве.                                     |
| Failed               | Это задание завершилось сбоем на этом устройстве. В сообщении об ошибке содержатся дополнительные сведения.  |
| Ожидает              | Задание еще не выполнялось на этом устройстве.                                   |

> [!NOTE]
> Если устройство удалено, вы не сможете выбрать его, и оно отобразится как удаленное с ИДЕНТИФИКАТОРом устройства.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как создавать задания в приложении IoT Central Azure, выполните следующие действия.

- [использовать наборы устройств](howto-use-device-sets.md);
- [Управление устройствами](howto-manage-devices.md)
- [Создание версии шаблона нового устройства](howto-version-device-template.md)
