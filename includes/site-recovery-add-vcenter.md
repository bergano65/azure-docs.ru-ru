---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8f7eae06947a40117f3952a0a5624c22df750b34
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50164575"
---
* В окне **Add vCenter** (Добавление vCenter) укажите понятное имя для узла vSphere или сервера vCenter, а также IP-адрес или полное доменное имя сервера. Если серверы VMware настроены на прослушивание запросов через другой порт, оставьте порт 443. Выберите учетную запись, чтобы подключиться к серверу VMware vCenter или vSphere ESXi. Последовательно выберите **ОК**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Если вы добавляете сервер VMware vCenter или узел VMware vSphere с учетной записью без прав администратора на сервере vCenter или сервере узла, предоставьте этой учетной записи права на доступ к следующим объектам: центр обработки данных, хранилище данных, папка, узел, сеть, ресурс, виртуальная машина и распределенный коммутатор vSphere. Кроме того, для сервера vCenter VMware следует активировать права представлений хранилища.
