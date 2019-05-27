---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159678"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Настройка удаленного управления на облачном устройстве

1. В службе диспетчера устройств StorSimple щелкните **Устройства**. Выберите облачное устройство в списке устройств, подключенных к службе.
    ![Выбор облачного устройства StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Чтобы открыть колонку **Параметры безопасности**, выберите **Параметры > Безопасность**.

     ![Параметры безопасности StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Перейдите к разделу **Удаленное управление**. Щелкните поле **Удаленное управление**.
     ![Удаленное управление StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. В колонке **Удаленное управление** выполните следующие действия:

    1. Проверьте, включен ли параметр **Разрешение удаленного администрирования**.
    2. По умолчанию выбрано подключение по HTTPS. Вы можете выбрать подключение по HTTP. Подключение по HTTP допустимо только в доверенных сетях. Убедитесь, что HTTP включен.
    3. На панели команд в верхней части колонки щелкните **... More** (...Дополнительно), а затем — **Скачать сертификат**, чтобы скачать сертификат удаленного управления. Необходимо указать расположение, в котором следует сохранить этот файл. Этот сертификат следует установить на клиентском компьютере или хост-компьютере, который используется для подключения к облачному устройству.

        ![Колонка "Удаленное управление"](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Щелкните **Сохранить**. При появлении запроса подтвердите изменения.