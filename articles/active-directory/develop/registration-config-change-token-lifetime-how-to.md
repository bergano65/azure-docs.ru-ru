---
title: Изменение значений времени существования маркера по умолчанию для пользовательских приложений Azure AD | Документация Майкрософт
description: Обновление политик времени существования токена для приложения, разрабатываемого в Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: a603be6d57581541c0868b9f48a0bf9997cadd71
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962837"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Как изменить стандартное время существования токена для специально разработанного приложения

В этой статье показано, как настроить политику времени жизни маркера с помощью Azure AD PowerShell. Azure AD Premium позволяет разработчикам приложений и администраторам клиентов настраивать время существования токенов, выпущенных для клиентов, не входящих в число конфиденциальных. Политики времени существования токенов настраиваются на уровне клиента или ресурсов, к которым осуществляется доступ.

1. Чтобы задать политику времени существования токена, нужно скачать [модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Выполните команду **Connect-AzureAD-Confirm**.

    Ниже приведен пример политики, в котором задается токен обновления максимального возраста с одним фактором. Создайте политику: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```.

## <a name="next-steps"></a>Дальнейшие действия

* См. в разделе [Настройка времени существования маркеров в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) чтобы узнать, как настроить время существования маркера, выданного Azure AD, включая то, как задать время существования маркеров для всех приложений в вашей организации, для многопользовательского приложения или для конкретной службы. 
* [Справочник по токенам в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
