---
title: Добавление устройства Azure IoT Edge в Azure IoT Central | Документация Майкрософт
description: Как оператор вы можете добавить устройство Azure IoT Edge в приложение Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: f16db7ebff087b164228f2b23d6fa7ec302705bb
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406331"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Руководство по добавлению устройства Azure IoT Edge в приложение Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этом учебнике показано, как добавить устройство Azure IoT Edge в приложение Azure IoT Central и настроить его. В этом учебнике мы выберем виртуальную машину Linux с поддержкой IoT Edge из Azure Marketplace.

Это руководство состоит из двух частей:

* Во-первых, вы как оператор узнаете, как выполнить первоначальную подготовку облака для устройства IoT Edge.
* Затем вы узнаете, как выполнить первоначальную подготовку устройства IoT Edge.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление нового устройства IoT Edge.
> * Настройка устройства IoT Edge для подготовки с использованием ключа подписанного URL-адреса (SAS).
> * Просмотр панелей мониторинга и разделов работоспособности модулей в IoT Central.
> * Отправка команд в модуль, выполняющийся на устройстве IoT Edge.
> * Настройка свойств модуля, выполняющегося на устройстве IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется приложение Azure IoT Central. Чтобы создать приложение Azure IoT Central, воспользуйтесь сведениями из [этого краткого руководства](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Включение группы регистрации Azure IoT Edge
Включите ключи SAS для группы регистрации Azure IoT Edge на странице **Администрирование**.

![Снимок экрана: страница администрирования с выделенным пунктом "Подключение устройств"](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Первоначальная подготовка в облаке для устройства Azure IoT Edge  
В этом разделе вы создадите новое устройство IoT Edge с помощью шаблона датчика среды и подготовите устройство. Выберите **Устройства** > **Environment Sensor Template** (Шаблон датчика среды). 

![Снимок экрана: страница устройств с выделенным пунктом Environment Sensor Template (Шаблон датчика среды)](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Щелкните **+ Создать** и введите идентификатор и имя устройства, которое вам подходит. Нажмите кнопку **Создать**.

![Снимок экрана: диалоговое окно "Создание устройства" с выделенным полем "Идентификатор устройства" и кнопкой "Создать"](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Устройство переходит в режим **Зарегистрировано**.

![Снимок экрана: страница шаблона датчика среды с выделенным столбцом "Состояние устройства"](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Развертывание виртуальной машины Linux с поддержкой IoT Edge

> [!NOTE]
> Вы можете выбрать любой компьютер или устройство. Операционной системой может быть Linux или Windows.

В этом учебнике мы выбрали виртуальную машину Linux с поддержкой Интернета вещей Azure, созданную в Azure. В [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) нажмите кнопку **Получить**. 

![Снимок экрана: Azure Marketplace с выделенной кнопкой "Получить"](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Выберите **Продолжить**.

![Снимок экрана: диалоговое окно "Создать это приложение в Azure" с выделенной кнопкой "Продолжить"](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Вас перенаправят на портал Azure. Нажмите кнопку **Создать**.

![Снимок экрана: портал Azure с выделенной кнопкой "Создать"](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Выберите **подписку**, создайте группу ресурсов и выберите регион **Западная часть США 2**, в котором будет доступна виртуальная машина. Затем введите сведения о пользователе и пароль. Они понадобятся для будущих шагов, поэтому их следует запомнить. Выберите **Review + create** (Просмотреть и создать).

![Снимок экрана: страница сведений "Создание виртуальной машины" с выделенными параметрами](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

После проверки нажмите кнопку **Создать**.

![Снимок экрана: страница "Создание виртуальной машины" с выделенными элементами "Проверка пройдена" и "Создать"](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Создание ресурсов занимает несколько минут. Выберите **Перейти к ресурсу**.

![Снимок экрана: страница с уведомлением о завершении развертывания с выделенной кнопкой "Перейти к ресурсу"](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Подготовка виртуальной машины как устройства IoT Edge 

В разделе **Поддержка и устранение неполадок** выберите **Серийная консоль**.

![Снимок экрана: раздел "Поддержка и устранения неполадок" с выделенным элементом "Серийная консоль"](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Появится экран, аналогичный показанному ниже:

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Нажмите клавишу ВВОД и введите имя пользователя и пароль в ответ на запрос, а затем снова нажмите клавишу ВВОД. 

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Чтобы выполнить команду от имени администратора или привилегированного пользователя, введите команду: **sudo su –**

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Проверьте версию среды выполнения IoT Edge. На момент написания этой статьи текущая общедоступная версия — 1.0.8.

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Установите редактор vim или используйте nano при необходимости. 

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Измените файл config.yaml IoT Edge.

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Прокрутите вниз и закомментируйте часть строки подключения в файле YAML. 

**До:**

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**После** (Нажмите клавишу ESC, а затем — строчную букву a, чтобы начать редактирование.)

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Раскомментируйте часть симметричного ключа в файле YAML. 

**До:**

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**После:**

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Перейдите в IoT Central. Получите идентификатор области, код устройства и симметричный ключ устройства IoT Edge.
![Снимок экрана: страница IoT Central с выделением различных параметров подключения к устройству](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Перейдите в поле для компьютера Linux и замените идентификатор области и идентификатор регистрации на код устройства и симметричный ключ.

Нажмите клавишу ESC и введите **:wq!** . Нажмите клавишу ВВОД, чтобы сохранить изменения.

Перезапустите IoT Edge для обработки изменений и нажмите клавишу ВВОД.

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Введите **iotedge list**. Через несколько минут вы увидите три развернутых модуля.

![Снимок экрана консоли](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Обозреватель устройств в IoT Central 

В IoT Central устройство будет переведено в состояние "Подготовлено".

![Снимок экрана: параметры устройства IoT Central с выделенным столбцом "Состояние устройства"](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

На вкладке **Модули** отображается состояние устройства и модуля в IoT Central. 

![Снимок экрана: вкладка "Модули" IoT Central](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Свойства облака будут отображаться в форме (из шаблона устройства, созданного на предыдущих шагах). Введите значения и нажмите кнопку **Сохранить**. 

![Снимок экрана: форма My Linux Edge Device (Мое устройство Linux Edge)](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Вот представление в виде плитки панели мониторинга.

![Снимок экрана плиток панели мониторинга My Linux Edge Device (Мое устройство Linux Edge)](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как управлять устройствами IoT Edge в IoT Central, перейдите к дальнейшему рекомендуемому шагу:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Настройка прозрачного шлюза](../../iot-edge/how-to-create-transparent-gateway.md)
