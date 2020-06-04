---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998098"
---
Следующим шагом является настройка приложения для использования идентификатора вашей учетной записи и ключа учетной записи. Вы скопировали их в текстовый редактор, когда выполняли [настройку ресурсов пространственных привязок](#create-a-spatial-anchors-resource).

На панели **Проект** перейдите к `Assets\AzureSpatialAnchors.SDK\Resources`. Выберите `SpatialAnchorConfig`. Затем в области **Инспектор** введите `Account Key` как значение для `Spatial Anchors Account Key` и `Account ID` как значение для `Spatial Anchors Account Id`.

Затем откройте `SpatialAnchorManager.cs`. Найдите `CreateSessionAsync()` и добавьте следующую строку, подставив в нее полученный ранее домен учетной записи: `session.Configuration.AccountDomain = "MyAccountDomain";`. Этот код можно добавить непосредственно перед комментарием `// Configure authentication`.
