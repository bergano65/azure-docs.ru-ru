---
title: Отслеживание кредитного баланса Azure для Клиентского соглашения Майкрософт
description: Узнайте, как проверить кредитный баланс Azure для Клиентского соглашения Майкрософт.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375538"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>Отслеживание кредитного баланса Azure для Клиентского соглашения Майкрософт

На портале Azure можно проверить кредитный баланс Azure для вашей учетной записи выставления счетов для Клиентского соглашения Майкрософт. 

Кредиты можно использовать только для оплаты расходов, на которые такие кредиты распространяются. Плата взимается при использовании продуктов, которые можно оплатить кредитами или использование которых превышает ваш кредитный баланс. См. дополнительные сведения о [продуктах, которые не охватываются кредитами Azure](#products-that-arent-covered-by-azure-credits).

В учетной записи выставления счетов для Клиентского соглашения Майкрософт кредиты назначаются профилю выставления счетов. Каждый профиль выставления счетов имеет собственные кредиты. Для просмотра кредитного баланса Azure профиля выставления счетов необходимо иметь роль "владелец", "участник", "читатель" или "менеджер счетов" для профиля выставления счетов, или роль "владелец", "участник", "читатель" для учетной записи выставления счетов. Подробные сведения о ролях см. в разделе [Сведения об административных ролях клиентских соглашений Майкрософт в Azure](billing-understand-mca-roles.md).

В этой статье рассматривается учетная запись выставления счетов для Клиентского соглашения Майкрософт. [Проверьте наличие доступа к Клиентскому соглашению Майкрософт](#check-access-to-a-microsoft-customer-agreement).

## <a name="check-your-credit-balance-in-the-azure-portal"></a>Проверка кредитного баланса на портале Azure

1. Войдите на [портале Azure]( https://portal.azure.com).

2. Выполните поиск по фразе **Управление затратами + выставление счетов**.

    ![Снимок экрана с изображением поиска на портале по фразе "Управление затратами + выставление счетов"](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  Выберите **Кредиты Azure** в левой части. В зависимости от уровня вашего доступа вам, возможно, потребуется выбрать учетную запись выставления счетов или профиль выставления счетов, а затем — **Кредиты Azure**.

4. На странице "Кредиты Azure" отображается следующая информация:

   ![Снимок экрана: кредитный баланс и транзакции для профиля выставления счетов](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | Термин               | Определение                           |
   |--------------------|--------------------------------------------------------|
   | Оценка баланса  | Оценочная сумма кредитов, которые у вас есть после рассмотрения всех выставленных счетов и незавершенных транзакций |
   | Текущий баланс    | Количество кредитов к последнему счету. Сюда не включены ожидающие транзакции |
   | Транзакции       | Платежные операции, на которые распространяется ваш кредитный баланс Azure |

   Когда ваш расчетный баланс снижается до 0, с вас взимается плата за все использование, в том числе за продукты, которые можно оплатить кредитами.

6. Выберите **Список кредитов**, чтобы просмотреть список кредитов для профиля выставления счетов. В списке кредитов представлены следующие данные:

   ![Снимок экрана списка кредитов для профиля выставления счетов](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | Термин | Определение |
   |---|---|
   | Источник | Источник приобретения кредита |
   | Дата начала | Дата приобретения кредита |
   | Срок действия | Дата истечения срока действия кредита |
   | Текущий баланс | Баланс последнего счета |
   | Исходная сумма | Исходный объем кредита |
   | Status | Текущее состояние кредита. Состояние может быть "Активно", "Использовано", "Просрочено" или "С истекшим сроком действия" |

## <a name="check-your-credit-balance-programmatically"></a>Проверьте свой кредитный баланс программным образом

Вы можете использовать API [Выставления счетов Azure](https://docs.microsoft.com/rest/api/billing/) и [Потребление](https://docs.microsoft.com/rest/api/consumption/), чтобы проверить кредитный баланс для своего платежного счета программным способом.

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

Используйте свойство `displayName` профиля выставления счетов, чтобы определить профиль выставления счетов, для которого необходимо проверить кредитный баланс. Скопируйте `id` профиля выставления счетов. Например, если вы хотите проверить кредитный баланс для профиля выставления счетов **Development** (Разработка), скопируйте ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге.

### <a name="get-azure-credit-balance"></a>Получение кредитного баланса Azure 

Выполните следующий запрос, заменив `<billingProfileId>` на `id`, скопированный на первом шаге (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

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

| Имя элемента  | ОПИСАНИЕ                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | Предполагаемая сумма кредитов, которые у вас есть после рассмотрения всех выставленных счетов и незавершенных транзакций. |
| `currentBalance`   | Количество кредитов по состоянию на момент выставления последнего счета. Сюда не включены незавершенные транзакции.    |
| `pendingCreditAdjustments`      | Корректировки, как возврат денег для которых еще не выставлен счет.  |
| `expiredCredit`      |  Кредит, срок действия которого истек с момента последнего счета.  |
| `pendingEligibleCharges`  | Расходы на кредит, для которых еще не выставлен счет.   |

### <a name="get-list-of-credits"></a>Получение списка кредитов

Выполните следующий запрос, заменив `<billingProfileId>` на `id`, скопированный на первом шаге (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). 

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
| Имя элемента  | ОПИСАНИЕ                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | Первоначальная сумма кредита. |
| `closedBalance`   | Баланс до выставления последнего счета.    |
| `source`      | Источник, определяющий, кто и в какой способ получил кредит. |
| `startDate`      |  Дата активации кредита.  |
| `expirationDate`  | Дата истечения срока действия кредита.   |
| `poNumber`  | Номер заказа на покупку, по которому был выставлен счет в кредитах.   |

### <a name="get-transactions-that-affected-credit-balance"></a>Получение транзакций, на которые распространяется кредитный баланс

Выполните следующий запрос, заменив `<billingProfileId>` на `id`, скопированный на первом шаге (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```). Чтобы получить транзакции за требуемый период времени, необходимо передать **startDate** и **endDate**.

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
| Имя элемента  | ОПИСАНИЕ                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | Дата выполнения транзакции. |
| `description` | Описание транзакции. |
| `adjustments`   | Корректировки кредита для транзакции.    |
| `creditExpired`      | Сумма кредита, срок действия которого истек. |
| `charges`      |  Плата за транзакцию.  |
| `closedBalance`  | Баланс после транзакции.   |
| `eventType`  | Тип транзакции.   |
| `invoiceNumber`  | Номер счета, по которому оплачивалась транзакция. Он будет пустым для незавершенной транзакции.   |

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
- Visual Studio Enterprise — ежемесячно
- Visual Studio Enterprise — ежегодно
- Visual Studio Professional — ежемесячно
- Visual Studio Professional — ежегодно
- Продукты в Azure Marketplace
- Планы поддержки Azure

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к Клиентскому соглашению Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Требуется помощь? Обратитесь в службу поддержки.

Если вам нужна помощь, [обратитесь в службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), которая поможет быстро устранить проблему.

## <a name="next-steps"></a>Дополнительная информация

- [Общие сведения об учетных записях выставления счетов для клиентского соглашения Майкрософт](billing-mca-overview.md)
- [Условия в счете клиентского соглашения Майкрософт](billing-mca-understand-your-invoice.md)
