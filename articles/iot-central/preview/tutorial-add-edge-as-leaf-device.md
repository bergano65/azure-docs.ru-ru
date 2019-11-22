---
title: Добавление устройства Azure IoT Edge в Azure IoT Central | Документация Майкрософт
description: Как оператор вы можете добавить устройство Azure IoT Edge в Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: ae80a624ed1f85a1f59fea79b152a4bc31067ad1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892640"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Руководство по добавлению устройства Azure IoT Edge в приложение Azure IoT Central (предварительные версии функций)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

В этом учебнике показано, как добавить *устройство Azure IoT Edge* в приложение Microsoft Azure IoT Central и настроить его. В этом учебнике мы выберем виртуальную машину Linux с поддержкой Azure IoT Edge из Azure Marketplace.

Это руководство состоит из двух частей:

* Во-первых, вы как оператор узнаете, как выполнить первоначальную подготовку облака для устройства Azure IoT Edge.
* Затем вы узнаете, как выполнить первоначальную подготовку устройства Azure IoT Edge.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Добавление нового устройства Azure IoT Edge.
> * Настройка устройства Azure IoT Edge для помощи в подготовке с использованием ключа SAS.
> * Просмотр панелей мониторинга и разделов работоспособности модулей в IoT Central.
> * Отправка команд в модуль, выполняющийся на устройстве Azure IoT Edge.
> * Настройка свойств модуля, выполняющегося на устройстве Azure IoT Edge.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуется приложение Azure IoT Central. Чтобы создать приложение Azure IoT Central, воспользуйтесь сведениями из [этой статьи](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Включение группы регистрации Azure IoT Edge
Включите ключи SAS для группы регистрации Azure IoT Edge на странице "Администрирование".

![Шаблон устройства — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Первоначальная подготовка в облаке для устройства Azure IoT Edge   
В этом разделе вы создадите новое устройство Azure IoT Edge с **помощью шаблона датчика окружающей среды** и подготовите устройство. Щелкните "Устройства" в области навигации слева и щелкните шаблон датчика окружающей среды. 

![Шаблон устройства — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Щелкните **+ Создать** и введите идентификатор и имя устройства, которое вам подходит. 

![Шаблон устройства — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Устройство переходит в режим **Зарегистрировано**.

![Шаблон устройства — Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Развертывание виртуальной машины Linux с поддержкой Azure IoT Edge

>Примечание. Вы можете выбрать любой компьютер или устройство. ОС: Linux или Windows.

В этом учебнике мы выбрали виртуальную машину Linux с поддержкой Интернета вещей Azure, которую можно создать в Azure. Перейдите в [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) и нажмите кнопку **Получить сейчас**. 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Нажмите кнопку **Продолжить**.

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Будет выполнен вход на портал Azure. Нажмите кнопку **Создать**.

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Выберите подписку, создайте группу ресурсов, выберите регион "Западная часть США 2", в котором будет доступна виртуальная машина, и введите пользователя и пароль. Запомните имя пользователя и пароль. Они вам потребуются позже. Щелкните **Просмотр и создание**.

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

После проверки нажмите кнопку **Создать**.

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Создание ресурсов занимает несколько минут. Щелкните **Перейти к ресурсу**.

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Подготовка виртуальной машины как устройства Azure IoT Edge 

В разделе "Поддержка и устранение неполадок" в левой области панели навигации щелкните "Серийная консоль".

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Должен отобразиться следующий экран.

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Нажмите клавишу ВВОД и введите имя пользователя и пароль в ответ на запрос, а затем снова нажмите клавишу ВВОД. 

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Чтобы выполнить команду от имени администратора или привилегированного пользователя, выполните команду: **sudo su –** .

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Проверьте версию среды выполнения Azure IoT Edge. Текущая общедоступная версия — 1.0.8.

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Установите редактор vim или используйте nano. 

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Правка файла config.yaml Azure IoT Edge

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Прокрутите вниз и закомментируйте часть строки подключения в файле YAML. 

**До:**

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**После** (Нажмите клавишу ESC, а затем — строчную букву a, чтобы начать редактирование.)

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Раскомментируйте часть симметричного ключа в файле YAML. 

**До:**

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**После:**

![Виртуальная машина Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Перейдите в IoT Central и получите идентификатор области, идентификатор устройства и симметричный ключ устройства Azure IoT Edge ![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png).

Перейдите в поле для Linux и замените идентификатор области, идентификатор регистрации на идентификатор устройства и симметричный ключ.

Нажмите клавишу **ESC** и введите **:wq!** . Нажмите клавишу **ВВОД**, чтобы сохранить изменения.

Перезапустите Azure IoT Edge для обработки изменений и нажмите клавишу **ВВОД**.

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Введите **iotedge list**. Через несколько минут вы увидите три развернутых модуля.

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Обозреватель устройств в IoT Central 

В IoT Central устройство будет переведено в состояние "Подготовлено".

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

На вкладке "Модули" отображается состояние устройства и модуля в IoT Central. 

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Свойства облака будут отображаться в форме (из шаблона устройства, созданного на предыдущих шагах). Введите значения и нажмите кнопку **Сохранить**. 

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Плитка на панели мониторинга

![Подключение устройства](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

Из этого руководства вы узнали, как выполнить следующие задачи:

* Добавление нового устройства Azure IoT Edge.
* Настройка устройства Azure IoT Edge для помощи в подготовке с использованием ключа SAS.
* Просмотр панелей мониторинга и разделов работоспособности модулей в IoT Central.
* Отправка команд в модуль, выполняющийся на устройстве Azure IoT Edge.
* Настройка свойств модуля, выполняющегося на устройстве Azure IoT Edge.

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как управлять устройствами Azure IoT Edge в IoT Central, перейдите к дальнейшему рекомендуемому шагу:

<!-- Next how-tos in the sequence -->

настройка прозрачного шлюза (перейдите к учебнику по ссылке, приведенной ниже).

> [!div class="nextstepaction"]
> [Учебник по настройке прозрачного шлюза](../../iot-edge/how-to-create-transparent-gateway.md)
