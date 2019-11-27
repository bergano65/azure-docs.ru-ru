---
title: Настройка стиля страницы на классическом портале разработчика в службе управления API Azure | Документация Майкрософт
description: Следуйте инструкциям из этого руководства, чтобы настроить стиль элементов на портале разработчика в службе управления API Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 779d1228393cdc88f88820d8a335cb2e32424c14
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176863"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Настройка стиля страниц для портала разработчика

Есть три распространенных способа настроить портал разработчика в службе управления API Azure:
 
* [Изменение содержимого статических страниц и элементов макета страницы.](api-management-modify-content-layout.md)
* Обновление стилей, которые используются для элементов страницы на портале разработчика (как описано в этом руководстве).
* [Изменение шаблонов, используемых для страниц, созданных порталом](api-management-developer-portal-templates.md) (например, документы по API, продукты, аутентификация пользователей и т. д.).

Из этой статьи вы узнаете, как настроить стиль элементов на страницах классического **портала разработчика** и просмотреть изменения.

![Настройка стиля](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Предварительные требования

+ Ознакомьтесь с [терминологией службы управления API в Azure](api-management-terminology.md).
+ Выполните инструкции из краткого руководства — [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md).
+ Также выполните инструкции из руководства [Импорт и публикация первого API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Настройка портала разработчика

1. Щелкните **Обзор**.
2. Нажмите кнопку **Портал разработчика (классический)** в верхней части окна **Обзор**.
3. В левом верхнем углу экрана вы увидите значок, которых состоит из двух кистей. Наведите на него указатель, чтобы открыть меню настройки портала.

    ![Настройка стиля](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. В меню выберите **Стили**, чтобы открыть панель настройки стилей.

    На странице отобразятся все элементы, которые можно настроить на панели **Стили**.
5. В поле **Change variable values to customize developer portal appearance** (Изменить значения переменных, чтобы настроить вид портала разработчика) введите "headings-color".

    На странице появится элемент **\@headings-color**. Эта переменная определяет цвет текста.

    ![Настройка стиля](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Щелкните поле для переменной **\@headings-color**. 
    
    Откроется раскрывающееся меню палитры.
7. В этом меню выберите новый цвет.

    > [!TIP]
    > Предварительный просмотр в реальном времени доступен для всех изменений. Индикатор хода выполнения отображается в верхней части панели настройки. Через несколько секунд цвет текста заголовка изменится на только что выбранный.

8. В меню панели настройки нажмите кнопку **Опубликовать** в нижнем левом углу.
9. Нажмите кнопку **Опубликовать настройки**, чтобы изменения стали доступны для всех.

## <a name="view-your-change"></a>просмотр изменений.

1. Перейдите на портал разработчика.
2. Вы увидите внесенные изменения.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в статье [Настройка портала разработчика в службе управления API Azure с помощью шаблонов](api-management-developer-portal-templates.md).