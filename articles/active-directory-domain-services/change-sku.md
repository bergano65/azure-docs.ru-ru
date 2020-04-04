---
title: Измените SKU для службдомены Azure AD domain Services (ru) Документы Майкрософт
description: Узнайте, как уровень SKU для домена домена Azure AD Domain, если ваши бизнес-требования изменятся
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655576"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Измените SKU для существующего домена Azure AD Domain

В службах активных доменов Azure Active Directory Domain Services (Azure AD DS) доступная производительность и функции основаны на типе SKU. Эти различия включают частоту резервного копирования или максимальное количество односторонних исходящих лесных трастов (в настоящее время в предварительном просмотре). Вы выбираете SKU при создании управляемого домена, и вы можете переключать SKUs вверх или вниз по мере изменения потребностей вашего бизнеса после развертывания управляемого домена. Изменения в бизнес-требованиях могут включать необходимость более частого резервного копирования или создания дополнительных лесных трастов. Более подробную информацию об ограничениях и ценах различных СКУ можно узнать на [страницах Azure AD DS SKU][concepts-sku] и страницах [ценообразования Azure AD DS.][pricing]

В этой статье показано, как изменить SKU для существующего домена Azure AD DS, управляемого с помощью портала Azure.

## <a name="before-you-begin"></a>Подготовка к работе

Для завершения этой статьи необходимы следующие ресурсы и привилегии:

* Активная подписка Azure.
    * Если у вас нет подписки НаAz, [создайте учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Связанный с вашей подпиской клиент Azure Active Directory, синхронизированный с локальным или облачным каталогом.
    * Если потребуется, [создайте клиент Azure Active Directory][create-azure-ad-tenant] или [свяжите подписку Azure со своей учетной записью][associate-azure-ad-tenant].
* Управляемый домен доменных служб Azure Active Directory, включенный и настроенный в клиенте Azure AD.
    * При необходимости заполните учебник для [создания и настройки экземпляра служб доменов Active Directory Службы Azure.][create-azure-ad-ds-instance]

## <a name="sku-change-limitations"></a>SKU меняют ограничения

После развертывания домена Azure AD DS управляемый DS можно изменять SK-удеми вверх или вниз. Однако, если вы используете ресурсный лес (в настоящее время находится в предварительном просмотре) и создали односторонние исходящие лесные трасты из Azure AD DS в среде AD DS, для операции изменения SKU существуют некоторые ограничения. *Premium* и *Enterprise* SKUs определяют ограничение на количество трастов, которые вы можете создать. Вы не можете измениться на SKU с более низким максимальным лимитом, чем вы в настоящее время настроены.

Пример:

* Если вы создали два лесных траста на *Premium* SKU, вы не можете перейти к *Стандартному* SKU. *Стандартный* СКУ не поддерживает лесные трасты.
* Или, если вы создали семь трастов на *Premium* SKU, вы не можете изменить сявра в *Корпоративный* SKU. *Предприятие* SKU поддерживает максимум пять трастов.

Для получения дополнительной информации [Azure AD DS SKU features and limits][concepts-sku]об этих ограничениях см.

## <a name="select-a-new-sku"></a>Выберите новый SKU

Чтобы изменить SKU для управляемого домена Azure AD DS с помощью портала Azure, выполните следующие шаги:

1. В верхней части портала Azure ищите **службы домена Azure AD.** Выберите управляемый домен из списка, *например, aaddscontoso.com.*
1. В меню на левой стороне страницы Azure AD DS выберите **Настройки > SKU.**

    ![Выберите опцию меню SKU для домена Azure AD DS, управляемый Azure](media/change-sku/overview-change-sku.png)

1. Из меню выпадающих средств выберите пожеланный SKU для домена Azure AD DS. Если у вас есть ресурсный лес, вы не можете выбрать *Standard* SKU, так как лесные трасты доступны только на *Корпоративном* SKU или выше.

    Выберите SKU, который вы хотите, из выпадающего меню, а затем выберите **Сохранить.**

    ![Выберите необходимый SKU из меню выпадения на портале Azure](media/change-sku/change-sku-selection.png)

Изменение типа SKU может занять минуту или две.

## <a name="next-steps"></a>Дальнейшие действия

Если у вас есть ресурсный лес и вы хотите создать дополнительные трасты после изменения SKU, [см. Создать исходящий лесной траст в домене Azure AD DS (предварительный просмотр).][create-trust]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
