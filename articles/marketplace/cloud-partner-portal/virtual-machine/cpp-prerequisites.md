---
title: Предпосылки для виртуальных машин для Microsoft Azure Лазурный рынок
description: Список предварительных требований, необходимых для публикации предложения виртуальной машины в Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277622"
---
# <a name="virtual-machine-prerequisites"></a>Предварительные требования к виртуальным машинам

В этой статье перечислены как технические, так и бизнес-требования, которые необходимо выполнить, прежде чем вы сможете опубликовать предложение VM на [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/)  Если вы еще не сделали этого, просмотрите руководство по [публикации предложения виртуальной машины.](../../marketplace-virtual-machines.md)


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
    
- Ваша компания (или ее дочерняя компания) должна находиться в регионе продажи из страны,за которую поддерживается Azure Marketplace.  Для текущего списка этих стран/регионов [см.](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)
- Продукт должен иметь лицензию, совместимую с моделями выставления счетов, которые поддерживает Azure Marketplace.  Для получения дополнительной информации ознакомьтесь [с опциями выставления счетов в Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Вы должны предоставить клиентам техническую поддержку экономически обоснованным образом. Поддержка может быть платной, бесплатной или обеспечиваться посредством поддержки от сообщества.
- Вы несете ответственность за лицензирование программного обеспечения, а также любых зависимостей от стороннего программного обеспечения.
- Необходимо предоставить маркетинговые материалы, отвечающие критериям вашего предложения, соблюдение которых требуется для добавления приложения в Azure Marketplace и на портале Azure. <!-- TD: Meaning/links? -->
- Вы должны согласиться с условиями [политики участия Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) и Соглашения об издателях.
- Вы должны соблюдать [условия использования веб-сайта Microsoft Azure,](https://azure.microsoft.com/support/legal/website-terms-of-use/) [заявление о конфиденциальности Майкрософт](https://privacy.microsoft.com/privacystatement)и [соглашение о программе Microsoft Azure Certified Program.](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)


## <a name="next-steps"></a>Дальнейшие действия

После того как вы выполнили эти предпосылки, вы можете [создать свое предложение VM.](./cpp-create-offer.md)
