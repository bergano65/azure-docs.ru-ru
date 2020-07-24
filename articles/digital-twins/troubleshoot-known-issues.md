---
title: Известные проблемы — Azure Digital двойников
description: Получите помощь в распознавании и устранении известных проблем с цифровым двойников Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044143"
---
# <a name="known-issues-in-azure-digital-twins"></a>Известные проблемы в службе Digital двойников

В этой статье содержатся сведения об известных проблемах, связанных с Azure Digital двойников.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"ошибка клиента 400: недопустимый запрос" в Cloud Shell

Команды в Cloud Shell могут периодически завершаться с ошибкой "400 ошибка клиента: неправильный запрос URL-адреса http://localhost:50342/oauth2/token ", за которым следует полная трассировка стека.

### <a name="troubleshooting-steps"></a>Действия по устранению неполадок

Это можно устранить, повторно выполнив `az login` команду и выполнив последующие шаги входа.

После этого вы сможете повторно выполнить команду.

### <a name="possible-causes"></a>Возможные причины

Это результат известной проблемы в Cloud Shell: [*Получение маркера от Cloud Shell периодически завершается с ошибкой 400 клиента: неправильный запрос*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о безопасности и разрешениях для Azure Digital двойников:
* [*Основные понятия: безопасность решений для цифровых двойников Azure*](concepts-security.md)