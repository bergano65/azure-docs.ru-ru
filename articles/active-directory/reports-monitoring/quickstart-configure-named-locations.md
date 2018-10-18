---
title: Настройка именованных расположений в Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить именованные расположения.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.component: report-monitor
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/23/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d99f077449529fb4a4a7f124fe1c0263d4113dee
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363451"
---
# <a name="quickstart-configure-named-locations-in-azure-active-directory"></a>Быстрое начало. Настройка именованных расположений в Azure Active Directory

С помощью именованных расположений можно пометить доверенные диапазоны IP-адресов в вашей организации. Именованные расположения в Azure AD используются для перечисленных ниже задач.
- Обнаружение ложноположительных событий в [событиях риска](concept-risk-events.md). Вход из надежного расположения снижает риски входа пользователя.   
- Настройка [условного доступа на основе расположения](../conditional-access/location-condition.md).

Из этого краткого руководства вы узнаете, как настроить именованные расположения в своей среде.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

* Клиент Azure AD. Зарегистрируйтесь на [бесплатную пробную версию](https://azure.microsoft.com/trial/get-started-active-directory/). 
* Пользователь, который является глобальным администратором для этого клиента.
* Диапазон IP-адресов, которые одобрены и признаны надежными в вашей организации. Диапазон IP-адресов должен быть представлен в формате **CIDR**.

## <a name="configure-named-locations"></a>Настройка именованных расположений

1. Войдите на [портале Azure](https://portal.azure.com).

2. В области слева выберите **Azure Active Directory**, а затем выберите **Условный доступ** в разделе **Безопасность**.

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

- [Условный доступ в Azure Active Directory](../active-directory-conditional-access-azure-portal.md)

- [Условия расположения при условном доступе в Azure AD](../conditional-access/location-condition.md)

- [Отчет о входах в систему, представляющих риск, на портале Azure AD](concept-risky-sign-ins.md).  
