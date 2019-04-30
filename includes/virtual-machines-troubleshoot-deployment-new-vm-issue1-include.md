---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123848"
---
## <a name="issue-custom-image-provisioning-errors"></a>Проблема Пользовательский образ; ошибки подготовки
Ошибки подготовки возникают при отправке или записи обобщенного образа виртуальной машины в качестве специализированного образа виртуальной машины (и наоборот). В первом случае возникнет ошибка времени ожидания подготовки, а во втором — сбой подготовки. Чтобы развернуть пользовательский образ без ошибок, необходимо обеспечить, чтобы тип изображения не менялся при записи.

В следующей таблице перечислены возможные комбинации обобщенного и специализированного образов, возможные типы ошибок, а также необходимые действия по их исправлению.

<!--Update_Description: wording update, update link-->