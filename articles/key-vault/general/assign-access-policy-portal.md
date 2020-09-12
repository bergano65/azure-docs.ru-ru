---
title: Назначение политики доступа Azure Key Vault (портал)
description: Использование портал Azure для назначения политики доступа Key Vault для субъекта-службы или удостоверения приложения.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: d16d14ff51cb9a7e71193d93c6755a8e4cb93ed0
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89481415"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Назначение политики доступа Key Vault с помощью портал Azure

Политика доступа Key Vault определяет, может ли заданный субъект-служба, а именно приложение или группа пользователей, выполнять различные операции с Key Vault [секретами](../secrets/index.yml), [ключами](../keys/index.yml)и [сертификатами](../certificates/index.yml). Политики доступа можно назначить с помощью портал Azure (в этой статье), [Azure CLI](assign-access-policy-cli.md)или [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Дополнительные сведения о создании групп в Azure Active Directory с помощью портал Azure см. в разделе [Создание базовой группы и добавление участников](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) .

## <a name="assign-an-access-policy"></a>Назначение политики доступа

1.  В [портал Azure](https://portal.azure.com)перейдите к ресурсу Key Vault. 

1.  В разделе **Параметры**выберите **политики доступа**, а затем щелкните **Добавить политику доступа**.

    ![Щелкните политики доступа, выберите добавить назначение ролей.](../media/authentication/assign-policy-portal-01.png)

1.  Выберите нужные разрешения в разделе **разрешения на сертификат**, **ключевые разрешения**и **секретные разрешения**. Можно также выбрать шаблон, содержащий сочетания общих разрешений:

    ![Указание разрешений политики доступа](../media/authentication/assign-policy-portal-02.png)

1. В разделе **Выбор субъекта**выберите пункт **нет выбранных** ссылок, чтобы открыть область выбора **субъекта** . Введите имя приложения или субъекта-службы в поле поиска, выберите соответствующий результат, а затем нажмите **кнопку Выбрать**.

    ![Выбор субъекта-службы для политики доступа](../media/authentication/assign-policy-portal-03.png)

    Если вы используете управляемое удостоверение для приложения, найдите и выберите имя самого приложения. (Дополнительные сведения об управляемых удостоверениях и субъектах-службах см. в разделе [Key Vault Authentication — удостоверение приложения и субъекты-службы](authentication.md#app-identity-and-security-principals).)
 
1.  Вернитесь в область **Добавить политику доступа** и выберите **Добавить** , чтобы сохранить политику доступа.

    ![Добавление политики доступа с назначенным субъектом-службой](../media/authentication/assign-policy-portal-04.png)

1. Вернитесь на страницу **политики доступа** , убедитесь, что политика доступа указана в разделе **текущие политики доступа**, а затем нажмите кнопку **сохранить**. Политики доступа применяются только после их сохранения.

    ![Сохранение изменений политики доступа](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Дальнейшие действия

- [Azure Key Vault security: Identity and access management](overview-security.md#identity-and-access-management) (Azure Key Vault security: управление удостоверениями и доступом)
- [Обеспечьте безопасность хранилища ключей](secure-your-key-vault.md).
- [Руководство разработчика Azure Key Vault](developers-guide.md)
- [Рекомендации по использованию Key Vault](best-practices.md)
