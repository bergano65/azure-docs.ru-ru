---
title: Создание хранилищ служб восстановления
description: В этой статье вы узнаете, как создавать хранилища служб восстановления, в которых хранятся резервные копии и точки восстановления.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 144d8cdb870e12474dfc47784749b5f0e466f8bf
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273396"
---
# <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Хранилище Служб восстановления — это объект, который хранит резервные копии и точки восстановления, созданные с течением времени. В нем также содержатся политики архивации, связанные с защищенными виртуальными машинами.

Чтобы создать хранилище служб восстановления, сделайте следующее:

1. Войдите в свою подписку на [портале Azure](https://portal.azure.com/).

2. В меню слева выберите **Все службы**.

    ![Выбор "Все службы"](./media/backup-create-rs-vault/click-all-services.png)

3. В диалоговом окне **Все службы** введите **Службы восстановления**. Список ресурсов отфильтруется на основе введенных данных. В списке ресурсов выберите **Хранилища служб восстановления**.

    ![Ввод и выбор "Хранилища служб восстановления"](./media/backup-create-rs-vault/all-services.png)

    После этого отобразится список хранилищ служб восстановления в подписке.

4. На информационной панели **Хранилища служб восстановления** выберите **Добавить**.

    ![Добавление хранилища Служб восстановления](./media/backup-create-rs-vault/add-button-create-vault.png)

    Откроется диалоговое окно **Хранилища служб восстановления**. Укажите значения **имени**, **подписки**, **группы ресурсов** и **расположения**.

    ![Настройка хранилища Служб восстановления](./media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Имя**: введите понятное имя для идентификации хранилища. Это имя должно быть уникальным в пределах подписки Azure. Введите имя, которое содержит от 2 до 50 знаков. Оно должно начинаться с буквы и может содержать только буквы, цифры и дефисы.
   - **Подписка**: выберите подписку, которую нужно использовать. Если вы являетесь участником только одной подписки, будет отображено ее имя. Если неизвестно, какую подписку нужно использовать, оставьте подписку по умолчанию (или предлагаемую подписку). Вариантов будет несколько только в том случае, если рабочая или учебная учетная запись связана с несколькими подписками Azure.
   - **Группа ресурсов**: выберите имеющуюся группу ресурсов или создайте новую. Выберите **Использовать существующий**, чтобы просмотреть список доступных групп ресурсов в вашей подписке, а затем выберите ресурс из раскрывающегося списка. Чтобы создать группу ресурсов, щелкните **Создать** и укажите ее имя. Дополнительные сведения о группах ресурсов см. в [обзоре Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
   - В поле **Расположение** выберите географический регион для хранилища. Чтобы создать хранилище для защиты виртуальных машин, оно **должно** находиться в том же регионе, что и эти виртуальные машины.

      > [!IMPORTANT]
      > Если вы не уверены в расположении своей виртуальной машины, закройте диалоговое окно. Перейдите в список виртуальных машин на портале. Если у вас есть виртуальные машины в нескольких регионах, создайте хранилище Служб восстановления в каждом из них. Создайте хранилище в первом расположении, прежде чем создавать хранилище во втором. Не нужно указывать учетные записи хранения для хранения данных резервного копирования. Хранилище служб восстановления и служба Azure Backup обрабатывают это автоматически.
      >
      >

5. Когда все будет готово к созданию хранилища Служб восстановления, выберите **Создать**.

    ![Создание хранилища Служб восстановления](./media/backup-create-rs-vault/click-create-button.png)

    Для создания хранилища Служб восстановления может потребоваться некоторое время. Следите за уведомлениями о состоянии в области **Уведомления** в верхнем правом углу на портале. После создания хранилище появится в списке хранилищ Служб восстановления. Если вы не видите хранилища, выберите **Обновить**.

     ![Обновление списка хранилищ Служб восстановления](./media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-redundancy"></a>Установка избыточности хранилища

Azure Backup автоматически обрабатывает хранилище для хранилища. Необходимо указать способ репликации хранилища.

1. В колонке **Хранилища служб восстановления** щелкните новое хранилище. В разделе **Параметры** щелкните **свойства**.
2. В окне **Свойства**в разделе **Конфигурация архивации**щелкните **Обновить**.

3. Выберите тип репликации хранилища и нажмите кнопку **сохранить**.

     ![Настройка конфигурации для нового хранилища](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

   - Если вы используете Azure в качестве конечной точки основного хранилища резервных копий, продолжайте использовать **геоизбыточное** значение по умолчанию.
   - а если нет, — **локально избыточное** (это позволит снизить плату за хранилище Azure).
   - Дополнительные сведения о [географической](../storage/common/storage-redundancy-grs.md) и [локальной](../storage/common/storage-redundancy-lrs.md) избыточности.

> [!NOTE]
> Изменение **типа репликации хранилища** (локально избыточное или геоизбыточное) для хранилища служб восстановления необходимо выполнить перед настройкой резервных копий в хранилище. После настройки резервного копирования параметр изменить будет отключен, и вы не сможете изменить **тип репликации хранилища**.

## <a name="modifying-default-settings"></a>Изменение параметров по умолчанию

Мы настоятельно рекомендуем проверить параметры по умолчанию для **типа репликации хранилища** и **параметров безопасности** перед настройкой резервных копий в хранилище. 
* Для **типа репликации хранилища** по умолчанию задана **геоизбыточность**. После настройки резервного копирования параметр изменить будет отключен. Выполните следующие [действия](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy) , чтобы проверить и изменить параметры. 
* **Обратимое удаление** по умолчанию **включено** во вновь созданных хранилищах для защиты данных резервного копирования от случайных или вредоносных удалений. Выполните следующие [действия](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#disabling-soft-delete) , чтобы проверить и изменить параметры.


## <a name="next-steps"></a>Дополнительная информация

[Дополнительные сведения](backup-azure-recovery-services-vault-overview.md) Хранилища служб восстановления.
[Дополнительные сведения](backup-azure-delete-vault.md) Удаление хранилищ служб восстановления.
