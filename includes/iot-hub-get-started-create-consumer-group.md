---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "66248934"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Добавление группы потребителей в Центр Интернета вещей

[Группы потребителей](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) предоставляют независимые представления в поток еде, которые позволяют приложениям и службам Azure самостоятельно использовать данные из одной и той же точки концентратора событий. В этом разделе вы добавляете группу потребителей в встроенную конечную точку концентратора IoT, которая позже используется в этом учебнике для вытягивания данных из конечных точек.

Чтобы добавить группу потребителей в Центр Интернета вещей, сделайте следующее:

1. Откройте Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. На левой панели выберите **встроенные конечные точки,** выберите **События** на правом стене и введите имя под **группами потребителей.** Нажмите кнопку **Сохранить**.

   ![Создание группы потребителей в Центре Интернета вещей](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
