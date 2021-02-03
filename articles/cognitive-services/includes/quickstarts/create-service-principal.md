---
title: Создание субъекта-службы Azure
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: dc5297a3abb913c7bef7c1669fa319d01a0c0203
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948039"
---
## <a name="create-an-azure-service-principal"></a>Создание субъекта-службы Azure

Чтобы приложение взаимодействовало с учетной записью Azure, необходима субъект-служба Azure для управления разрешениями. Выполните инструкции по [созданию субъекта-службы Azure](/powershell/azure/create-azure-service-principal-azureps?viewFallbackFrom=azps-3.3.0).

При создании субъекта-службы вы увидите, что у него есть значение секрета, идентификатор и идентификатор приложения. Сохраните идентификатор приложения и секрет во временное расположение для последующих шагов.
