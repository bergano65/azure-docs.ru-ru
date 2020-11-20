---
title: Определение размера SAP HANA в Azure (крупные экземпляры) | Документация Майкрософт
description: Определение размера SAP HANA в Azure (крупные экземпляры).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1750f1d61028cb186b02251b551b7a798e1df9d6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967539"
---
# <a name="sizing"></a>Определение размера

В целом определение размера для крупных экземпляров HANA ничем не отличается от определения размера для HANA. Для существующих и развернутых систем необходимо перенести данные из других реляционных СУБД в HANA, так как SAP предоставляет ряд отчетов, которые выполняются в имеющихся системах SAP. Эти отчеты проверяют данные и определяют требования к памяти при перемещении базы данных в HANA. Прочтите следующие примечания SAP, чтобы получить дополнительные сведения о выполнении этих отчетов и информацию о последних исправлениях и версиях этих отчетов:

- [Примечание SAP #1793345 — изменение размера для SAP Suite в HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Примечание SAP #1872170-Suite в HANA и S/4. отчет о размере HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Примечание SAP #2121330 — часто задаваемые вопросы: SAP BW в отчете о размере HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [Примечание SAP #1736976 — отчет о размере для BW в HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [Примечание SAP #2296290 — новый отчет о размере для BW в HANA](https://launchpad.support.sap.com/#/notes/2296290)

Для новых развертываний доступно средство для определения требований к памяти для реализации программного обеспечения SAP на базе HANA — SAP Quick Sizer.

Требования к памяти для HANA повышаются по мере роста объема данных. Вам нужно следить за потреблением памяти, чтобы спрогнозировать будущие требования. В зависимости от требований к памяти вы можете определить подходящий SKU для крупных экземпляров HANA.

**Дальнейшие действия**
- См. раздел [Onboarding requirements](hana-onboarding-requirements.md) (Требования для внедрения)