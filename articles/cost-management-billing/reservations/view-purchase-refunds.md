---
title: Просмотр данных о покупках резервирования и транзакциях возврата Azure
description: Узнайте, как просматривать данные о покупках резервирования и транзакциях возврата Azure.
author: yashesvi
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 988dd057834f6eb19dfd75a868c2893aefb61435
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87290763"
---
# <a name="view-reservation-purchase-and-refund-transactions"></a>Просмотр данных о покупках резервирования и транзакциях возврата

Существует несколько способов просмотра данных о покупках резервирования и транзакциях возврата. Вы можете использовать портал Azure, Power BI и REST API.

## <a name="view-reservation-transactions-in-the-azure-portal"></a>Просмотр транзакций резервирования на портале Azure

Администратор выставления счетов по регистрации учетных записей корпоративных пользователей или по клиентскому соглашению Майкрософт может просматривать транзакции резервирования в службе "Управление затратами и выставление счетов".

1. Войдите на [портал Azure](https://portal.azure.com).
1. Выполните поиск по фразе **Управление затратами + выставление счетов**.
1. Выберите **Транзакции резервирования**.
1. Чтобы отфильтровать результаты, выберите **Временной диапазон**, **Тип** или **Описание**.
1. Нажмите кнопку **Применить**.

[![Снимок экрана с транзакциями резервирования на портале Azure](./media/view-purchase-refunds/azure-portal-reservation-transactions.png)](./media/view-purchase-refunds/azure-portal-reservation-transactions.png#lightbox)

## <a name="view-reservation-transactions-in-power-bi"></a>Просмотр транзакций резервирования в Power BI

Администратор выставления счетов по регистрации учетных записей корпоративных пользователей или по клиентскому соглашению Майкрософт может просматривать транзакции резервирования с помощью приложения Power BI "Управление затратами".

1. Получите [приложение Power BI "Управление затратами"](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp).
1. Перейдите к отчету о покупках зарезервированных экземпляров.

[![Пример транзакций резервирования](./media/view-purchase-refunds/power-bi-reservation-transactions.png)](./media/view-purchase-refunds/power-bi-reservation-transactions.png#lightbox)

Чтобы получить дополнительные сведения, см. статью [Анализ затрат с помощью приложения Power BI Azure Cost Management для Соглашений Enterprise (EA)](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

## <a name="use-apis-to-get-reservation-transactions"></a>Использование API для получения транзакций резервирования

Пользователи, использующие Соглашение Enterprise и Клиентское соглашение Майкрософт, могут получить данные о транзакциях резервирования, воспользовавшись [API списка транзакций резервирования](https://docs.microsoft.com/rest/api/consumption/reservationtransactions/list).

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об управлении резервированием см. в разделе [Управление резервированиями в Azure](manage-reserved-vm-instance.md).
- Дополнительные сведения о резервировании в Azure см. по следующим ссылкам:
  - [Основные сведения о резервировании в Azure](save-compute-costs-reservations.md)
  - [Управление резервированиями в Azure](manage-reserved-vm-instance.md)
