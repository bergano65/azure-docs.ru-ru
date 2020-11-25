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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026014"
---
 Машинное обучение Azure COMPUTE Clusters также поддерживает [управляемые удостоверения](../articles/active-directory/managed-identities-azure-resources/overview.md) для проверки подлинности доступа к ресурсам Azure без включения учетных данных в код. Существует два типа управляемых удостоверений.

* **Управляемое системой удостоверение** включается непосредственно в кластере машинное обучение Azure COMPUTE. Жизненный цикл удостоверения, назначенного системой, напрямую привязан к кластеру вычислений. Если кластер будет удален, Azure автоматически очистит учетные данные и удостоверение в Azure AD.
* **Назначаемое пользователем управляемое удостоверение** — это автономный ресурс Azure, предоставляемый через службу управляемых удостоверений Azure. Назначаемое пользователем управляемое удостоверение можно назначить нескольким ресурсам, и оно будет сохранено до тех пор, пока вы хотите.