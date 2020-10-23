---
title: Отслеживание кредитного баланса Azure для Клиентского соглашения Майкрософт
description: Узнайте, как проверить кредитный баланс Azure для Клиентского соглашения Майкрософт.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2019
ms.author: banders
ms.openlocfilehash: cf5772e9cf08eaa7f34acd59a9c96da6f20cd03d
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131417"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Отслеживание кредитного баланса Azure для Клиентского соглашения Майкрософт

На портале Azure или через REST API можно проверить остаток денег на счете Azure для вашей учетной записи выставления счетов в рамках Клиентского соглашения Майкрософт.

В учетной записи выставления счетов для Клиентского соглашения Майкрософт кредиты назначаются профилю выставления счетов. В каждом профиле выставления счетов есть деньги на счете, которые автоматически используются для погашения расходов по соответствующим счетам. Для просмотра кредитного баланса Azure профиля выставления счетов необходимо иметь роль "владелец", "участник", "читатель" или "менеджер счетов" для профиля выставления счетов, или роль "владелец", "участник", "читатель" для учетной записи выставления счетов. Подробные сведения о ролях см. в разделе [Сведения об административных ролях клиентских соглашений Майкрософт в Azure](understand-mca-roles.md).

> [!NOTE]
> Появление нового кредита на портале Azure может занять до 24 часов. Если вы получаете новый кредит и не видите его на портале, подождите 24 часа.

