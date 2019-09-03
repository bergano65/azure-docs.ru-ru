---
title: Настройка именованных расположений в Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить именованные расположения.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.subservice: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1513dba19221e64d40551ffb13a5e9f33fd9858f
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127241"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Краткое руководство. Настройка именованных расположений в Azure Active Directory

С помощью именованных расположений можно пометить доверенные диапазоны IP-адресов в вашей организации. Именованные расположения в Azure AD используются для перечисленных ниже задач.
- Определение ложноположительных событий при [обнаружении рисков](concept-risk-events.md). Вход из надежного расположения снижает риски входа пользователя.   
- Настройка [условного доступа на основе расположения](../conditional-access/location-condition.md).

Из этого краткого руководства вы узнаете, как настроить именованные расположения в своей среде.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Клиент Azure AD. Зарегистрируйтесь на [бесплатную пробную версию](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Пользователь, который является глобальным администратором для этого клиента.
* Диапазон IP-адресов, которые одобрены и признаны надежными в вашей организации. Диапазон IP-адресов должен быть представлен в формате **CIDR**.

## <a name="configure-named-locations"></a>Настройка именованных расположений

1. Войдите на [портале Azure](https://portal.azure.com).

2. В области слева выберите **Azure Active Directory**, а затем **Условный доступ** в разделе **Безопасность**.

    ![Вкладка "Условный доступ"](./media/quickstart-configure-named-locations/entrypoint.png)

3. На странице **Условный доступ** в разделе **Именованные расположения** выберите **Новое расположение**.

    ![Именованное расположение](./media/quickstart-configure-named-locations/namedlocation.png)

6. Заполните форму на новой странице. 

   * В поле **Имя** введите имя именованного расположения.
   * В поле **Диапазон IP-адресов** укажите диапазон IP-адресов в формате CIDR.  
   * Нажмите кнопку **Создать**.
    
     ![Колонка "Создать"](./media/quickstart-configure-named-locations/61.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения можно найти в разделе

- [Что представляет собой условный доступ в Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- [What is the location condition in Azure Active Directory Conditional Access?](../conditional-access/location-condition.md) (Что собой представляют условия расположения при условном доступе в Azure AD?)
- [Отчет о событиях входа, представляющих риск, на портале Azure Active Directory](concept-risky-sign-ins.md)  
