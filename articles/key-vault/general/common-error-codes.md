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
ms.openlocfilehash: a36e15a56a5a4c8a637120ca730ae1da764d376d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422909"
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
| цертификатикспиред |  Проверьте срок действия сертификата и срок его действия. |


## <a name="next-steps"></a>Дальнейшие действия

- См. [руководство разработчика Azure Key Vault](developers-guide.md).
- См. дополнительные сведения о [проверке подлинности в хранилище ключей](authentication.md).
