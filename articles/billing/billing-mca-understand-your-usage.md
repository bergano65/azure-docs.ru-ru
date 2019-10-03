---
title: Условия в файле использования и расходов Azure для Клиентского соглашения Майкрософт
description: Узнайте, как интерпретировать разделы, посвященные использованию и расходам CSV для вашего профиля выставления счетов.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c0c43c779affb4edca78def95906f5adfcc6fac4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709462"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Условия в файле использования и расходов Azure для Клиентского соглашения Майкрософт

В этой статье рассматривается учетная запись выставления счетов для Клиентского соглашения Майкрософт. [Проверьте наличие доступа к Клиентскому соглашению Майкрософт](#check-access-to-a-microsoft-customer-agreement).

CSV-файл Azure с данными об использовании и расходах содержит ежедневные сведения за текущий расчетный период на уровне средства измерения.

Узнайте, как [просмотреть и скачать сведения об использовании и расходах в Azure в рамках вашего клиентского соглашения Майкрософт](billing-download-azure-daily-usage.md). Эти сведения доступны в формате файла данных с разделителями-запятыми (CSV), который можно открыть в приложении для электронных таблиц.

Плата за использование — это общая сумма за использование подписки **за месяц**. Она не учитывает какие-либо поступления на счет или скидки.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Изменения использования и расходов EA в Azure

Если вы — клиент EA, вы заметите, что условия в CSV-файле использования профиля выставления счетов отличаются от условий в CSV-файле использования EA в Azure. Ниже приведено сопоставление условий использования EA с условиями использования профиля выставления счетов:

| CSV-файл использования EA в Azure | CSV-файл использования и расходов Azure для Клиентского соглашения Майкрософт |
| --- | --- |
| Дата | date |
| Месяц| date |
| День | date |
| Год | date |
| Продукт | product |
| Значение MeterId | Значение meterID |
| MeterCategory | meterCategory |
| meterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Теги | tags |
| StoreServiceIdentifier | Недоступно |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitOfMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Подробные условия и описания

Следующие условия отображаются в файле использования и расходов Azure.

Термин | ОПИСАНИЕ
--- | ---
invoiceId | Уникальный идентификатор документа, указанный в PDF-счете
previousInvoiceId | Ссылка на исходный счет, если этот элемент строки является возвратом денег
billingAccountName | Имя учетной записи выставления счетов
billingAccountId | Уникальный идентификатор для корневой учетной записи выставления счетов
billingProfileId | Название профиля выставления счетов, в котором начисляются расходы, подлежащие оплате
billingProfileName | Уникальный идентификатор профиля выставления счетов, в котором начисляются расходы, подлежащие оплате
invoiceSectionId | Уникальный идентификатор раздела счетов
invoiceSectionName | Название раздела счета
costCenter | Центр затрат, определенный в подписке для отслеживания затрат (доступен только в открытых периодах выставления счетов)
billingPeriodStartDate | Начальная дата расчетного периода, для которого создается счет
billingPeriodEndDate | Дата окончания расчетного периода, для которого создается счет
servicePeriodStartDate | Дата начала периода оценки, в которой определены и заблокированы цены для использованной или приобретенной службы
servicePeriodEndDate | Дата окончания периода оценки, в которой определены и заблокированы цены для использованной или приобретенной службы
date | Для расходов на использование Azure и Marketplace, это дата оценки. Для одноразовых покупок (резервирование, Marketplace) или фиксированных повторяющихся расходов (предложения поддержки) это дата покупки.
serviceFamily | Семейство служб, к которому принадлежит служба
productOrderId | Уникальный идентификатор заказа продукта
productOrderName | Уникальное имя заказа продукта
consumedService | Имя потребляемой службы
meterId | Уникальный идентификатор средства измерения
meterName | Имя средства измерения
meterCategory | Имя категории классификации для единицы измерения. Например, *Облачные службы*, *Сеть*и т. д.
meterSubCategory | Имя категории подклассификации средства измерения
meterRegion | Имя региона, в котором доступна единица измерения для службы. В этом столбце указывается расположение центра обработки данных для определенных служб. От этого расположения может зависеть стоимость некоторых услуг.
offer | Название приобретенного предложения
productId | Уникальный идентификатор продукта с начислением расходов
product | Название продукта, в котором начисляется плата
Идентификатор подписки | Уникальный идентификатор подписки с начислением расходов
subscriptionName | Название подписки, в которой начисляется плата
reservationId | Уникальный идентификатор для приобретенного экземпляра резервирования
reservationName | Имя приобретенного экземпляра резервирования
publisherType | Тип издателя (значения: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Издатель для служб Marketplace
resourceGroupId | Уникальный идентификатор группы ресурсов, связанной с ресурсом
имя_группы_ресурсов | Имя группы ресурсов, связанное с ресурсом
resourceId | Уникальный идентификатор экземпляра ресурса
тип_ресурса | Тип экземпляра ресурса
resourceLocation | Определяет месторасположение центра обработки данных, где выполняется ресурс.
location | Нормализованное месторасположение ресурса, если для одних и тех же регионов настроены разные месторасположения ресурсов
quantity | Количество приобретенных или израсходованных единиц
unitOfMeasure | Единица измерения выставления счетов для службы. Например, счета за службы вычислений выставляются на почасовой основе.
ChargeType | Тип расходов. Значения: <ul><li>AsCharged-Usage: Расходы, начисленные на основе использования службы Azure. Это включает в себя использование виртуальных машин, которые не оплачиваются из-за зарезервированных экземпляров.</li><li>AsCharged-PurchaseMarketplace: Однократная или фиксированная плата за покупки в Marketplace</li><li>AsCharged-UsageMarketplace: Плата за услуги Marketplace, которые оплачиваются на основе единиц потребления</li></ul>
isAzureCreditEligible | Флажок, указывающий, может ли взиматься плата за использование службы с помощью кредитов Azure (значения: True, False)
serviceInfo1 | Метаданные определенных служб
serviceInfo2 | Устаревшее поле, в которое записываются необязательные метаданные службы
additionalInfo | Дополнительные метаданные определенных служб.
tags | Теги, присваиваемые ресурсу

### <a name="make-sure-that-charges-are-correct"></a>Убедитесь, что взимаемая плата — правильная

Если вы хотите убедиться, что плата в подробном файле использования указана правильно, вы можете проверить ее. См. раздел [Understand charges on your billing profile's invoice](billing-mca-understand-your-bill.md) (Общие сведения об оплате счета для профиля выставления счетов)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверка доступа к Клиентскому соглашению Майкрософт
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дополнительная информация

- [Просмотр и скачивание счета Microsoft Azure](billing-download-azure-invoice.md)
- [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](billing-download-azure-daily-usage.md)
