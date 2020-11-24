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
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556688"
---
 Машинное обучение Azure COMPUTE Clusters также поддерживает [управляемые удостоверения](../articles/active-directory/managed-identities-azure-resources/overview.md) для проверки подлинности доступа к ресурсам Azure без включения учетных данных в код. Существует два типа управляемых удостоверений.

* **Управляемое системой удостоверение** включается непосредственно в кластере машинное обучение Azure COMPUTE. Жизненный цикл удостоверения, назначенного системой, напрямую привязан к кластеру вычислений. Если кластер будет удален, Azure автоматически очистит учетные данные и удостоверение в Azure AD.
* **Назначаемое пользователем управляемое удостоверение** — это автономный ресурс Azure, предоставляемый через службу управляемых удостоверений Azure. Назначаемое пользователем управляемое удостоверение можно назначить нескольким ресурсам, и оно будет сохранено до тех пор, пока вы хотите.