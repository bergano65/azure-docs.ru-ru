---
title: включить файл
description: включить файл
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: d8ee7b61897a9718d7078fc16eddc177fefbff43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89147605"
---
 Машинное обучение Azure COMPUTE Clusters также поддерживает [управляемые удостоверения](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) для проверки подлинности доступа к ресурсам Azure без включения учетных данных в код. Существует два типа управляемых удостоверений.

* **Управляемое системой удостоверение** включается непосредственно в кластере машинное обучение Azure COMPUTE. Жизненный цикл удостоверения, назначенного системой, напрямую привязан к кластеру вычислений. Если кластер будет удален, Azure автоматически очистит учетные данные и удостоверение в Azure AD.
* **Назначаемое пользователем управляемое удостоверение** — это автономный ресурс Azure, предоставляемый через службу управляемых удостоверений Azure. Назначаемое пользователем управляемое удостоверение можно назначить нескольким ресурсам, и оно будет сохранено до тех пор, пока вы хотите.