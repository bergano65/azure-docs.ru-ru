---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: c10482029e6cfce7063d205161fed54030919c48
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159720"
---
#### <a name="to-stop-and-start-a-cloud-appliance"></a>Остановка и запуск облачного устройства

1. Чтобы остановить облачное устройство, перейдите к виртуальной машине данного устройства.
    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart1.png)

2. На панели команд щелкните **Остановить**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart2.png)

3. При появлении запроса на подтверждение нажмите кнопку **Да**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart3.png)

4. При остановке виртуальная машина освобождается. Во время остановки облачное устройство переходит в состояние **Отмена выделения**. После остановки облачного устройства его состояние будет **Остановлено (освобождено)**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart4.png)

5. После остановки виртуальной машины нажмите кнопку **Запустить** (кнопка становится доступной), чтобы инициировать запуск. После запуска облачного устройства его состояние будет **Запущено**.

    ![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart5.png)

Используйте следующие командлеты для остановки и запуска облачного устройства.

`Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

#### <a name="to-restart-a-cloud-appliance"></a>Перезапуск облачного устройства

Чтобы перезапустить облачное устройство, перейдите к виртуальной машине данного устройства. На панели команд нажмите кнопку **Перезапустить**. При появлении запроса подтвердите перезапуск. Когда облачное устройство будет готово к использованию, его состояние будет **Работает**.

![Виртуальная машина облачного устройства StorSimple](./media/storsimple-8000-stop-restart-cloud-appliance/sca-stop-restart6.png)

Используйте следующий командлет для перезапуска облачного устройства.

`Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

