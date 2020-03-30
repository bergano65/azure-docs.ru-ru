---
title: Облачное подготовка Azure AD Connect поддерживает сяпологии и сценарии
description: В этой теме описаны предварительные требования и требования к оборудованию, облачные подготовки.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 386af46bbee623d37bc914d2ee9130c914c6c885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620870"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Облачное подготовка Azure AD Connect поддерживает сяпологии и сценарии
В этой статье описаны различные наместах и доступные для Azure Active Directory (Azure AD) топологии, которые используют облачное подключение Azure AD Connect. Эта статья включает только поддерживаемые конфигурации и сценарии.

> [!IMPORTANT]
> Корпорация Майкрософт не поддерживает изменение или эксплуатацию облака Azure AD Connect, не входящие в конфигурации или действия, которые официально задокументированы. Любая из этих конфигураций или действий может привести к несогласованному или неподдерживаемому состоянию облачного подключения Azure AD Connect. Для таких развертываний Майкрософт не предоставляет техническую поддержку.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Что нужно помнить обо всех сценариях и топологиях
Ниже приводится список информации, чтобы иметь в виду при выборе решения.

- Пользователи и группы должны быть однозначно идентифицированы во всех лесах
- Соответствие между лесами не происходит с облачным подготовкой
- Пользователь или группа должны быть представлены только один раз во всех лесах
- Исходный якорь для объектов выбирается автоматически.  Он использует ms-DS-ConsistencyGuid, если присутствует, в противном случае ObjectGUID используется.
- Нельзя изменять атрибут, используемый для якоря исходного кода.

## <a name="single-forest-single-azure-ad-tenant"></a>Один лес, один клиент Azure AD
![Топология для одного леса и одного клиента](media/plan-cloud-provisioning-topologies/single-forest.png)

Простейшая топология — это единый лесной лес с одним или несколькими доменами и единый арендатор Azure AD.  На примере этого сценария см. [Tutorial: Один лес с одним арендатором Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Многолесной, одинокий арендатор Azure AD
![Топология для многолесного и единственного арендатора](media/plan-cloud-provisioning-topologies/multi-forest.png)

Распространенной топологией является несколько лесов АД, с одним или несколькими доменами, и один арендатор Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Существующий лес с Azure AD Connect, новый лес с облачным обеспечением
![Топология для одного леса и одного клиента](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Этот сценарий похож на многолесной сценарий, однако этот включает в себя существующую среду Azure AD Connect, а затем внедряет новый лес с помощью облачного облака Azure AD Connect.  На примере этого сценария см. [Учебник: Существующий лес с одним арендатором Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Пилотирование облачного Облака Azure AD Connect в существующем гибридном лесу AD
![Топология для одного леса](media/plan-cloud-provisioning-topologies/migrate.png) и одного арендатора Сценарий пилотирования предполагает наличие в одном лесу и соответственно облачных проемов Azure AD Connect и ad-версии. ПРИМЕЧАНИЕ: Объект должен быть в области только в одном из инструментов. 

В примере этого сценария см. [Учебник: Протекающий облако Azure AD Connect в существующем синхронизированном лесу AD](tutorial-pilot-aadc-aadccp.md)



## <a name="next-steps"></a>Дальнейшие действия 

- [Что собой представляет подготовка?](what-is-provisioning.md)
- [Что такое подготовка облака Azure AD Connect?](what-is-cloud-provisioning.md)