В этой статье рассматривается учетная запись выставления счетов для Клиентского соглашения Майкрософт. [Проверьте наличие доступа к Клиентскому соглашению Майкрософт](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance"></a>Проверьте свой кредитный баланс

### <a name="azure-portal"></a>[Портал Azure](#tab/portal)

1. Войдите на [портал Azure](https://portal.azure.com).

2. Выполните поиск по фразе **Управление затратами + выставление счетов**.

    ![Снимок экрана: поиск на портале по фразе "Управление затратами + выставление счетов".](./media/mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3. На странице областей выставления счетов выберите учетную запись выставления счетов, для которой вам нужно проверить остаток денег на счете. Учетная запись выставления счетов должна иметь тип **Клиентское соглашение Майкрософт**.

    ![Снимок экрана: страница "Области выставления счетов".](./media/mca-check-azure-credits-balance/list-of-scopes.png)

    > [!NOTE]
    >
    > На портале Azure сохраняется область выставления счетов, которую вы просматривали последней, и она отображается при следующем обращении к странице "Управление затратами + выставление счетов". Если вы посещали страницу "Управление затратами + выставление счетов" ранее, вы не увидите страницу областей выставления счетов. В этом случае убедитесь, что выбрана [правильная область](#check-access-to-a-microsoft-customer-agreement). В противном случае [переключите область](view-all-accounts.md#switch-billing-scope-in-the-azure-portal) и выберите учетную запись выставления счетов для Клиентского соглашения Майкрософт.

3. В меню слева щелкните **Методы оплаты** и выберите **Деньги на счете в Azure**.

   ![Снимок экрана: предполагаемый и текущий остаток на счете.](./media/mca-check-azure-credits-balance/mca-payment-methods.png)

4. На странице сведений о деньгах на счете в Azure присутствуют следующие разделы:

   #### <a name="balance"></a>Balance

   В этом разделе отображается сводка по остатку денег на счете в Azure.

   ![Снимок экрана с информацией об остатке денег на счете в профиле выставления счетов](./media/mca-check-azure-credits-balance/mca-credit-balance.png)

   | Термин               | Определение                           |
   |--------------------|--------------------------------------------------------|
   | Оценка баланса  | Оценочная сумма кредитов, которые у вас есть после рассмотрения всех выставленных счетов и незавершенных транзакций |
   | Текущий баланс    | Количество кредитов к последнему счету. Сюда не включены ожидающие транзакции |

   Когда ваш расчетный баланс снижается до 0, с вас взимается плата за все использование, в том числе за продукты, которые можно оплатить кредитами.

   #### <a name="credits-list"></a>Список кредитов

   В этом разделе отображается список с данными о деньгах на счете в Azure.

   ![Снимок экрана списка кредитов для профиля выставления счетов](./media/mca-check-azure-credits-balance/mca-credits-list.png)

   | Термин | Определение |
   |---|---|
   | Источник | Источник приобретения кредита |
   | Дата начала | Дата приобретения кредита |
   | Срок действия | Дата истечения срока действия кредита |
   | Текущий баланс | Баланс последнего счета |
   | Исходная сумма | Исходный объем кредита |
   | Состояние | Текущее состояние кредита. Состояние может быть "Активно", "Использовано", "Просрочено" или "С истекшим сроком действия" |

   #### <a name="transactions"></a>Transactions

   В разделе транзакций отображаются все транзакции, которые влияли на остаток денег на счете.

   ![Снимок экрана с информацией о транзакциях для профиля выставления счетов](./media/mca-check-azure-credits-balance/mca-credits-transactions.png)

   | Термин | Определение |
   |---|---|
   | Дата транзакции | Дата выполнения транзакции. |
   | Описание | Описание транзакции. |
   | Сумма| Сумма транзакции |
   | Balance | Остаток на счете после транзакции. |

    > [!NOTE]
    >
    > Если на странице методов оплаты не отображаются деньги на счете в Azure, значит у вас не осталось денег на счете или вы выбрали не ту область. Выберите учетную запись выставления счетов, в которую входит один или несколько профилей выставления счетов с остатком денег на счете. Сведения о том, как менять области, см. в разделе [Переключение области выставления счетов на портале Azure](view-all-accounts.md#switch-billing-scope-in-the-azure-portal).

5. Если вы просматриваете сведения о деньгах на счете в Azure по области учетной записи выставления счетов, в которую входят несколько профилей выставления счетов, на странице со сведениями о деньгах на счете в Azure отобразится таблица со сводной информацией об этих деньгах по каждому профилю выставления счетов. Выберите в этом списке профиль выставления счетов, затем методы оплаты и деньги на счете в Azure, чтобы просмотреть сведения о профиле выставления счетов.

    ![Снимок экрана со списком с данными о деньгах на счете в профиле выставления счетов](./media/mca-check-azure-credits-balance/mca-account-credit-list.png)

### <a name="rest-api"></a>[REST API](#tab/rest)

Вы можете использовать API [Выставления счетов Azure](/rest/api/billing/) и [Потребление](/rest/api/consumption/), чтобы проверить кредитный баланс для своего платежного счета программным способом.

В приведенных ниже примерах используются REST API. В настоящее время PowerShell и Azure CLI не поддерживаются.

### <a name="find-billing-profiles-you-have-access-to"></a>Поиск профилей выставления счетов, к которым у вас есть доступ

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
Ответ API возвращает список учетных записей выставления счетов и их профилей выставления счетов.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Используйте свойство `displayName` профиля выставления счетов, чтобы определить профиль выставления счетов, для которого необходимо проверить остаток денег на счете. Скопируйте `id` профиля выставления счетов. Например, если вы хотите проверить кредитный баланс для профиля выставления счетов **Development** (Разработка), скопируйте ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="get-azure-credit-balance"></a>Получение кредитного баланса Azure

Выполните следующий запрос, заменив `<billingProfileId>` на идентификатор `id`, который вы скопировали на первом шаге (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

Ответ API возвращает оценочный и текущий баланс профиля выставления счетов.

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| Имя элемента  | Описание                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Предполагаемая сумма кредитов, которые у вас есть после рассмотрения всех выставленных счетов и незавершенных транзакций. |
| `currentBalance`   | Количество кредитов по состоянию на момент выставления последнего счета. Сюда не включены незавершенные транзакции.    |
| `pendingCreditAdjustments`      | Корректировки, как возврат денег для которых еще не выставлен счет.  |
| `expiredCredit`      |  Кредит, срок действия которого истек с момента последнего счета.  |
| `pendingEligibleCharges`  | Расходы на кредит, для которых еще не выставлен счет.   |

### <a name="get-list-of-credits"></a>Получение списка кредитов

Выполните следующий запрос, заменив `<billingProfileId>` на идентификатор `id`, который вы скопировали на первом шаге (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```).

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
Ответ API возвращает списки кредитов Azure для профиля выставления счетов.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| Имя элемента  | Описание                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Первоначальная сумма кредита. |
| `closedBalance`   | Баланс до выставления последнего счета.    |
| `source`      | Источник, определяющий, кто и в какой способ получил кредит. |
| `startDate`      |  Дата активации кредита.  |
| `expirationDate`  | Дата истечения срока действия кредита.   |
| `poNumber`  | Номер заказа на покупку, по которому был выставлен счет в кредитах.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Получение транзакций, на которые распространяется кредитный баланс

Выполните следующий запрос, заменив `<billingProfileId>` на идентификатор `id`, который вы скопировали на первом шаге (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Чтобы получить транзакции за требуемый период времени, необходимо передать **startDate** и **endDate**.

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
Ответ API возвращает все транзакции, на которые распространяется кредитный баланс профиля выставления счетов.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| Имя элемента  | Описание                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Дата выполнения транзакции. |
| `description` | Описание транзакции. |
| `adjustments`   | Корректировки кредита для транзакции.    |
| `creditExpired`      | Сумма кредита, срок действия которого истек. |
| `charges`      |  Плата за транзакцию.  |
| `closedBalance`  | Баланс после транзакции.   |
| `eventType`  | Тип транзакции.   |
| `invoiceNumber`  | Номер счета, по которому оплачивалась транзакция. Он будет пустым для незавершенной транзакции.   |

---

## <a name="how-credits-are-used"></a>Использование кредитов

В учетной записи выставления счетов для клиентов с Клиентским соглашением Майкрософт профили выставления счетов используются для управления счетами и методами оплаты. Для каждого профиля выставления счетов создается ежемесячный счет, а для оплаты счета используются методы оплаты.

Вы назначаете кредиты, приобретенные в профиле выставления счетов. При создании счета для профиля выставления счетов, кредиты автоматически применяются к общим расходам для расчета суммы, которую необходимо оплатить. Оставшуюся сумму вы оплачиваете другим методом, например чеком или банковским переводом.

## <a name="products-that-arent-covered-by-azure-credits"></a>Продукты, которые не охватываются кредитами Azure

 Ваши кредиты Azure не охватывают продукты, приведены ниже. Вы платите за использование этих продуктов независимо от вашего баланса:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- Зарегистрированные пользователи
- Openlogic
- Зарегистрированный пользователь Службы предоставления прав на удаленный доступ XenApp Essentials
- Ubuntu Advantage
- Visual Studio Enterprise — ежемесячно
- Visual Studio Enterprise — ежегодно
- Visual Studio Professional — ежемесячно
- Visual Studio Professional — ежегодно
- Продукты в Azure Marketplace
- Планы поддержки Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к Клиентскому соглашению Майкрософт
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения об учетных записях выставления счетов для клиентского соглашения Майкрософт](../understand/mca-overview.md)
- [Условия в счете клиентского соглашения Майкрософт](../understand/mca-understand-your-invoice.md)