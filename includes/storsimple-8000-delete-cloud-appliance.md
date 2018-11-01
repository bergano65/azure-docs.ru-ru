---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8de46fee4f93fffa669dc96ec5bb84e36842a055
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164777"
---
#### <a name="to-delete-a-cloud-appliance"></a>Удаление облачного устройства

1. Войдите на портал Azure.
2. Вы можете удалить только то деактивированное устройство, которое не содержит данных. Сначала удалите данные на устройстве, или же вы можете [выполнить отработку отказа данных](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md) в контейнерах томов на другое устройство. После удаления данных вы будете готовы отключить устройство.
3. На странице службы диспетчера устройств StorSimple щелкните **Устройства**, а затем выберите устройство. Щелкните его правой кнопкой мыши и выберите **Отключить**.
4. После деактивации устройства щелкните его правой кнопкой мыши и выберите **Удалить**.

    ![Выбор деактивированного устройства и его удаление щелчком](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. Введите имя устройства, чтобы подтвердить его удаление. После удаления устройства список устройств обновится.

    ![Подтверждение удаления](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. После удаления устройства вы получите уведомление.

    ![Уведомление об успешном удалении устройства](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. Список устройств обновляется, чтобы указать удаленное устройство.

    ![Обновленный список устройств](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
