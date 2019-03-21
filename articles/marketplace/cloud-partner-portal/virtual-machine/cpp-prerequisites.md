---
title: Предварительные требования к виртуальной машине для Microsoft Azure | Документация Майкрософт
description: Список предварительных требований, необходимых для публикации предложения виртуальной машины в Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010843"
---
# <a name="virtual-machine-prerequisites"></a>Предварительные требования к виртуальным машинам

В этой статье перечислены оба технических и бизнес-требований, которые необходимо выполнить, прежде чем можно публиковать предложения виртуальной Машины для [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Если вы еще не сделано, просмотрите [виртуальной машины предоставляют руководство по публикации](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Технические требования

Технические требования, необходимые для публикации решения в виртуальной машине, не вызывают затруднений.

- Необходимо иметь учетную запись Azure. Если ее нет, вы можете зарегистрироваться на [сайте Microsoft Azure](https://azure.microsoft.com).  
- Необходимо настроить среду поддержки разработки виртуальных машин для Windows или Linux.  Дополнительные сведения см. на сайте документации о виртуальной машине:
    - [Документация по виртуальным машинам Linux](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Документация по виртуальным машинам Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Бизнес-требования

Бизнес-требования включают процедурные, контрактные и юридические обязательства. 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Необходимо зарегистрироваться в качестве издателя в облаке Marketplace.  Если вы еще не зарегистрированы, выполните действия, описанные в статье [Регистрация в качестве издателя в облаке Marketplace](../../become-publisher.md).

    > [!NOTE]
    > Следует использовать одну и ту же учетную запись регистрации Центра разработки для Майкрософт для входа на [портал Cloud Partner](https://cloudpartner.azure.com).
    > У вас должна быть только одна учетная запись Майкрософт для публикации предложений в Azure Marketplace. Эта запись не должна быть привязана к конкретным службам или предложениям.
    
- Ваша компания (или ее филиал) должна находиться в стране, на территории которой могут осуществляться продажи в Azure Marketplace.  Текущий список этих стран см. в разделе [Политики участия в Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Продукт должен иметь лицензию, совместимую с моделями выставления счетов, которые поддерживает Azure Marketplace.  Подробнее см. статью [Варианты выставления счетов за использование Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Вы должны предоставить клиентам техническую поддержку экономически обоснованным образом. Поддержка может быть платной, бесплатной или обеспечиваться посредством поддержки от сообщества.
- Вы несете ответственность за лицензирование программного обеспечения, а также любых зависимостей от стороннего программного обеспечения.
- Необходимо предоставить маркетинговые материалы, отвечающие критериям вашего предложения, соблюдение которых требуется для добавления приложения в Azure Marketplace и на портале Azure. <!-- TD: Meaning/links? -->
- Вам нужно принять условия [политики участия в Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) и соглашения для издателей.
- Вы должны соблюдать [Microsoft Azure веб-сайта условия использования](https://azure.microsoft.com/support/legal/website-terms-of-use/), [заявление о конфиденциальности Microsoft](https://privacy.microsoft.com/privacystatement), и [соглашение по программе сертификации Azure Microsoft](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Дальнейшие действия

После выполнения этих предварительных требованиях, вы можете [Создание предложения виртуальной Машины](./cpp-create-offer.md).
