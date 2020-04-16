---
title: Как выдавать разрешения на управляемое удостоверение в рабочем пространстве Azure Synapse
description: Статья, объясняя, как настроить разрешения для управляемых идентификационных данных в рабочем пространстве Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428021"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Разрешение на получение разрешения на идентировать рабочую область (предварительный просмотр)

Эта статья научит вас, как выдавать разрешения на управляемое удостоверение в рабочей области synapse Azure. Разрешения, в свою очередь, позволяют получить доступ к пулам S'L в рабочей области и учетной записи хранения ADLS gen2 через портал Azure.

>[!NOTE]
>Эта управляемая рабочая область будет называться управляемой идентификацией через остальную часть этого документа.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Предоставление разрешений на получение управляемой идентификации пулу S'L

Управляемая идентификация предоставляет разрешения пулам S'L в рабочей области. С разрешениями можно организовать конвейеры, выполняющие действия, связанные с пулом S'L. При создании рабочего пространства Azure Synapse с помощью портала Azure можно предоставить управляемые разрешения CONTROL на пулах S'L.

Выберите **сеть безопасности и сети** при создании рабочего пространства Azure Synapse. Затем выберите **Grant CONTROL для управляемой идентификации рабочего пространства в пулах S'L.**

![Разрешение CONTROL на пулы S'L](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Предоставление разрешений на получение управляемой идентификации на учетную запись хранения ADLS gen2

Для создания рабочего пространства Azure Synapse требуется учетная запись хранения ADLS gen2. Для успешного запуска пулов Spark в рабочем пространстве Azure Synapse управляемой идентификации Azure Synapse требуется роль *вкладчика хранилища данных в* этом аккаунте хранения. Оркестрирование трубопровода в Azure Synapse также выигрывает от этой роли.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Разрешение на получение управляемой идентификации при создании рабочего пространства

Azure Synapse попытается предоставить роль вкладчика хранилища данных управляемому итогу после создания рабочего пространства Azure Synapse с помощью портала Azure. Вы предоставляете данные учетной записи хранилища ADLS gen2 во вкладке **Основы.**

![Вкладка Основы в потоке создания рабочего пространства](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Выберите учетную запись хранения ADLS gen2 и файловую систему в **имени учетной записи** и **имени файловой системы.**

![Предоставление сведений о хранении aDLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Если создатель рабочего пространства также является **владельцем** учетной записи хранения ADLS gen2, то Azure Synapse присвоит роль *вкладчика хранилища данных для* управляемого итога. Вы увидите следующее сообщение ниже ввода учетной записи хранилища.

![Успешное назначение вкладчика хранилища данных](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Если создатель рабочего пространства не является владельцем учетной записи хранения ADLS gen2, то Azure Synapse не присваивает роль *вкладчика хранилища данных Blob.* Сообщение, появляющееся ниже сведения об учетной записи хранилища, уведомляет создателя рабочего пространства о том, что у него нет достаточных разрешений на предоставление роли *вкладчика хранилища данных Вашего* сведения.

![Неудачное назначение вкладчика хранилища данных](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Как говорится в сообщении, нельзя создавать пулы Spark, если *автор данных Хранилища Blob* не назначен управляемому итору.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Разрешение на получение разрешения на управление идентификацией после создания рабочего пространства

Во время создания рабочего пространства, если вы не присвоите *вкладчику Хранилища Blob Data* управляемой идентификации, то **владелец** учетной записи хранения ADLS gen2 вручную присваивает эту роль иждиц. Следующие шаги помогут вам выполнить ручное задание.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Шаг 1: Перейдите к учетной записи хранения ADLS gen2 на портале Azure

На портале Azure откройте учетную запись хранения ADLS gen2 и выберите **обзор** левой навигации. Вам нужно будет только назначить роль *вкладчика хранилища данных* на уровне контейнера или файловой системы. Выберите **Контейнеры**.  
![Обзор учетной записи хранения ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Шаг 2: Выберите контейнер

Управляемый имитатор должен иметь доступ к данным к контейнеру (файловой системе), который был предоставлен при создании рабочего пространства. Этот контейнер или файловую систему можно найти на портале Azure. Откройте рабочее пространство Azure Synapse на портале Azure и выберите вкладку **«Обзор»** из левой навигации.
![Контейнер для хранения данных ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Выберите тот же контейнер или файловую систему, чтобы предоставить роль *вкладчика хранилища данных* управляемому итоему.
![Выбор контейнеров для хранения aDLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Шаг 3: Перейдите к управлению доступом

Выберите **элемент управления доступом (IAM)**.

![Контроль доступа (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Шаг 4: Добавить новое назначение ролей

Выберите **+ Добавить**.

![Добавление нового назначения ролей](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Шаг 5: Выберите роль RBAC

Выберите роль **вкладчика хранилища данных.**

![Выберите роль RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Шаг 6: Выберите принцип безопасности Azure AD

Выберите **пользователя, группу или услугу Azure AD** из **доступа Assign для** отсева.

![Выберите директор по безопасности AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Шаг 7: Поиск управляемой личности

Имя управляемого удостоверения также является именем рабочего пространства. Поиск управляемой идентификации, введя имя рабочего пространства Azure Synapse в **Select.** Вы должны увидеть в списке управляемых удостоверений.

![Поиск управляемой идентификации](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Шаг 8: Выберите управляемую идентификацию

Выберите управляемую идентификацию **для выбранных членов.** Выберите **Сохранить,** чтобы добавить назначение роли.

![Выберите управляемую идентификацию](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Шаг 9: Проверить, что роль вкладчика хранилища данных присваивается управляемой идентификации

Выберите **контроль доступа (IAM),** а затем выберите **назначения ролей.**

![Проверка назначения ролей](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Вы должны увидеть, что ваша управляемая личность указана в разделе **Вкладчик данных хранилища с** ролью *вкладчика хранилища данных Blob,* назначенной ему. 
![Выбор контейнеров для хранения aDLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Дальнейшие действия

Подробнее об [иждипове управляемого рабочего пространства](./synapse-workspace-managed-identity.md)
