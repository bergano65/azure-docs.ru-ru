---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 48a3326dbe0e9eed4a5490e720248555586d189c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185339"
---
### <a name="to-take-a-backup"></a>Создание резервной копии

1. Откройте службу диспетчера устройств StorSimple. Выберите устройство в таблице со списком устройств, а затем щелкните **Все параметры**. В колонке **Параметры** выберите **Параметры > Управление > Политика архивации**.

    ![Добавление политики резервного копирования](./media/storsimple-8000-take-backup/step8takebu1.png)

2. В колонке **Политика архивации** щелкните **+ Add policy** (+ Добавить политику).

    ![Добавление политики резервного копирования](./media/storsimple-8000-take-backup/step8takebu2.png)

3. В колонке **Создать политику архивации** введите имя политики архивации длиной от 3 до 150 символов.

4. Выберите тома для резервного копирования. При выборе нескольких томов эти тома группируются вместе для создания отказоустойчивой резервной копии.

    ![Добавление политики резервного копирования](./media/storsimple-8000-take-backup/step8takebu4.png)

5. В колонке **Добавление первого расписания** выполните следующие действия:

    1. Выберите тип резервного копирования. Чтобы восстановление выполнялось быстрее, выберите **локальный** моментальный снимок. Чтобы обеспечить устойчивость данных, выберите **облачный** моментальный снимок.
    2. Укажите интервал резервного копирования в минутах, часах, днях или неделях.
    3. Выберите период удержания. Возможные периоды удержания зависят от частоты резервного копирования. Например, для политики ежедневного резервного копирования период удержания можно указать в неделях, а для политики ежемесячного резервного копирования — в месяцах.
    4. Выберите время и дату начала применения политики резервного копирования.
    5. Нажмите кнопку **ОК**, чтобы создать политику архивации.

        ![Добавление политики резервного копирования](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Щелкните **Создать**, чтобы приступить к созданию политики архивации. После ее создания вы получите соответствующее уведомление. Список политик архивации также обновится.
      
      ![Добавление политики резервного копирования](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      Теперь у вас есть политика архивации, на основе которой выполняется резервное копирование данных тома по расписанию.




