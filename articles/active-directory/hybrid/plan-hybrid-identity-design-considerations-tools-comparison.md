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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80811100"
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Сравнение инструментов интеграции каталогов гибридной идентификации
На протяжении многих лет инструменты интеграции каталогов развивались и расширялись.  


- [FIM](https://docs.microsoft.com/previous-versions/windows/desktop/forefront-2010/ff182370%28v%3dvs.100%29) и [MIM](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) по-прежнему поддерживаются и в основном обеспечивают синхронизацию между локальными системами.   [Соединитель FIM Windows Azure AD](https://docs.microsoft.com/previous-versions/mim/dn511001(v=ws.10)?redirectedfrom=MSDN) поддерживается как в FIM, так и в MIM, но не рекомендуется для новых развертываний. клиенты с локальными источниками, такими как примечания или SAP HCM, должны использовать MIM для заполнения служб домен Active Directory Services (AD DS), а затем использовать либо Azure AD Connect синхронизацию, либо Azure AD Connect подготовки облака для синхронизации из AD DS в Azure AD.
- [Azure AD Connect синхронизация](how-to-connect-sync-whatis.md) включает компоненты и функции, ранее выпущенные в DirSync и Azure AD Sync, для синхронизации между лесами AD DS и Azure AD.  
- [Azure AD Connect подготовка облака](../cloud-provisioning/what-is-cloud-provisioning.md) — это новый агент Майкрософт для синхронизации из AD DS с Azure AD. он полезен для таких сценариев, как слияние и приобретение, когда приобретенные леса AD изолированы от леса AD родительской компании.

Дополнительные сведения о различиях между Azure AD Connect синхронизацией и Azure AD Connect подготовки облачных ресурсов см. в статье [что такое Azure AD Connect подготовки облака?](../cloud-provisioning/what-is-cloud-provisioning.md)

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения об [интеграции локальных удостоверений с Azure Active Directory](whatis-hybrid-identity.md).

