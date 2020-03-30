---
title: Завершение проверки ролей ресурсов Azure в PIM - Azure AD Документы Майкрософт
description: Узнайте, как пройти обзор доступа к функциям ресурсов Azure Privileged Identity Management в Active Directory Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e45249245aaab97070b7e774d4b6bab6827bdc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021983"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Завершите обзор функций ресурсов Ресурсов Azure в привилегированном управлении identity

Привилегированные администраторы ролей могут просматривать привилегированный доступ после [начала проверки доступа.](pim-resource-roles-start-access-review.md) Привилегированное управление идентификацией (PIM) в Active Directory Azure (Azure AD) автоматически отправляет электронное письмо, которое предлагает пользователям просмотреть их доступ. Если пользователь не получил электронное письмо, ему можно отправить [инструкции по выполнению проверки доступа](pim-resource-roles-perform-access-review.md).

После окончания периода проверки доступа или после завершения всеми пользователями самостоятельной проверки выполните шаги, описанные в этой статье, для управления проверкой и просмотра результатов.

## <a name="manage-access-reviews"></a>Управление проверками доступа

1. Перейдите на [портал Azure](https://portal.azure.com/). На панели мониторинга выберите службу **ресурсов Azure.**

2. Выберите ресурс.

3. Выберите раздел **Проверки доступа** на панели мониторинга.

    ![Ресурсы Azure - Список обзоров доступа с указанием роли, владельца, даты начала, даты окончания и статуса](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Выберите необходимую проверку доступа.

На странице детализации для обзора доступа существует несколько вариантов управления этим обзором. Существуют следующие варианты выбора.

![Варианты управления обзором - Остановка, сбросить, применить, удалить](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Остановить

Все отзывы о доступе имеют дату окончания. Выберите **Стоп,** чтобы закончить его раньше. Любые пользователи, которые не закончили свой обзор к этому времени не сможет закончить его после остановки обзора. Нельзя перезапустить проверку после ее остановки.

### <a name="reset"></a>Reset

Вы можете сбросить проверку доступа, чтобы удалить все соответствующие решения. После сбросить отзыв доступа все пользователи помечаются как не рассмотренные повторно.

### <a name="apply"></a>Применить

После завершения проверки доступа выберите **Apply** для реализации результатов обзора. Если в процессе проверки пользователю было отказано в доступе, то это приведет к удалению назначенной пользователю роли.  

### <a name="delete"></a>DELETE

Если проверка вам больше не нужна, удалите ее. Выберите **Удалить** yo удалить отзыв из службы управления привилегированными удостоверениями.

## <a name="results"></a>Результаты

На странице **Результаты** просмотрите и загрузите список результатов обзора.

![Результаты страницы список пользователей, результат, причина, рассмотрены, применяется, и применять результат](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Рецензенты

Просмотрите и добавьте рецензентов в существующие проверки доступа. Напомните рецензентам выполнить проверки.

![Рецензенты страницы список и имя пользователя основное](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Запуск проверки доступа для ролей ресурсов Azure в Привилегированном управлении identity](pim-resource-roles-start-access-review.md)
- [Провести обзор доступа к моим функциям ресурсов Azure в Привилегированном управлении идентификацией](pim-resource-roles-perform-access-review.md)
