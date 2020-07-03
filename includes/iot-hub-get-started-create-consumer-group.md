---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "66248934"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Добавление группы потребителей в Центр Интернета вещей

[Группы потребителей](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) предоставляют независимые представления в поток событий, позволяющие приложениям и службам Azure независимо потреблять данные из одной и той же конечной точки концентратора событий. В этом разделе вы добавите группу потребителей в встроенную конечную точку центра Интернета вещей, которая будет использоваться далее в этом руководстве для извлечения данных из конечной точки.

Чтобы добавить группу потребителей в Центр Интернета вещей, сделайте следующее:

1. Откройте Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. На левой панели выберите **встроенные конечные точки**, щелкните **события** на правой панели и введите имя в поле **группы потребителей**. Щелкните **Сохранить**.

   ![Создание группы потребителей в Центре Интернета вещей](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
