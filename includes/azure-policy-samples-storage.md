---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/17/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: fe6c6d461fd6b99ce2ce57ebdd61a3f3e961489c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155422"
---
## <a name="storage"></a>Хранилище

|  |  |
|---------|---------|
| [Допустимые номера SKU для учетных записей хранения и виртуальных машин](../articles/governance/policy/samples/allowed-skus-storage.md) | Требует, чтобы учетные записи хранения и виртуальные машины использовали утвержденные номера SKU. Для обеспечения утвержденных номеров SKU используются встроенные политики. Вы можете указать массив утвержденных номеров SKU виртуальных машин и учетных записей хранения. |
| [Допустимые SKU учетной записи хранения](../articles/governance/policy/samples/allowed-storage-account-skus.md) | Требует, чтобы учетные записи хранения использовали допустимые номера SKU. Нужно указать массив утвержденных номеров SKU. |
| [Запрет на распределение по холодным уровням доступа для учетных записей хранения](../articles/governance/policy/samples/deny-cool-access-tiering.md) | Запрещает распределение по холодным уровням доступа для учетных записей хранения больших двоичных объектов.  |
| [Использование HTTPS-трафика для учетной записи хранения](../articles/governance/policy/samples/ensure-https-storage-account.md) | Требует, чтобы учетные записи хранения использовали HTTPS-трафик.  |
| [Обеспечение шифрования файлов для хранилища](../articles/governance/policy/samples/ensure-storage-file-encryption.md) | Требует, чтобы для учетных записей хранения было включено шифрование файлов.  |
| [Требование шифрования учетной записи хранения](../articles/governance/policy/samples/require-storage-account-encryption.md) | Требует, чтобы учетная запись хранения использовала шифрование больших двоичных объектов.  |