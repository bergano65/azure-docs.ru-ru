---
title: Как изменить стандартное время существования токена для специально разработанного приложения | Документы Майкрософт
description: Обновление политик времени существования токена для приложения, разрабатываемого в Azure AD
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
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c0700ff40063bfb7709b583f849eed179648306
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56203579"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Как изменить стандартное время существования токена для специально разработанного приложения

Azure AD Premium позволяет разработчикам приложений и администраторам клиентов настраивать время существования токенов, выпущенных для клиентов, не входящих в число конфиденциальных. Политики времени существования токенов настраиваются на уровне клиента или ресурсов, к которым осуществляется доступ.

1. Чтобы задать политику времени существования токена, нужно скачать [модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
1. Выполните команду **Connect-AzureAD-Confirm**.

    Ниже приведен пример политики, в котором задается токен обновления максимального возраста с одним фактором. Создайте политику: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```.

## <a name="next-steps"></a>Дополнительная информация

* См. в разделе [Настройка времени существования маркеров в Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) чтобы узнать, как настроить время существования маркера, выданного Azure AD, включая то, как задать время существования маркеров для всех приложений в вашей организации, для многопользовательского приложения или для конкретной службы. 
* [Справочник по токенам в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

