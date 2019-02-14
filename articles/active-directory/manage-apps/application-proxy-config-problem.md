---
title: Проблема при создании приложения прокси приложения | Документы Майкрософт
description: Сведения об устранении неполадок при создании приложений прокси приложения на портале администрирования Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7aad73331d06828a4bfe909dbb3b98350b1d0d6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56166657"
---
# <a name="problem-creating-an-application-proxy-application"></a>Проблема при создании приложения прокси приложения 

Ниже приведены некоторые наиболее распространенные проблемы, возникающие при создании приложения прокси приложения.

## <a name="recommended-documents"></a>Рекомендуемые документы 

Дополнительные сведения о создании приложения прокси приложения на портале администрирования см. в статье [Публикация приложений с помощью прокси приложения Azure AD](application-proxy-add-on-premises-application.md).

Если при создании приложения по инструкциям из этого документа возникает ошибка, см. описание ошибки для получения сведений о ее исправлении. Большинство сообщений об ошибках включают в себя предлагаемое исправление. 

## <a name="specific-things-to-check"></a>Что нужно проверить

Чтобы избежать распространенных ошибок, проверьте следующее:

-   Используется учетная запись администратора с разрешением на создание приложения прокси приложения.

-   Внутренний URL-адрес является уникальным.

-   Внешний URL-адрес является уникальным.

-   URL-адрес начинается с http или https и заканчивается на "/".

-   URL-адрес должен включать имя домена, а не IP-адрес.

Сообщение об ошибке при создании приложения должно отображаться в правом верхнем углу. Для просмотра сообщений об ошибках также можно выбрать значок уведомления.

   ![Уведомление](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Дополнительная информация
[Включение прокси приложения на портале Azure](application-proxy-add-on-premises-application.md)
