---
title: Привязка учетной записи Azure к идентификатору партнера | Документация Майкрософт
description: Отслеживайте взаимодействие с клиентами Azure, привязав идентификатор партнера к учетной записи пользователя, используемой для управления ресурсами клиента.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 10/01/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 00b44b62e5ce666a8cd6d4b02270e6d9de6897e8
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719832"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Привязка идентификатора партнера к учетной записи Azure

Партнеры Microsoft предоставляют услуги, которые помогают клиентам достичь целей бизнеса и задач с помощью продуктов Microsoft. Действуя от имени клиента, управляющего, конфигурирующего и поддерживающего сервисы Azure, пользователи-партнеры должны иметь доступ к среде клиента. Используя Ссылку администратора партнера, партнеры могут связать идентификатор своей партнерской сети с учетными данными, используемыми для предоставления услуг.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Получение доступа от клиента

Перед привязкой идентификатора партнера клиент должен предоставить вам доступ к своим ресурсам Azure. Это можно сделать с помощью одного из следующих вариантов.

- **Гостевой пользователь**. Клиент может добавить вас в качестве гостевого пользователя и назначить роли компонента управления доступом на основе ролей (RBAC). Дополнительные сведения см. в статье [Что такое служба совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Учетная запись каталога**. Клиент может создать учетную запись пользователя для вас в своем каталоге и назначить любую роль RBAC.

- **Субъект-служба**. Клиент может добавить в каталог приложение или скрипт из вашей организации и назначить любую роль RBAC. Идентификатор приложения или сценария называют субъектом-службой.

## <a name="link-to-a-partner-id"></a>Привязка идентификатора партнера

Получив доступ к ресурсам клиента, привяжите идентификатор Microsoft Partner Network (MPN ID) к идентификатору пользователя или субъекта-службы с помощью портала Azure, PowerShell или Azure CLI. Привяжите идентификатор партнера в каждом клиенте.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Использование портала Azure для привязки нового идентификатора партнера

1. Перейдите на страницу [Link to a partner ID](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) (Привязка идентификатора партнера) на портале Azure.

2. Войдите на портал Azure.

3. Введите идентификатор партнера Майкрософт. Идентификатор партнера — это идентификатор [Microsoft Partner Network](https://partner.microsoft.com/) вашей организации.

   ![Снимок экрана страницы "Привязка идентификатора партнера"](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Чтобы привязать идентификатор партнера к другому клиенту, переключите каталог. В разделе **Переключение каталога** выберите свой каталог.

   ![Снимок экрана страницы "Переключение каталога"](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Привязка нового идентификатора партнера с помощью PowerShell

1. Установите модуль [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell.

2. Войдите в клиент пользователя с помощью учетной записи пользователя или субъекта-службы. Дополнительные сведения см. в разделе [Вход с помощью PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Привяжите новый идентификатор партнера. Идентификатор партнера — это идентификатор [Microsoft Partner Network](https://partner.microsoft.com/) вашей организации.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Получение связанного идентификатора партнера
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Обновление связанного идентификатора партнера
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Удаление связанного идентификатора партнера
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Использование Azure CLI для привязки нового идентификатора партнера
1. Установите расширение Azure CLI.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Войдите в клиент пользователя с помощью учетной записи пользователя или субъекта-службы. Дополнительные сведения см. в разделе [Вход с помощью Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Привяжите новый идентификатор партнера. Идентификатор партнера — это идентификатор [Microsoft Partner Network](https://partner.microsoft.com/) вашей организации.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Получение связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Обновление связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Удаление связанного идентификатора партнера
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Дополнительная информация

Присоединитесь к обсуждениям [сообщества партнеров Майкрософт](https://aka.ms/PALdiscussion), чтобы получать обновления или отправлять отзывы.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

**Кто может привязать идентификатор партнера?**

Любой пользователь из партнерской организации, который управляет ресурсами Azure клиента, может привязать идентификатор партнера к учетной записи.

**Можно ли изменить идентификатор партнера после его привязки?**

Да. Связанный идентификатор партнера можно изменить, добавить или удалить.

**Что делать, если у пользователя есть учетная запись в нескольких клиентах?**

Привязка идентификатора партнера к учетной записи выполняется для каждого клиента. Привяжите идентификатор партнера в каждом клиенте.

**Могут ли другие партнеры или клиенты изменить или удалить привязку к идентификатору партнера?**

Привязка создана на уровне учетной записи пользователя. Только вы можете изменить или удалить привязку к идентификатору партнера. Клиенты и другие партнеры не могут изменять привязку к идентификатору партнера.


**Какой идентификатор MPN следует использовать, если у моей компании их несколько?**

Учетные записи расположения партнеров и связанные с ними идентификаторы MPN должны использоваться для связывания с идентификатором партнера.  Дополнительные сведения о [партнерских учетных записях](https://docs.microsoft.com/partner-center/account-structure)

**Где я могу найти информацию, оказавшую влияние на отчетность по доходам для идентификатора связанного партнера?**

Отчеты о производительности облачных продуктов доступны для партнеров в партнерском центре в [Моя информационная панель ](https://partner.microsoft.com/membership/reports/myinsights). В качестве типа партнерской ассоциации необходимо выбрать Ссылку администратора партнера.

**Почему в отчетах не отображается клиент?**

Вы не видите клиента в отчетах по следующим причинам:

1. Связанная учетная запись пользователя не имеет [доступ на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/overview) ни на одном клиентском ресурсе Azure.

2. Абонентская плата Azure, к которой у пользователя есть доступ [Доступ на основе ролей](https://docs.microsoft.com/azure/role-based-access-control/overview), не имеет никакого использования.

**Работает ли идентификатор партнера с Azure Stack?**

Да, можно связать идентификатор партнера для Azure Stack.
