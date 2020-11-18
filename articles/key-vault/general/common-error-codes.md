---
title: Распространенные коды ошибок для Azure Key Vault | Документация Майкрософт
description: Распространенные коды ошибок для Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: a543f03cb73d9eae8eaa81eeb3a37fd59e4e6a81
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685806"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Распространенные коды ошибок для Azure Key Vault

Коды ошибок, перечисленные в следующей таблице, могут быть возвращены операцией в хранилище ключей Azure.

| Код ошибки | Сообщение для пользователя |
|--|--|
| ваулталреадексистс |  Не удалось создать новое хранилище ключей с указанным именем, так как оно уже используется. Если вы недавно удалили хранилище ключей с этим именем, оно может по-прежнему быть в состоянии обратимого удаления. Вы можете проверить, существует ли состояние обратимого удаления [здесь](https://docs.microsoft.com/azure/key-vault/general/key-vault-recovery?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) . |
| ваултнаменотвалид |  Имя хранилища должно состоять из 24 символов и цифр и начинаться с буквы |
| AccessDenied |  Возможно, отсутствуют разрешения в политике доступа для выполнения этой операции. |
| форбидденбифиревалл |  Адрес клиента не является полномочным, и вызывающий объект не является доверенной службой. |
| ConflictError |  Выполняется запрос нескольких операций над одним и тем же элементом.  |
| регионнотсуппортед |  Указанный регион Azure не поддерживается для этого ресурса. |
| скунотсуппортед |  Указанный тип SKU не поддерживается для этого ресурса. |
| ResourceNotFound |  Указанный ресурс Azure не найден. |
| ResourceGroupNotFound | Указанная группа ресурсов Azure не найдена. |
| цертификатикспиред |  Проверьте срок действия сертификата и срок его действия. |


## <a name="next-steps"></a>Дальнейшие действия

- См. [руководство разработчика Azure Key Vault](developers-guide.md).
- См. дополнительные сведения о [проверке подлинности в хранилище ключей](authentication.md).
