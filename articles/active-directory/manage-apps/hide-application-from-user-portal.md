---
title: Скрытие приложения в пользовательском интерфейсе Azure Active Directory | Документация Майкрософт
description: Как скрыть приложение от пользователей на панелях доступа Azure Active Directory или в средствах запуска Office 365.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3dd98aa974f2adcd363c04c10b7a10cef6ca8ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824536"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Скрытие приложений от пользователей в Azure Active Directory

Инструкции о том, как скрыть приложения на панели MyApps пользователей или в средстве запуска Office 365. Когда приложение скрыто, у пользователей по-прежнему есть к нему доступ. 

## <a name="prerequisites"></a>Технические условия

Для того чтобы скрыть приложение на панели MyApps и в средстве запуска Office 365, необходимы права администратора приложения.

Чтобы скрыть все приложения Office 365, необходимы права глобального администратора.


## <a name="hide-an-application-from-the-end-user"></a>Скрытие приложения от пользователя
Чтобы скрыть приложение на панели MyApps и в средствах запуска приложений Office 365, выполните описанные ниже действия.

1.  Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор каталога.
2.  Выберите **Azure Active Directory**.
3.  Выберите **Корпоративные приложения**. Откроется колонка **Корпоративные приложения — Все приложения**.
4.  В разделе **Тип приложения** выберите **Корпоративные приложения**, если этот параметр еще не выбран.
5.  Найдите приложение, которое нужно скрыть, а затем щелкните его.  Откроется колонка обзора приложения.
6.  Щелкните **Свойства**. 
7.  Для вопроса **Видно пользователям?** выберите **Нет**.
8.  Выберите команду **Сохранить**.


## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Скрытие приложений Office 365 на панели MyApps

Чтобы скрыть все приложения Office 365 на панели MyApps, выполните следующие действия. Приложения по-прежнему будут видны на портале Office 365.

1.  Войдите на [портал Azure](https://portal.azure.com) как глобальный администратор каталога.
2.  Выберите **Azure Active Directory**.
3.  Выберите **Параметры пользователя**.
4.  В разделе **корпоративных приложений** щелкните **Manage how end users launch and view their applications** (Управление запуском и просмотром приложений для пользователей).
5.  Для параметра **Пользователи могут видеть приложения Office 365 только на портале Office 365** выберите **Да**.
6.  Выберите команду **Сохранить**.


## <a name="next-steps"></a>Дальнейшие действия
* [Просмотр всех моих групп](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Назначение корпоративному приложению пользователя или группы](assign-user-or-group-access-portal.md)
* [Удаление назначения пользователя или группы из корпоративного приложения](remove-user-or-group-access-portal.md)
* [Изменение имени или логотипа корпоративного приложения](change-name-or-logo-portal.md)

