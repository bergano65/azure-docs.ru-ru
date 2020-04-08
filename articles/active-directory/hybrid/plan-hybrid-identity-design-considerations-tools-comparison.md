---
title: 'Гибридная идентификация: сравнение инструментов интеграции каталогов | Документация Майкрософт'
description: На этой странице приведено исчерпывающее сравнение различных инструментов, которые можно использовать для интеграции каталогов.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 052d99a819aee415d5e7ad6dc00b8c786af0f636
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811100"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Сравнение инструментов интеграции каталогов гибридной идентификации
На протяжении многих лет инструменты интеграции каталогов развивались и расширялись.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) и [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) по-прежнему поддерживаются и в первую очередь обеспечивают синхронизацию между системами, на которые они находятся.   [FiM Windows Azure AD Connector](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) поддерживается как в FIM, так и в MIM, но не рекомендуется для новых развертываний - клиенты с предприимчивыми источниками, такими как Notes или SAP HCM, должны использовать MIM для заполнения служб active Directory Domain Services (AD DS), а затем использовать либо синхронизацию Azure AD Connect, либо облако Azure AD Connect для синхронизации с AD DD.
- [Синхронизация Azure AD Connect](how-to-connect-sync-whatis.md) включает компоненты и функциональность, ранее выпущенные в DirSync и Azure AD Sync, для синхронизации между лесами AD DS и Azure AD.  
- [Облачное обеспечение Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) — это новый агент Корпорации Майкрософт для синхронизации от AD DS до Azure AD, полезный для таких сценариев, как слияние и поглощение, когда нужные aD-леса компании изолированы от aD-лесов материнской компании.

Подробнее о различиях между синхронизацией Azure AD Connect и облачным обеспечением Azure AD Connect читайте в статье [Что такое подготовка облачных технологий Azure AD Connect?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Дальнейшие действия
Подробнее об [интеграции личных данных с помощью Active Directory Azure Active.](whatis-hybrid-identity.md)

