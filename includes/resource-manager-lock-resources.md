---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/25/2018
ms.author: tomfitz
ms.openlocfilehash: 03e4053b65cf39101e8cb5d35ce439a759ec11d6
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66129042"
---
1. В колонке параметров для ресурса, группы ресурсов или подписки, которые необходимо заблокировать, выберите **Блокировки**.
   
      ![выбор блокировки](./media/resource-manager-lock-resources/select-lock.png)
2. Чтобы добавить блокировку, выберите **Добавить**. Если вы хотите создать блокировку на родительском уровне, выберите родительский ресурс. Выбранный ресурс наследует блокировку от родительского ресурса. Например, можно заблокировать группу ресурсов, чтобы применить блокировку ко всем ее ресурсам.
   
      ![добавление блокировки](./media/resource-manager-lock-resources/add-lock.png) 
3. Присвойте блокировке имя и уровень блокировки. При необходимости можно добавить примечания, описывающие блокировку.
   
      ![установка блокировки](./media/resource-manager-lock-resources/set-lock.png) 
4. Чтобы удалить блокировку, нажмите кнопку с многоточием (...) и из списка доступных вариантов выберите **Удалить** .
   
      ![удаление блокировки](./media/resource-manager-lock-resources/delete-lock.png) 

