---
title: Необходимые компоненты виртуальной машины для Microsoft Azure | Azure Marketplace
description: Список предварительных требований, необходимых для публикации предложения виртуальной машины в Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/13/2019
ms.author: pabutler
ms.openlocfilehash: a657ceaacf3680de54dc0d639a3f1a0aff6a6a03
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824422"
---
# <a name="virtual-machine-prerequisites"></a>Предварительные требования к виртуальным машинам

В этой статье перечислены технические и бизнес-требования, которые необходимо выполнить перед публикацией предложения виртуальной машины в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Ознакомьтесь с [руководством по публикации предложения виртуальной машины](../../marketplace-virtual-machines.md), если вы еще этого не сделали.


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

- Необходимо зарегистрироваться в качестве издателя в облаке Marketplace.  Если вы еще не зарегистрированы, выполните действия, описанные в статье [Регистрация в качестве издателя в облаке Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > Следует использовать одну и ту же учетную запись регистрации Центра разработки для Майкрософт для входа на [портал Cloud Partner](https://cloudpartner.azure.com).
    > У вас должна быть только одна учетная запись Майкрософт для публикации предложений в Azure Marketplace. Эта запись не должна быть привязана к конкретным службам или предложениям.
    
- Ваша компания (или ее филиал) должна находиться в продаже по стране или регионе, поддерживаемой Azure Marketplace.  Текущий список стран и регионов см. в разделе [политики участия в Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Продукт должен иметь лицензию, совместимую с моделями выставления счетов, которые поддерживает Azure Marketplace.  Подробнее см. статью [Варианты выставления счетов за использование Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Вы должны предоставить клиентам техническую поддержку экономически обоснованным образом. Поддержка может быть платной, бесплатной или обеспечиваться посредством поддержки от сообщества.
- Вы несете ответственность за лицензирование программного обеспечения, а также любых зависимостей от стороннего программного обеспечения.
- Необходимо предоставить маркетинговые материалы, отвечающие критериям вашего предложения, соблюдение которых требуется для добавления приложения в Azure Marketplace и на портале Azure. <!-- TD: Meaning/links? -->
- Вам нужно принять условия [политики участия в Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) и соглашения для издателей.
- Вы должны соблюдать [условия использования Microsoft Azure веб-сайта](https://azure.microsoft.com/support/legal/website-terms-of-use/), заявления [о конфиденциальности Майкрософт](https://privacy.microsoft.com/privacystatement)и [соглашения о Microsoft Azure сертифицированной программе](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Дальнейшие действия

После выполнения этих предварительных требований можно [создать предложение виртуальной машины](./cpp-create-offer.md).
