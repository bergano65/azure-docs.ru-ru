---
title: включить файл
description: включить файл
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3417bf0bd4ae1e0aa670f9fbfcc1fbbfeb372972
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471567"
---
Если вы хотите, чтобы пользователям было предложено провести второй фактор проверки подлинности до предоставления доступа, можно настроить Azure Multi-Factor Authentication (MFA). Вы можете настроить МИД на основе пользователя, или вы можете использовать МИД через [условный доступ](../articles/active-directory/conditional-access/overview.md).

* MFA на пользователя может быть включена без дополнительных затрат. При включении MFA на пользователя пользователю будет предложено проверить подлинность второго фактора в отношении всех приложений, связанных с арендатором Azure AD. [См. Вариант 1](#peruser) для шагов.
* Условный доступ позволяет более точно контролировать, как следует продвигать второй фактор. Он может позволить назначение МИД только VPN, и исключить другие приложения, связанные с арендатором Azure AD. [См. Вариант 2](#conditional) для шагов.