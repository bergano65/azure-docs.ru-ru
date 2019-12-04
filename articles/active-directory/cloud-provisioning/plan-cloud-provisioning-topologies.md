---
title: Azure AD Connect поддерживаемые топологии и сценарии для подготовки облака
description: В этом разделе описаны необходимые компоненты и требования к оборудованию для подготовки облака в облаке.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794001"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Azure AD Connect поддерживаемые топологии и сценарии для подготовки облака
В этой статье описываются различные локальные и Azure Active Directory (Azure AD) топологии, в которых используется облачная подготовка Azure AD Connect. Эта статья содержит только поддерживаемые конфигурации и сценарии.

> [!IMPORTANT]
> Корпорация Майкрософт не поддерживает изменение или эксплуатацию Azure AD Connect подготовки облака за пределами конфигураций или действий, которые формально документированы. Любая из этих конфигураций или действий может привести к нестабильному или неподдерживаемому состоянию Azure AD Connect подготовки облака. Для таких развертываний Майкрософт не предоставляет техническую поддержку.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Вопросы, которые следует помнить о всех сценариях и топологиях
Ниже приведен список сведений, которые следует учитывать при выборе решения.

- Пользователи и группы должны быть однозначно идентифицированы во всех лесах
- Сопоставление между лесами не выполняется при подготовке облака
- Пользователь или группа должны быть представлены только один раз во всех лесах.
- Привязка источника для объектов выбирается автоматически.  Он использует MS-DS-ConsistencyGuid, если он есть, в противном случае используется ObjectGUID.
- Нельзя изменить атрибут, используемый для привязки к источнику.



## <a name="multi-forest-single-azure-ad-tenant"></a>Один клиент Azure AD с несколькими лесами
![Топология для нескольких лесов и одного клиента](media/plan-cloud-provisioning-topologies/multi-forest.png)

Наиболее распространенная топология — это несколько лесов AD с одним или несколькими доменами и одним клиентом Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Существующий лес с Azure AD Connect, новый лес с подготовкой облачных сред
![Топология для одного леса и одного клиента](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Этот сценарий аналогичен сценарию с несколькими лесами, однако в нем используется существующая Azure AD Connect среда, а затем идет перенос в новый лес с помощью Azure AD Connect подготовки облачных ресурсов.  Пример этого сценария см. в разделе [учебник. существующий лес с одним клиентом Azure AD.](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Пилотное развертывание Azure AD Connect подготовки облака в существующем гибридном лесу AD
![топологию для одного леса и одного клиента](media/plan-cloud-provisioning-topologies/migrate.png) сценарий пилотного развертывания включает в себя как Azure AD Connect, так и Azure AD Connect подготовки облака в одном лесу, а также соответствующим образом расставить пользователей и группы. Примечание. объект должен находиться в области только одного из средств. 

Пример этого сценария см [. в статье пилотная Azure AD Connect подготовка облака в существующем синхронизированном лесу AD.](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Один лес, один клиент Azure AD
![Топология для одного леса и одного клиента](media/plan-cloud-provisioning-topologies/single-forest.png)

Простейшая топология — это один локальный лес с одним или несколькими доменами и одним клиентом Azure AD.  Пример этого сценария см [. в разделе Учебник. один лес с одним клиентом Azure AD.](tutorial-single-forest.md)

## <a name="next-steps"></a>Дальнейшие действия 

- [Что такое подготовка?](what-is-provisioning.md)
- [Что такое Azure AD Connect подготовки облака?](what-is-cloud-provisioning.md)

