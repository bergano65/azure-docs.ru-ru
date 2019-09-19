---
title: Просмотр и скачивание сведений о ценах для вашей организации
description: Узнайте, как просматривать и скачивать цены, а также оценивать затраты с учетом цен вашей организации.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: a7f7da197a06dbbb730a7386882e534b8381cf9e
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491356"
---
# <a name="view-and-download-your-organizations-azure-pricing"></a>Просмотр и скачивание сведений о ценах для вашей организации

Клиенты с Соглашением Enterprise Azure (EA) или <bpt id="p1">[</bpt>Клиентским соглашением Майкрософт<ept id="p1">](#check-your-access-to-a-microsoft-customer-agreement)</ept> могут просматривать и загружать свои цены на портале Azure.

## <a name="ea-pricing"></a>Цены на EA

В зависимости от политик, которые администратор задал для вашей организации, только некоторые административные роли предоставляют доступ к сведениям о ценах EA вашей организации. Дополнительные сведения см. в описании [административных ролей в Azure при использовании Соглашения Azure Enterprise](billing-understand-ea-roles.md).

1. Войдите на [портал Azure](https://portal.azure.com/) с правами администратора организации.
1. Выполните поиск по фразе *Управление затратами + выставление счетов*.

   ![Снимок экрана, показывающий поиск на портале Azure](./media/billing-ea-pricing/portal-cm-billing-search.png)

1. В учетной записи выставления счетов выберите **Использование+расходы**.

   ![Снимок экрана с пунктом "Использование+расходы" в разделе "Выставление счетов"](./media/billing-ea-pricing/ea-pricing-usage-charges-nav.png)

1. Выберите ![Снимок экрана с поиском на портале Azure](./media/billing-ea-pricing/download-icon.png) **Скачать** для месяца.

1. В разделе **Прейскурант** выберите **Скачать CSV-файл**.

   ![Снимок экрана с кнопкой скачивания CSV-файла с прейскурантом](./media/billing-ea-pricing/download-ea-price-sheet.png)

## <a name="microsoft-customer-agreement-pricing"></a>Цены на Клиентское соглашение Майкрософт

Для просмотра и скачивания цен необходимо быть владельцем профиля выставления счетов, участником, читателем или менеджером по счетам. Дополнительные сведения о ролях выставления счетов для Клиентского соглашения Майкрософт см. в разделе [Роли и задачи профиля выставления счетов](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

### <a name="download-price-sheets-for-the-current-billing-period"></a>Скачивание прейскурантов за текущий расчетный период

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выполните поиск по фразе *Управление затратами + выставление счетов*.
1. Выберите профиль выставления счетов. В зависимости от уровня вашего доступа вам, возможно, сначала потребуется выбрать учетную запись выставления счетов.
1. В области <bpt id="p1">**</bpt>Обзор<ept id="p1">**</ept> найдите ссылки на скачивание под тарифами за текущий месяц.
1. Выберите **Прейскурант Azure**.
![![![![

### <a name="download-price-sheets-for-billed-charges"></a>Скачивание прейскурантов для оплаченных счетов

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выполните поиск по фразе *Управление затратами + выставление счетов*.
1. Выберите профиль выставления счетов. В зависимости от уровня вашего доступа вам, возможно, сначала потребуется выбрать учетную запись выставления счетов.
1. Выберите **Счета**.
1. В сетке "Счет" найдите строку счета, соответствующего прейскуранту, который необходимо загрузить.
1. Нажмите кнопку с многоточием (`...`) в конце строки.
![Снимок экрана, на котором показано выбранное многоточие](./media/billing-ea-pricing/billingprofile-invoicegrid.png)

1. Если вы хотите просмотреть цены для служб в выбранном счете, выберите **Прейскуранты счетов**.
1. Если вы хотите просмотреть цены для всех служб Azure за указанный период выставления счетов, выберите **Прейскурант Azure**.

![Снимок экрана, показывающий контекстное меню с прейскурантами](./media/billing-ea-pricing/contextmenu-pricesheet.png)

## <a name="estimate-costs-with-the-azure-pricing-calculator"></a>Оценка затрат с помощью калькулятора цен Azure

Вы можете также воспользоваться сведениями о ценах вашей организации для оценки затрат с помощью калькулятора цен Azure.

1. Перейдите к [калькулятору цен Azure](https://azure.microsoft.com/pricing/calculator).
1. В правом верхнем углу выберите **Вход**.
1. В разделе **Programs and Offer** (Программы и предложения)  > **Программа лицензирования** выберите **Enterprise Agreement (EA)** (Соглашение Enterprise (EA)).
1. В разделе **Programs and Offer** (Программы и предложения)  > **Selected agreement** (Выбранное соглашение) выберите **Ничего не выбрано**.

    ![Снимок экрана с кнопкой скачивания CSV-файла с прейскурантом](./media/billing-ea-pricing/ea-pricing-calculator-estimate.png)

1. Выберите организацию.
1. Нажмите кнопку **Применить**.
1. Поиск и добавление продуктов к вашей оценке.
1. Отображаемые ориентировочные цены основаны на ценах для выбранной организации.

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к Клиентскому соглашению Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="next-steps"></a>Дополнительная информация

Если вы являетесь клиентом с соглашением Enterprise, см. следующие сведения:

- [Управление доступом к сведениям о счетах EA для Azure](billing-manage-access.md)
- [Просмотр и скачивание счета Microsoft Azure](billing-download-azure-invoice.md)
- [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](billing-download-azure-daily-usage.md)
- [Сведения о счетах для Клиентского соглашения Enterprise](billing-understand-your-bill-ea.md)

Если у вас есть Клиентское соглашение Майкрософт см. следующие сведения.

- [Сведения об условиях в прейскуранте для клиентов с Клиентским соглашением Майкрософт](billing-mca-understand-pricesheet.md)
- [Просмотр и скачивание счета Microsoft Azure](billing-download-azure-invoice.md)
- [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](billing-download-azure-daily-usage.md)
- [View and download tax documents for your billing profile](billing-mca-download-tax-document.md) (Просмотр и скачивание налоговых документов для профиля выставления счетов)
- [Общие сведения об оплате счета для профиля выставления счетов](billing-mca-understand-your-bill.md)
