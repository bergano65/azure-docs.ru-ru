---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 05/20/2019
ms.openlocfilehash: c164433efc6a34a3a06676a3145feb18d3de80b9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248934"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>Добавление группы потребителей в Центр Интернета вещей

[Группы потребителей](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#event-consumers) предоставляют независимых представлений в поток событий, приложений и служб Azure независимо друг от друга получать данные от одну и ту же конечную точку концентратора событий. В этом разделе описано добавление группы потребителей центра Интернета вещей встроенную конечную точку, которая используется далее в этом руководстве для извлечения данных из конечной точки.

Чтобы добавить группу потребителей в Центр Интернета вещей, сделайте следующее:

1. Откройте Центр Интернета вещей на [портале Azure](https://portal.azure.com/).

2. На левой панели выберите **встроенные конечные точки**выберите **события** в правой области и введите имя в разделе **групп потребителей**. Щелкните **Сохранить**.

   ![Создание группы потребителей в Центре Интернета вещей](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
