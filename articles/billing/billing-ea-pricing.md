---
title: Просмотр и загрузка цены на Azure в организации | Документация Майкрософт
description: Узнайте, как просмотреть и скачать цены или оценить затраты благодаря ценовой политике вашей организации.
services: ''
documentationcenter: ''
author: adpick
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d4c64433089f14d845cea8be9adf0ef13675cfd1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127639"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Просмотр и загрузка цены на Azure вашей организации

Клиенты с соглашением Enterprise (EA), Azure или [соглашении клиента Microsoft](#check-your-access-to-a-microsoft-customer-agreement) может просмотреть и скачать их стоимость на портале Azure. Если у вас есть соглашение Microsoft клиента, см. в разделе представления и загрузки, цены на соглашение с клиентом Microsoft.

## <a name="view-and-download-ea-pricing"></a>Просмотр и скачивание сведений о ценах EA

В зависимости от политик, которые администратор задал для вашей организации, только некоторые административные роли предоставляют доступ к сведениям о ценах EA вашей организации. Дополнительные сведения см. в описании [административных ролей в Azure при использовании Соглашения Azure Enterprise](billing-understand-ea-roles.md).

1. Войдите на [портал Azure](https://portal.azure.com/) с правами администратора организации.
1. Выполните поиск по **Управление затратами + выставление счетов**.

   ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. В учетной записи выставления счетов выберите **Использование+расходы**.

   ![Снимок экрана с пунктом "Использование+расходы" в разделе "Выставление счетов"](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Выберите ![Снимок экрана с поиском на портале Azure](./media/billing-ea-pricing/download-icon.png) **Скачать** для месяца.
1. В разделе **Прейскурант** выберите **Скачать CSV-файл**.

   ![Снимок экрана с кнопкой скачивания CSV-файла с прейскурантом](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="view-and-download-pricing-for-your-microsoft-customer-agreement"></a>Просмотр и загрузка, цены на соглашение с клиентом Microsoft

Будьте выставления счетов профиль владельца, участника, чтения или Управление счетами возможность просматривать и скачивать цены. Дополнительные сведения о ролях выставления счетов для соглашений клиентов Майкрософт, см. в разделе [выставления счетов профиля роли и задачи](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Скачайте прайс-листы для текущего расчетного периода

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. Выберите профиль выставления счетов. В зависимости от доступа необходимо сначала выбрать учетную запись выставления счетов.
1. В **Обзор** колонке найдите ссылки для скачивания под расходы с начала месяца.
1. Выберите **Azure прайс-лист**.
![Снимок экрана, показывающий загрузки из сводки](./media/billing-ea-pricing/open-pricing.png)

### <a name="download-price-sheets-for-billed-charges"></a>Скачайте прайс-листы для начисления оплаты

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выполните поиск по **Управление затратами + выставление счетов**.
1. Выберите профиль выставления счетов. В зависимости от доступа необходимо сначала выбрать учетную запись выставления счетов.
1. Выберите **Счета**.
1. В сетке счета найдите строку счета-фактуры, соответствующий прайс-лист, который вы хотите загрузить.
1. Нажмите кнопку с многоточием (`...`) в конце строки.
![Снимок экрана, показывающий кнопку с многоточием выбранные](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Если вы хотите посмотреть цены для служб в выбранной накладной, выберите **прайс-листа счета**.
1. Если вы хотите посмотреть цены для всех служб Azure для заданного периода выставления счетов, выберите **Azure прайс-лист**.

![Снимок экрана, показывающий контекстное меню с прайс-листы](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Оценить затраты с калькулятором цен Azure

Можно также использовать цены вашей организации для оценки затрат с калькулятором цен Azure.

1. Перейдите к [калькулятору цен Azure](https://azure.microsoft.com/pricing/calculator).
1. В правом верхнем углу выберите **Вход**.
1. В разделе **Programs and Offer** (Программы и предложения)  > **Программа лицензирования** выберите **Enterprise Agreement (EA)** (Соглашение Enterprise (EA)).
1. В разделе **Programs and Offer** (Программы и предложения)  > **Selected agreement** (Выбранное соглашение) выберите **Ничего не выбрано**.

    ![Снимок экрана с кнопкой скачивания CSV-файла с прейскурантом](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Выберите организацию.
1. Нажмите кнопку **Применить**.
1. Найдите и добавьте продукты в расчетную стоимость.
1. Отображаемые ориентировочные цены основаны на ценах для выбранной организации.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Проверьте доступ к соглашению клиента Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Дальнейшие действия

Если вы являетесь клиентом EA, см. в разделе:

- [Управление доступом к информации EA выставления счетов для Azure](billing-manage-access.md)
- [Просмотр и загрузка счете Microsoft Azure](billing-download-azure-invoice.md)
- [Просматривать и скачивать данные об использовании Microsoft Azure и затратах](billing-download-azure-daily-usage.md)
- [Расшифровка счета для клиентов с соглашением Enterprise](billing-understand-your-bill-ea.md)

Если у вас есть соглашения клиентов Майкрософт, см. в разделе:

- [Значение терминов в прайс-лист по договору клиента Microsoft](billing-mca-understand-pricesheet.md)
- [Просмотр и загрузка счете Microsoft Azure](billing-download-azure-invoice.md)
- [Просматривать и скачивать данные об использовании Microsoft Azure и затратах](billing-download-azure-daily-usage.md)
- [Просмотр и Загрузка налоговых документов для профиля выставления счетов](billing-mca-download-tax-document.md)
- [Основная информация о расходах на профиль выставления счетов](billing-mca-understand-your-bill.md)
