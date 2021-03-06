---
title: Использование портал Azure для доступа к данным BLOB-объектов или очередей
titleSuffix: Azure Storage
description: Когда вы обращаетесь к данным большого двоичного объекта или очереди с помощью портал Azure, портал выполняет запросы к службе хранилища Azure, как описано в этой статье. Эти запросы к службе хранилища Azure могут пройти проверку подлинности и авторизоваться с помощью учетной записи Azure AD или ключа доступа к учетной записи хранения.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 89816e3640c0afad6290e77faa3904c691df4318
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892403"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>Использование портал Azure для доступа к данным BLOB-объектов или очередей

Когда вы обращаетесь к данным большого двоичного объекта или очереди с помощью [портал Azure](https://portal.azure.com), портал выполняет запросы к службе хранилища Azure, как описано в этой статье. Запрос к службе хранилища Azure можно авторизовать с помощью учетной записи Azure AD или ключа доступа к учетной записи хранения. Портал указывает, какой метод используется, и позволяет переключаться между ними, если у вас есть соответствующие разрешения.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Разрешения, необходимые для доступа к данным BLOB-объектов или очередей

В зависимости от того, как вы хотите авторизовать доступ к данным большого двоичного объекта или очереди в портал Azure, вам потребуются определенные разрешения. В большинстве случаев эти разрешения предоставляются с помощью управления доступом на основе ролей (RBAC). Дополнительные сведения о RBAC см. в статье [что такое управление доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md).

### <a name="account-access-key"></a>Ключ доступа к учетной записи

Чтобы получить доступ к данным большого двоичного объекта и очереди с помощью ключа доступа к учетной записи, необходимо назначить роль RBAC, которая включает действие RBAC **Microsoft. Storage/storageAccounts/listkeys/Action**. Эта роль RBAC может быть встроенной или настраиваемой ролью. Встроенные роли, поддерживающие **Microsoft. Storage, storageAccounts/listkeys/Action** , включают:

- Роль [владельца](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager
- Роль [участника](../../role-based-access-control/built-in-roles.md#contributor) Azure Resource Manager
- Роль [участника учетной записи хранения](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

При попытке доступа к данным большого двоичного объекта или очереди в портал Azure на портале сначала проверяется, была ли назначена роль с помощью **Microsoft. Storage/storageAccounts/listkeys/Action**. Если вы назначили роль с этим действием, портал использует ключ учетной записи для доступа к данным большого двоичного объекта и очереди. Если вы не назначили роль с этим действием, портал пытается получить доступ к данным с помощью учетной записи Azure AD.

> [!NOTE]
> Администратор службы ролей администратора и соадминистраторов классической подписки включает в себя эквивалент роли [владельца](../../role-based-access-control/built-in-roles.md#owner) Azure Resource Manager. Роль **владелец** включает все действия, включая **Microsoft. Storage/storageAccounts/listkeys/Action**, поэтому пользователь с одной из этих административных ролей также может обращаться к данным большого двоичного объекта и очереди с помощью ключа учетной записи. Дополнительные сведения см. в разделе [роли администратора классической подписки](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

### <a name="azure-ad-account"></a>Учетная запись Azure AD

Чтобы получить доступ к данным BLOB-объектов или очередей из портал Azure с помощью учетной записи Azure AD, для вас должны быть справедливы обе следующие инструкции:

- Вы назначили роль [читателя](../../role-based-access-control/built-in-roles.md#reader) Azure Resource Manager как минимум на уровне учетной записи хранения или выше. Роль **читателя** предоставляет наиболее ограниченные разрешения, но также приемлема другая роль Azure Resource Manager, которая предоставляет доступ к ресурсам управления учетной записью хранения.
- Вам была назначена встроенная или пользовательская роль, которая предоставляет доступ к данным большого двоичного объекта или очереди.

Назначение роли **читателя** или другое назначение роли Azure Resource Manager необходимо для того, чтобы пользователь мог просматривать ресурсы управления учетной записью хранения и переходить по ним в портал Azure. Роли RBAC, предоставляющие доступ к данным BLOB-объектов или очередей, не предоставляют доступ к ресурсам управления учетной записью хранения. Чтобы получить доступ к данным BLOB-объектов или очередей на портале, пользователю требуются разрешения для навигации по ресурсам учетной записи хранения. Дополнительные сведения об этом требовании см. [в разделе Назначение роли читателя для доступа к порталу](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access).

Встроенные роли, поддерживающие доступ к данным большого двоичного объекта или очереди, включают:

- [Владелец данных большого двоичного объекта службы хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner): для управления доступом POSIX для Azure Data Lake Storage 2-го поколения.
- [Участник данных BLOB-объектов хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): разрешения на чтение, запись и удаление для больших двоичных объектов.
- [Модуль чтения данных большого двоичного объекта хранилища](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader): разрешения только на чтение для больших двоичных объектов.
- [Участник данных очереди хранилища](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): разрешения на чтение, запись и удаление для очередей.
- [Модуль чтения очереди хранилища](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): разрешения только на чтение для очередей.
    
Пользовательские роли могут поддерживать различные сочетания тех же разрешений, которые предоставляются встроенными ролями. Дополнительные сведения о создании настраиваемых ролей RBAC см. в статьях [пользовательские роли для ресурсов Azure](../../role-based-access-control/custom-roles.md) и [Определение ролей для ресурсов Azure](../../role-based-access-control/role-definitions.md).

> [!NOTE]
> Вывод списка очередей с ролью администратора классической подписки не поддерживается. Чтобы получить список очередей, пользователю должны быть назначены роль **читателя** Azure Resource Manager, роль **чтения данных очереди хранилища** или роль **участника данных очереди хранилища** .

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Переход к BLOB-объектам или очередям на портале

Чтобы просмотреть данные большого двоичного объекта или очереди на портале, перейдите к **обзору** учетной записи хранения и щелкните ссылки для **больших двоичных объектов** или **очередей**. Кроме того, можно переходить к **службе BLOB-объектов** и **Служба очередей** разделов в меню. 

![Переход к данным большого двоичного объекта или очереди в портал Azure](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>Определение текущего метода проверки подлинности

При переходе к контейнеру или очереди портал Azure указывает, используется ли в настоящее время ключ доступа к учетной записи или учетная запись Azure AD для проверки подлинности.

В примерах, приведенных в этом разделе, показан доступ к контейнеру и его BLOB-объектам, но на портале отображается то же сообщение при доступе к очереди и ее сообщениям, а также список очередей.

### <a name="account-access-key"></a>Ключ доступа к учетной записи

Если проверка подлинности выполняется с помощью ключа доступа к учетной записи, вы увидите **ключ доступа** , указанный в качестве метода проверки подлинности на портале:

![В настоящее время доступ к данным контейнера осуществляется с помощью ключа учетной записи](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Чтобы переключиться на использование учетной записи Azure AD, щелкните ссылку, выделенную на изображении. Если у вас есть соответствующие разрешения через назначенные вам роли RBAC, вы сможете продолжать работу. Однако если у вас нет нужных разрешений, появится сообщение об ошибке следующего вида:

![Ошибка отображается, если учетная запись Azure AD не поддерживает доступ](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Обратите внимание, что в списке нет BLOB-объектов, если у вашей учетной записи Azure AD нет разрешений на их просмотр. Щелкните ссылку **Переключение на ключ доступа** , чтобы снова использовать ключ доступа для проверки подлинности.

### <a name="azure-ad-account"></a>Учетная запись Azure AD

Если вы выполняете проверку подлинности с помощью учетной записи Azure AD, вы увидите на портале **учетную запись пользователя Azure AD** , указанную в качестве метода проверки подлинности:

![В данный момент доступ к данным контейнера осуществляется с помощью учетной записи Azure AD](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Чтобы переключиться на использование ключа доступа к учетной записи, щелкните ссылку, выделенную на изображении. Если у вас есть доступ к ключу учетной записи, вы сможете продолжать работу. Однако если у вас отсутствует доступ к ключу учетной записи, вы увидите сообщение об ошибке следующего вида:

![Если у вас нет доступа к ключу учетной записи, отображается ошибка](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Обратите внимание, что в списке нет BLOB-объектов, если у вас нет доступа к ключам учетной записи. Щелкните ссылку **Переключение на учетную запись пользователя Azure AD** , чтобы снова использовать учетную запись Azure AD для проверки подлинности.

## <a name="next-steps"></a>Дальнейшие действия

- [Проверка подлинности доступа к BLOB-объектам и очередям Azure с помощью Azure Active Directory](storage-auth-aad.md)
- [Предоставление доступа к контейнерам и очередям Azure с RBAC в портал Azure](storage-auth-aad-rbac-portal.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC с помощью Azure CLI](storage-auth-aad-rbac-cli.md)
- [Предоставление доступа к BLOB-объектам Azure и создание очереди данных с использованием RBAC с помощью Powershell](storage-auth-aad-rbac-powershell.md)
