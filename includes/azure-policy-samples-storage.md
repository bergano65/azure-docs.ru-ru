---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/21/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 42e965b188db2b84579ab322fbe19781000dff7e
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894091"
---
## <a name="storage"></a>Хранилище

|  |  |
|---------|---------|
| [Допустимые номера SKU для учетных записей хранения и виртуальных машин](../articles/governance/policy/samples/allowed-skus-storage.md) | Требует, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU. Для обеспечения утвержденных номеров SKU используются встроенные политики. Вы можете указать массив утвержденных номеров SKU виртуальных машин и учетных записей хранения. |
| [Допустимые SKU учетной записи хранения](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Требует, чтобы учетные записи хранения использовали допустимые номера SKU. Нужно указать массив утвержденных номеров SKU. |
| [Запрет на распределение по холодным уровням доступа для учетных записей хранения](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Запрещает распределение по холодным уровням доступа для учетных записей хранения больших двоичных объектов.  |
| [Использование HTTPS-трафика для учетной записи хранения](../articles/governance/policy/samples/ensure-https-storage-account.md) | Требует, чтобы учетные записи хранения использовали HTTPS-трафик.  |
| [Обеспечение шифрования файлов для хранилища](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Требует, чтобы для учетных записей хранения было включено шифрование файлов.  |