---
title: 'Azure AD Connect выполняет следующие функции: экземпляры службы синхронизации | Документация Майкрософт'
description: На этой странице приводятся специальные рекомендации для экземпляров Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: f340ea11-8ff5-4ae6-b09d-e939c76355a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/18/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca481d50efb99d6e36c66388192e9f27cd66bf45
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096102"
---
# <a name="azure-ad-connect-special-considerations-for-instances"></a>Azure AD Connect выполняет следующие функции: Специальные рекомендации для экземпляров
Azure AD Connect чаще всего используется с доступным во всем мире экземпляром Azure AD и Office 365. Но существуют и другие экземпляры, и они имеют другие требования к URL-адресам и прочие особенности.

## <a name="microsoft-cloud-germany"></a>Microsoft Cloud Germany
[Microsoft Cloud Germany](https://www.microsoft.de/cloud-deutschland) — это независимое облако, обслуживаемое управителем данными из Германии.

| URL-адреса для открытия на прокси-сервере |
| --- |
| \*.microsoftonline.de |
| \*.windows.net |
| +Списки отзыва сертификатов |

При входе в клиент Azure AD необходимо использовать учетную запись в домене onmicrosoft.de.

Функции, которые пока отсутствуют в Microsoft Cloud Germany:

* **Компонент обратной записи паролей** доступен в предварительной версии в Azure AD Connect версии 1.1.570.0 и более поздних.
* Другие службы Azure AD Premium недоступны.

## <a name="microsoft-azure-government-cloud"></a>Облако Microsoft Azure для государственных организаций
[Облако Microsoft Azure для государственных организаций](https://azure.microsoft.com/features/gov/) — это облако для правительства США.

Это облако поддерживается в более ранних выпусках DirSync. Начиная со сборки 1.1.180 Azure AD Connect поддерживается следующее поколение облака. Это поколение использует базирующиеся только в США конечные точки и имеет другой список URL-адресов для открытия на прокси-сервере.

| URL-адреса для открытия на прокси-сервере |
| --- |
| \*.microsoftonline.com |
| \*.microsoftonline.us |
| \*.windows.net (требуется для автоматического обнаружения клиента Azure AD для государственных организаций) |
| \*.gov.us.microsoftonline.com |
| +Списки отзыва сертификатов |

> [!NOTE]
> Начиная с версии AAD Connect 1.1.647.0 значение AzureInstance в реестре больше не требуется при условии, что на прокси-серверах открыта страница *.windows.net.

Функции, которые пока отсутствуют в облаке Microsoft Azure для государственных организаций:

* **Компонент обратной записи паролей** доступен в предварительной версии в Azure AD Connect версии 1.1.570.0 и более поздних.
* Другие службы Azure AD Premium недоступны.

## <a name="next-steps"></a>Дальнейшие действия
Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).
