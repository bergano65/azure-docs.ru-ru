---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: cd18d71d26410767a2d3119c12a1339bdc84bd33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58114880"
---
1. На сервере конфигурации запустите файл CSPSConfigtool.exe. Он доступен через ярлык на рабочем столе и расположен в папке *install location*\home\svsystems\bin.
2. Выберите **Управление учетными записями** > **Добавить учетную запись**.

    ![Добавить учетную запись](./media/site-recovery-add-vcenter-account/credentials1.png)
3. В разделе **Сведения об учетной записи** добавьте учетную запись, которая будет использоваться для автоматического обнаружения.

    ![Сведения](./media/site-recovery-add-vcenter-account/credentials2.png)

    > [!Note]
   > Имя учетной записи появится на портале через 15 (или больше) минут. Чтобы выполнить обновление немедленно, выберите **Серверы конфигурации** > ***имя сервера*** > **Обновить сервер**.
