---
title: Различия между API управления и API службы
description: API-интерфейсы работают на разных уровнях пакетной службы Azure.
ms.topic: conceptual
ms.date: 02/26/2020
ms.custom: seodec18
ms.openlocfilehash: 33b8c5980aba1090155d6b136c6707e928666abf
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115403"
---
# <a name="service-level-and-management-level-apis"></a>API уровня обслуживания и уровня управления

Пакетная служба Azure имеет два набора интерфейсов API: один для уровня обслуживания и один для уровня управления. Именование часто аналогично, но они возвращают разные результаты. Если вам нужны журналы действий, необходимо использовать API управления. API уровня обслуживания обходят уровень управления ресурсами Azure и не регистрируются в журнале.


Для управления пакетной службой и пакетной службы предусмотрены интерфейсы API для пула, например. 
- Этот API для удаления пула предназначен непосредственно для учетной записи пакетной службы:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Этот API-интерфейс для https://docs.microsoft.com/rest/api/batchmanagement/pool/delete удаления пула предназначен для слоя Management.Azure.com.

