---
title: Сводка по выплатам для коммерческого рынка | Azure Marketplace
description: В сводке выплата отображаются сведения о денежных средствах, полученных с вашего предложения. Она также содержит информацию о сроках получения платежей и сумме выплат.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 0d05802c9d5d80f91913291d710b674369f0ff17
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980274"
---
# <a name="payout-reporting"></a>Отчеты о выплатах

В **сводке выплата** отображаются сведения о денежных средствах, полученных корпорацией Майкрософт. Она также содержит информацию о сроках получения платежей и сумме выплат.

Если вы продаете предложения в Azure Marketplace, вы также увидите сведения об успешных выплатах в **сводке по выплатам**. Дополнительные сведения о платеже Azure Marketplace см. в статьях [политики участия в Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/p/?LinkId=722436) и [Соглашение Microsoft Azure Marketplace издателя](https://go.microsoft.com/fwlink/p/?LinkID=699560).

> [!NOTE]
> Чтобы иметь право на выплату, необходимо достигнуть [пороговое значение платежа](payment-thresholds-methods-timeframes.md) $50. Дополнительные сведения о пороговом значении оплаты см. на этой странице и в [соглашении Microsoft Azure Marketplace издателя](https://go.microsoft.com/fwlink/p/?LinkID=699560).

- [Отчет о выплатах: разница между Портал Cloud Partner и центром партнеров](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [Типы клиентов](#customer-types)
- [Взаимосвязь между выплатами и использованием](#corelation-between-payout-and-usage)
- [Загрузка журнала транзакций](#transaction-history-download-export)
- [Вопросы о выставлении счетов и поддержка](#billing-questions-and-support)

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>Отчет о выплатах: разница между Портал Cloud Partner и центром партнеров

| | Портал Cloud Partner | Центр партнеров |
|---------|---------|---------|
| Ссылки | https://cloudpartner.azure.com/ | https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory и https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| Навигации | Отчеты о выплатах, предоставленные в выплатах Insights | Отчет о выплатах, предоставленный в центре партнеров — значок выплата |
| Область действия | <ul> <li>Элемент "транзакция на строку" является видимым, для сбора, сбора и оплаты </li> <li>Отчеты — показывает все элементы строк после создания заказа на покупку, включая сбор и выполнение выставления счетов, а также состояние сбора и элементы строк, которые еще не подходят для оплаты. </li> </ul> | <ul> <li>Показывает элементы строки после того, как они вычисляются как доступные доходы.</li> <li>Клиенты платят в корпорацию Майкрософт первыми, а затем независимые поставщики программного обеспечения могут увидеть, как запускается отчет о выплатах.</li> <li>В отчете о выплатах не отображается выполняемая коллекция и выполняется выставление счетов.  </li> </ul>  |
| Транзакция не готова к выплатам | Выставление счетов выполняется | Следующий предполагаемый платеж: состояние выплаты в необработанном состоянии.  |
| Состояние вывыплаты |  | Необработанные <br> Получение имеет право на оплату. Он остается в этом состоянии для периода охлаждения, определенного в программном обеспечении для программы поощрения. <br> <br> Предстоящие веб-трансляции: <br> Платежный заказ — создано ожидающих внутренних проверок до обработки платежа. <br> <br> Отправлено: <br> Платеж был отправлен в ваш банк. |

## <a name="customer-types"></a>Типы клиентов 

### <a name="enterprise-agreement"></a>Соглашение Enterprise

Клиенты без Соглашения Enterprise ежемесячно получают счета за лицензии на программное обеспечение из Marketplace. Клиенты с Соглашением Enterprise ежемесячно оплачивают счета через накладную, представляемую раз в квартал.

### <a name="credit-cards-and-monthly-invoice"></a>Кредитные карты и ежемесячный счет

Клиенты также могут оплачивать решения с помощью кредитных карт и ежемесячных накладных. В этом случае плата за лицензии на программное обеспечение будет взиматься ежемесячно.

### <a name="csp-and-direct-pay-users"></a>CSP и прямые платежи пользователям

Например, если клиент приобретается с помощью кредитной карты.

## <a name="corelation-between-payout-and-usage"></a>Взаимосвязь между выплатами и использованием 

|Описание    |    Дата  | Заказы/использование  | Выплата |
|----------|----------|-----------|-------------|
|Период заказа   | 15 августа 2019 г., 30 августа 2019 | **Заказы атрибутов корреляции** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Использование** <br> <ul> <li>CustomerId </li> <li>Имя клиента</li> <li>(Усажереференце) Пурчасерекордид/Линеитемид</li> <li> Оценка расширенной оплаты <br> Сметная сумма выплаты (в валюте издателя) </li> </ul> |  |
|Окончание срока (месяц)   | 30 августа 2019 г. | | |
|Дата выставления счета | 1 сентября 2019 г. | | |
|Дата клиентской выплаты | 1 сентября 2019 г. | | |
|Период депонирования (только для кредитных карт, 30 дней) | 1 сентября 2019 г., 30 сентября 2019 г. | | **Заказы атрибутов корреляции:** <br> <ul><li>AssetId</li> <li>Идентификатор клиента</li> <li> Имя клиента</li> </ul> <br> **Использование** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Имя клиента</li> <li>OrderId</li> <li>линеитемид</li> <li>трансактионамаунт</li> <li>еарнингамаунтинластпайменткурренци</li> </ul> <br> **Состояние вывыплаты:** Необработанные |
|Начало периода сбора | 1 сентября 2019 г. | | |
|Окончание периода сбора (максимальное, 30 дней) | 30 сентября 2019 г. | | |
|Дата вычисления выплат (15-е число каждого месяца) | 1 октября 2019 г. | | **Атрибуты корреляции** <br> <ul><li>AssetId</li> <li>Идентификатор клиента</li> <li>Имя клиента</li> </ul> <br> **Использование** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Имя клиента</li> <li>OrderId</li> <li>линеитемид</li> <li>трансактионамаунт</li> <li>еарнингамаунтинластпайменткурренци</li> </ul> <br> **Состояние вывыплаты:** Стоящие |
|Дата выплаты | 15 октября 2019 г. | | **Атрибуты корреляции** <br> <ul><li>AssetId</li> <li>Идентификатор клиента</li> <li> Имя клиента</li> </ul> <br> **Использование** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Имя клиента</li> <li>OrderId</li> <li>линеитемид</li> <li>трансактионамаунт</li> <li>еарнингамаунтинластпайменткурренци</li> </ul> <br> **Состояние вывыплаты:** Оплата отправлена |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>Соглашение Enterprise (для ежеквартальных и ежемесячных клиентов)

| Описание |    Дата  | Использование | Выплата |
|----------|----------|---------|-----------|
|Период заказа | 15 августа 2019 г., 30 августа 2019 | **Заказы атрибутов корреляции** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Отчет об использовании** <br> <ul> <li>CustomerId </li> <li>Имя клиента</li> <li>(Усажереференце) Пурчасерекордид/Линеитемид</li> <li> Оценка расширенной оплаты <br> Сметная сумма выплаты (в валюте издателя) </li> </ul> | |
|Окончание периода (квартал) | 30 сентября 2019 г. | | |
|Дата выставления счета | 15 октября 2019 г. | | |
|Период депонирования (только для кредитных карт, 30 дней) | Н/Д | | |
|Начало периода сбора | 15 октября 2019 г. | | |
|Только кредитные карты, 30 дней | 1 ноября 2019 — 30 ноября 2019 г. | | |
|Окончание периода сбора (максимальное, 90 дней) | 15 января 2020 г. | | |
|Дата клиентской выплаты | 30 декабря 2019 г. | | |
|Вычисление выплата | 15 января 2020 г. | | |
|Дата выплаты | 15 февраля, 2020 | | **Для ежеквартальных клиентов** <br> <br> **Отчет о заказах** <br> <ul><li>AssetId</li> <li>Идентификатор клиента</li> <li> Имя клиента</li> </ul> <br> **Использование** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Имя клиента</li> <li>OrderId</li> <li>линеитемид</li> <li>трансактионамаунт</li> <li>еарнингамаунтинластпайменткурренци</li> </ul> <br> **Состояние вывыплаты:** Отправлено |

<!---
## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•   If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.



**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.
--->

## <a name="transaction-history-download-export"></a>Загрузка журнала транзакций экспорт

Этот параметр обеспечивает загрузку каждого элемента строки получения данных, который вы видите на странице Журнал транзакций, получения типа, даты, связанной суммы транзакции, клиента, продукта и других сведений о транзакциях, применимых к программе поощрения. 

| Имя столбца     | Описание    | 
|-------------|-------------------------------|
| еарнингид                      | Уникальный идентификатор для каждого из них                                                                                                       |
| партиЦипантид                  | Основное удостоверение партнера в программе                                                                            | 
| партиЦипантидтипе              | В основном идентификатор программы для поощрения программ и продавцов, если для магазинов и Azure Marketplace                                          | 
| партиЦипантнаме                | Имя участвующего партнера                                                                                                              | 
| партнеркаунтрикоде             | Местонахождение и страна попарного партнера                                                                                                  |
| programName                    | Имя программы поощрения или магазина                                                                                                             | 
| transactionId                  | Уникальный идентификатор транзакции                                                                                                    | 
| трансактионкурренци            | Валюта, в которой была выполнена исходная транзакция клиента (это не Валюта местонахождения партнера)                                     | 
| transactionDate                | Дата транзакции. Полезно для программ, где многие транзакции вносят вклад в один из них                                           | 
| трансактионексчанжерате        | Обменный курс, используемый для отображения суммы соответствующих транзакций USD                                                                 | 
| трансактионамаунт              | Сумма транзакции в валюте исходной транзакции в зависимости от того, какое время формируется.                                              | 
| трансактионамаунтусд           | Сумма транзакции в USD                                                                                                                | 
| Ручки                          | Указывает бизнес-правило на получение                                                                                                  | 
| еарнинграте                    | Коэффициент поощрения, применяемый к сумме транзакций для создания доходности                                                                      | 
| quantity                       | Зависит от программы. Показывает оплачиваемое количество для транзакционных программ                                                            | 
| куантититипе                   | Указывает тип количества, например "количество в счете", MAU                                                                                     |
| еарнингтипе                    | Указывает, является ли он платным, бонусом, Coop, продавать и т. д.                                                                                          | 
| еарнингамаунт                  | Получение суммы в исходной валюте транзакции                                                                                      |
| еарнингамаунтусд               | Сумма в долларах США                                                                                                                    |
| еарнингдате                    | Дата получение                                                                                                                      |
| калкулатиондате                | Дата, когда полученное значение было вычислено в системе                                                                                            |
| еарнинжексчанжерате            | Обменный курс, используемый для отображения соответствующей суммы USD                                                                                  |
| ексчанжератедате               | Дата валютного курса, используемая для расчета Еарнингамаунт USD                                                                                   | 
| пайментамаунтвотакс             | Получение суммы (без налога) в оплате по валюте только для "отправленных" выплат                                                                 |
| пайменткурренци                | Оплата по валюте, выбранной партнером в профиле оплаты. Отображается только для отправленных платежей                                                   |
| пайментексчанжерате            | Обменный курс, используемый для расчета Пайментамаунтвотакс в валюте оплаты с помощью Ексчанжератедате                                            |
| пайментид            | Уникальный идентификатор платежа. Это число отображается в банковской выписку                                            |
| пайментстатус            | Состояние платежа                                            |
| пайментстатусдескриптион            | Понятное описание состояния оплаты                                            |
| customerId                     | Всегда будет пустым                                                                                                                     |
| customerName                   | Всегда будет пустым                                                                                                                     |
| partNumber                     | Всегда будет пустым                                                                                                                     |
| productName                    | Имя продукта, связанное с транзакцией                                                                                                       |
| productId                      | Уникальный код продукта                                                                                                                |
| parentProductId                | Уникальный код родительского продукта. Примечание. Если для транзакции не существует родительского продукта, идентификатор родительского продукта — Product ID. |
| парентпродуктнаме              | Название родительского продукта. Примечание. Если для транзакции не существует родительского продукта, имя родительского продукта — название продукта.   |
| productType                    | Тип продукта (например, приложение, надстройка, игра и т. п.)                                                                                        |
| инвоиценумбер                  | Номер счета (применимо только для EA)                                                                                                  |
| реселлерид                     | Идентификатор торгового посредника                                                                                                                      |
| resellerName                   | Имя торгового посредника                                                                                                                            |
| трансактионтипе                | Тип транзакции (например, покупка, возврат денежных средств, реверсирование, возвратный платеж и т. д.)                                                               |
| локалпровидерселлер            | Локальный поставщик или продавец в записи                                                                                                          |
| таксремиттед                    | Сумма налоговых отчислений (продажи, использование или налоги НДС/GST).                                                                                   |
| таксремитмодел                  | Сторона, ответственная за удержание налогов (продажи, использование или налоги НДС/GST).                                                                    |
| сторефи                       | Сумма, которая хранится корпорацией Майкрософт как плата за предоставление приложения или надстройки в магазине.                                            |
| трансактионпайментмесод       | Платежное средство клиента, используемое для транзакции (например, карта, мобильный оператор, PayPal и т. д.)                                |
| тпан                           | Указывает сеть сторонних поставщиков рекламы                                                                                                     |
| кустомеркаунтри                | Страна или регион клиента                                                                                                                         |
| кустомерЦити                   | Город клиента                                                                                                                            |
| кустомерстате                  | Состояние клиента                                                                                                                           |
| кустомерзип                    | Почтовый индекс клиента                                                                                                                 |
| идентификатор клиента;                       |                                                                                                                                          |
| екстерналреференцеид            | Уникальный идентификатор программы                                                                                                        |
| екстерналреференцеидлабел       | Метка уникального идентификатора                                                                                                                  |
| трансактионкаунтрикоде       | Код страны, в которой произошла транзакция                                                                                                                  |
| такскаунтри       | Продано в страну клиента                                                                                                                  |
| таксстате       | Продано в состояние клиента                                                                                                                  |
| таксЦити       | В городе клиента                                                                                                                  |
| таксзипкоде       | Продано в ZIP-индекс клиента                                                                                                                  |
| лиценсингпрограмнаме       |                                                                                                                   |
| Программный код       | Строка для соотнесения с именем программы                                                                                                                   |
| еарнингамаунтинластпайменткурренци       | Получение суммы в последней валюте оплаты (поле будет пустым, если предыдущие платежи не были оплачены)                                                                                                                   |
| ластпайменткурренци       | Валюта последнего платежа (поле будет пустым, если предыдущий платеж не был оплачен)                                                                                                                   |
| AssetId       | Уникальный идентификатор заказов клиентов для службы Marketplace.  Он представляет элементы строки покупки в транзакциях. Может быть несколько ресурсов.                                                                                                                   |
| OrderId       | относится к счету клиента                                                                                                                   |
| линеитемид       | отдельная строка в счете клиента                                                                                                                   |
| Страна клиента       | Название страны, предоставленное клиентом.  Это может отличаться от страны в подписке Azure клиента.                                                                                                                   |
| Клиент EmailAddress       | Адрес электронной почты, предоставленный конечным пользователем.  Это может отличаться от адреса электронной почты в подписке Azure клиента.                                                                                                                   |
| SkuId       | Идентификатор SKU, определенный во время публикации. Предложение может иметь много номеров SKU, но номер SKU может быть связан только с одним предложением.                                                                                                                   |

>[!Note]
>Все отчеты и аналитические сведения о параметре публикации на языке Transact доступны в разделе Insights статьи Портал Cloud Partner или аналитики в центре партнеров.

## <a name="billing-questions-and-support"></a>Поддержка и вопросы о выставлении счетов

Чтобы получить справку по вопросам выставления счетов, обратитесь в [службу поддержки издателя коммерческого магазина](https://aka.ms/marketplacepublishersupport).
