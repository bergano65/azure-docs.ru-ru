---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: fbb4e53e0047b9768a70c01aecfb7f31ae213b3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025498"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Добавление группы потребителей в Центр Интернета вещей

[Группы потребителей](../articles/event-hubs/event-hubs-features.md#event-consumers) предоставляют независимые представления в поток событий, что позволяет приложениям и службам Azure независимо использовать данные из одной конечной точки концентратора событий. В этом разделе вы добавите группу потребителей в встроенную конечную точку центра Интернета вещей, которая будет использоваться далее в этом руководстве для извлечения данных из конечной точки.

Чтобы добавить группу потребителей в Центр Интернета вещей, сделайте следующее:

1. Откройте Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. На левой панели выберите **встроенные конечные точки**, щелкните **события** на правой панели и введите имя в поле **группы потребителей**. Нажмите кнопку **Сохранить**.

   ![Создание группы потребителей в Центре Интернета вещей](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)