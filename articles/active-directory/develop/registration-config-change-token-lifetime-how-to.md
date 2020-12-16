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
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 41dd1ae62e67fe7a23ad62927e1a67989d684bdb
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516482"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Как изменить стандартное время существования токена для специально разработанного приложения

В этой статье показано, как настроить политику времени жизни маркера доступа с помощью Azure AD PowerShell. Azure AD Premium позволяет разработчикам приложений и администраторам клиентов настраивать время существования токенов, выпущенных для клиентов, не входящих в число конфиденциальных. Политики времени существования токенов настраиваются на уровне клиента или ресурсов, к которым осуществляется доступ.

> [!IMPORTANT]
> После мая 2020 клиенты больше не смогут настраивать время существования маркера обновления и сеанса.  Azure Active Directory прекращает учитывать существующую конфигурацию маркера обновления и сеанса в политиках после 30 января 2021 г. Вы по-прежнему можете настроить время существования маркера доступа после его устаревания. Дополнительные сведения см. [в статье настраиваемые времена жизни маркеров в Azure AD](./active-directory-configurable-token-lifetimes.md).
> Мы реализовали [возможности управления сеансом проверки подлинности](../conditional-access/howto-conditional-access-session-lifetime.md)   в условном доступе Azure AD. С помощью этой новой функции можно настроить время существования маркера обновления, установив частоту входа.  

Чтобы задать политику времени существования маркера доступа, необходимо скачать [модуль Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview).
Выполните команду **Connect-AzureAD-Confirm**.

Ниже приведен пример политики, которая требует от пользователей выполнять проверку подлинности в веб-приложении чаще. Эта политика задает время существования доступа к субъекту-службе для вашего приложения. Создайте политику и назначьте ее субъекту-службе. Вам потребуется также получить идентификатор объекта субъекта-службы.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Дальнейшие шаги

* См. в разделе [Настройка времени существования маркеров в Azure AD](./active-directory-configurable-token-lifetimes.md) чтобы узнать, как настроить время существования маркера, выданного Azure AD, включая то, как задать время существования маркеров для всех приложений в вашей организации, для многопользовательского приложения или для конкретной службы. 
* [Справочник по маркерам Azure AD](./id-tokens.md)
