---
title: Включение Многофакторной идентификации для всех администраторов Azure
description: Инструкции по включению глобального администратора
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: 2dcd7f42d86000dd3b642c10f6d3db0b0d0fcb03
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56116343"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Обеспечение принудительной многофакторной идентификации (MFA) для администраторов подписок

Когда вы создаете администраторов, включая учетную запись глобального администратора, очень важно использовать методы очень строгой проверки подлинности.

При необходимости вы можете выполнять повседневное администрирование, назначая определенные роли администратора, например администратора Exchange или администратора паролей, для учетных записей пользователей ИТ-сотрудников.
Кроме того, включение [многофакторной идентификации Azure (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) для администраторов добавляет второй уровень безопасности при входе пользователей в систему и выполнении транзакций. Azure MFA также минимизирует риск того, что посторонние лица смогут получить доступ к данным с использованием скомпрометированных учетных данных.

Например, настройте принудительное использование Azure MFA для пользователей и телефонный звонок или текстовое сообщение в качестве способа проверки. Таким образом, злоумышленник не сможет выполнить компрометацию учетных данных, так как у него не будет доступа к телефону пользователя. Организации, не использующие дополнительные уровни защиты идентификации, более уязвимы к атакам путем кражи учетных данных, что может привести к компрометации данных.

Для тех организаций, которые предпочитают управлять всей системой аутентификации локально, мы можем предложить [сервер Многофакторной идентификации Azure](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server) (локальный сервер MFA). Этот метод позволит вам применить Многофакторную идентификацию, используя при этом локальный сервер MFA.

Для проверки того, у кого в вашей организации есть права администратора, используйте следующую команду PowerShell Microsoft Azure AD версии 2:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Включение MFA

Прежде чем продолжить, просмотрите как работает [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next).

При наличии у пользователей лицензий, которые предусматривают использование многофакторной проверки подлинности в Azure, вам не нужно включать Azure MFA. Для отдельных пользователей можно включить двухфакторную проверку подлинности. Лицензии, которые предусматривают использование Azure MFA:

- Многофакторная идентификация Azure
- Azure Active Directory Premium;
- Enterprise Mobility + Security.

## <a name="turn-on-two-step-verification-for-users"></a>Включение двухфакторной проверки подлинности для пользователей

Используйте одну из процедур, перечисленных в статье [Состояние пользователей в службе "Многофакторная идентификация Microsoft Azure"](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states), для начала работы с Azure MFA. Вы можете применять двухфакторную проверку подлинности для всех входов или создать политики условного доступа, чтобы требовать ее только там, где нужно.

