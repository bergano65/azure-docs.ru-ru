---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 62bb01b39f33f801f1eb43d128083cff7e16830a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66116038"
---
1. Войдите на [портал Azure](https://portal.azure.com) > **Службы восстановления**.
2. Последовательно выберите **Создать ресурс** > **Monitoring + Management** (Мониторинг и управление) > **Backup and Site Recovery**.
3. В поле **Имя**укажите понятное имя для идентификации хранилища. Если у вас есть несколько подписок, выберите нужную.
4. [Создайте новую группу ресурсов](../articles/azure-resource-manager/resource-group-template-deploy-portal.md) или выберите существующую. Укажите регион Azure. 
5. Чтобы быстро получить доступ к хранилищу на панели мониторинга, щелкните **Закрепить на панели мониторинга** > **Создать**.

   ![Новое хранилище](./media/site-recovery-create-vault/new-vault-settings.png)

   Новое хранилище появится в колонке **Панель мониторинга** > **Все ресурсы** и на основной странице **Хранилища служб восстановления**.
