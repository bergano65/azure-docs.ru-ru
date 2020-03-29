---
title: Различия между AI-иупотечно-сервисными AIS и сервисными AIS - Azure Batch Документы Майкрософт
description: AA работают на различных уровнях службы Azure Batch.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 181515c0f497af8ffadcb909c13e51a40bfbf3b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672764"
---
# <a name="service-level-and-management-level-apis"></a>AA и уровень управления уровнем обслуживания

Пакет Azure имеет два набора AA, один для уровня обслуживания и один для уровня управления. Именование часто похоже, но они возвращают разные результаты. Если вам нужны журналы активности, вам необходимо использовать УПравутов управления. AA уровней обслуживания обходят слой управления ресурсами Azure и не регистрируются.


Управление пакетами и служба пакетов имеют AAP для пула, например. 
- Этот API для удаления пула предназначен непосредственно на пакетную учетную запись:https://docs.microsoft.com/rest/api/batchservice/pool/delete 

- Этот API для https://docs.microsoft.com/rest/api/batchmanagement/pool/delete удаления пула предназначен на management.azure.com слое.

