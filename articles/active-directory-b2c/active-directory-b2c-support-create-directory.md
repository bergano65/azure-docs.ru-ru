---
title: Устранение неполадок при создании клиентов в Azure Active Directory B2C | Документация Майкрософт
description: В этой статье описаны проблемы и их решения при создании клиента Azure Active Directory или клиента Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: b8075e084481a9951c13896fa4db41613adc388f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160373"
---
# <a name="troubleshoot-creating-an-azure-active-directory-or-azure-active-directory-b2c-tenant"></a>Устранение неполадок при создании клиентов Azure Active Directory или Azure Active Directory B2C 

## <a name="create-an-azure-ad-tenant"></a>Создание клиента Azure AD
Если вам не удается создать клиент Azure Active Directory (Azure AD) с первого раза, попробуйте сделать это еще раз. Если проблема не исчезла, обратитесь в службу поддержки Azure.

## <a name="create-an-azure-ad-b2c-tenant"></a>Создание клиента Azure AD B2C
При возникновении проблем во время [создания клиента Azure Active Directory B2C](active-directory-b2c-get-started.md) попробуйте сделать следующее.

* Если клиент Azure AD B2C не отображается в списке клиентов, повторите попытку создания клиента.
* Если клиент Azure AD B2C не отображается в списке клиентов и появляется указанное ниже сообщение об ошибке, удалите клиента и создайте его заново.

    "Could not complete the creation of the B2C tenant 'contosob2c'. Please visit this [link](https://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) for more guidance." (Не удалось завершить создание клиента B2C contosob2c. Щелкните ссылку, чтобы получить дополнительные рекомендации).
* При удалении существующего клиента Azure AD B2C и его повторном создании с тем же доменным именем могут возникнуть известные проблемы. При создании клиента Azure AD B2C необходимо использовать другое доменное имя.
* Если ни одно из приведенных решений не помогло, обратитесь в службу поддержки Azure. Дополнительные сведения см. в статье [Azure Active Directory B2C: регистрация запросов в службу поддержки](active-directory-b2c-support.md).

