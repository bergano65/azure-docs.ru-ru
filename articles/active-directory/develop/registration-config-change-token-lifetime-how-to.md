---
title: Изменение значений времени существования маркера по умолчанию для пользовательских приложений Azure AD
description: Обновление политик времени существования токена для приложения, разрабатываемого в Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114937"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Как изменить стандартное время существования токена для специально разработанного приложения

В этой статье показано, как настроить политику времени жизни маркера с помощью Azure AD PowerShell. Azure AD Premium позволяет разработчикам приложений и администраторам клиентов настраивать время существования токенов, выпущенных для клиентов, не входящих в число конфиденциальных. Политики времени существования токенов настраиваются на уровне клиента или ресурсов, к которым осуществляется доступ.

1. Чтобы задать политику времени существования токена, нужно скачать [модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Выполните команду **Connect-AzureAD-Confirm**.

    Ниже приведен пример политики, в котором задается токен обновления максимального возраста с одним фактором. Создайте политику: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```.

## <a name="next-steps"></a>Дальнейшие действия

* См. в разделе [Настройка времени существования маркеров в Azure AD](./active-directory-configurable-token-lifetimes.md) чтобы узнать, как настроить время существования маркера, выданного Azure AD, включая то, как задать время существования маркеров для всех приложений в вашей организации, для многопользовательского приложения или для конкретной службы. 
* [Справочник по маркерам Azure AD](./id-tokens.md)