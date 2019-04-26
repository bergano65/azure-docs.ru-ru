---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 62bb01b39f33f801f1eb43d128083cff7e16830a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124148"
---
1. Войдите на [портал Azure](https://portal.azure.com) > **Службы восстановления**.
2. Последовательно выберите **Создать ресурс** > **Monitoring + Management** (Мониторинг и управление) > **Backup and Site Recovery**.
3. В поле **Имя**укажите понятное имя для идентификации хранилища. Если у вас есть несколько подписок, выберите нужную.
4. [Создайте новую группу ресурсов](../articles/azure-resource-manager/resource-group-template-deploy-portal.md) или выберите существующую. Укажите регион Azure. 
5. Чтобы быстро получить доступ к хранилищу на панели мониторинга, щелкните **Закрепить на панели мониторинга** > **Создать**.

   ![Новое хранилище](./media/site-recovery-create-vault/new-vault-settings.png)

   Новое хранилище появится в колонке **Панель мониторинга** > **Все ресурсы** и на основной странице **Хранилища служб восстановления**.
