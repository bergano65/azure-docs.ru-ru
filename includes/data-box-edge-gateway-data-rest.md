---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67185929"
---
Для неактивных данных:

- Шифрование BitLocker XTS-AES 256 бит используется для защиты локальных данных.
- Ограничен доступ к данным, хранящимся в общих папках.

    - Клиенты SMB, которые обращаются к данным в общем ресурсе необходимые учетные данные пользователя, связанный с общей папкой. Эти учетные данные, определяются при создании общей папки.
    - IP-адреса, которые обращаются к общей папке NFS-клиенты должны быть добавлены при создании общей папки.