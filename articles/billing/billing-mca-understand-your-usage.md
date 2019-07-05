---
title: Условия в файле об использовании и затратах, Azure для клиента соглашение Microsoft
description: Узнайте, как читать и интерпретировать разделы Azure об использовании и затратах с CSV-ФАЙЛ для профиля выставления счетов.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490630"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Условия в файле об использовании и затратах, Azure для клиента соглашение Microsoft

Эта статья относится к учетной записи выставления счетов для клиента соглашение Microsoft. [Проверьте наличие доступа к соглашению клиента Microsoft](#check-access-to-a-microsoft-customer-agreement).

Azure об использовании и затратах CSV-файл содержит ежедневное и уровне средства измерения плата за использование за текущий расчетный период.

Чтобы получить файл Azure об использовании и затратах, см. в разделе [Просмотр и загрузка данных об использовании Azure и плата за соглашение с клиентом Microsoft](billing-download-azure-daily-usage.md). Эти сведения доступны в формате файла данных с разделителями-запятыми (CSV), который можно открыть в приложении для электронных таблиц.

Плата за использование — это общая сумма за использование подписки **за месяц**. Она не учитывает какие-либо поступления на счет или скидки.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Изменения из Azure EA об использовании и затратах

Если вы являетесь клиентом EA, вы заметите, что условия Azure выставления счетов профиля использование CSV-файла отличаются от условия в CSV-файле использование Azure EA. Ниже приведен сопоставление EA условия использования для выставления счетов условия использования профиля.

| Использование Azure EA CSV | Microsoft Customer соглашение Azure об использовании и затратах CSV |
| --- | --- |
| Дата | date |
| Месяц| date |
| День | date |
| Год | date |
| Продукт | product |
| Значение MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| Значение MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| resourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | теги |
| StoreServiceIdentifier | Н/Д |
| DepartmentName | invoiceSection |
| Центр затрат | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Подробные условия и описания

В файле Azure об использовании и затратах отображаются следующие термины.

Термин | Описание
--- | ---
invoiceId | Документа уникальный идентификатор, указанный в счете PDF
previousInvoiceId | Ссылка на исходный счет, если этот элемент имеет возмещение
billingAccountName | Имя учетной записи выставления счетов
billingAccountId | Уникальный идентификатор для корня платежный счет
billingProfileId | Имя профиля выставления счетов, накапливаемый накладные расходы, выставляются
billingProfileName | Уникальный идентификатор для профиля выставления счетов, накапливаемый накладные расходы, выставляются
invoiceSectionId | Уникальный идентификатор для раздела счета
invoiceSectionName | Имя раздела счета
costCenter | Центр затрат, определенные в подписке для отслеживания затрат (доступно только в открытых периодов выставления счетов)
billingPeriodStartDate | Дата начала расчетного периода, для которого создается счета
billingPeriodEndDate | Дата окончания расчетного периода, для которого создается счета
servicePeriodStartDate | Дата начала периода, оценка которого определила и заблокирован цен на службу потребляемого или приобретенных
servicePeriodEndDate | Дата окончания периода, оценка которого определила и заблокирован цен на службу потребляемого или приобретенных
date | Для Azure и Marketplace основано на использовании расходов это дата оценки. Для разового покупки (резервирования, Marketplace) или основных регулярные платежи (поддержка предложений) это с даты покупки.
serviceFamily | Семейство службы, к которой принадлежит служба
productOrderId | Уникальный идентификатор для заказа продукта
productOrderName | Уникальное имя для заказа продукта
consumedService | Имя используемой службы
meterId | Уникальный идентификатор для единицы измерения
meterName | Имя счетчика
meterCategory | Имя категории, классификации для единицы измерения. Например *облачные службы*, *сети*и т. д.
meterSubCategory | Имя категории счетчика дочернюю классификацию
meterRegion | Имя региона, где доступны метрики для службы. Расположение центра обработки данных для определенных служб, которые оцениваются в зависимости от расположения центра обработки данных.
offer | Название предложения, которые приобрели
productId | Уникальный идентификатор для продукта, выставление счетов, связанные
product | Имя продукта, выставление счетов, связанные
Идентификатор подписки | Уникальный идентификатор для подписки, выставление счетов, связанные
subscriptionName | Имя подписки, выставление счетов, связанные
ReservationId | Уникальный идентификатор для экземпляра приобретенное резервирование
reservationName | Имя экземпляра приобретенное резервирование
значение свойства publisherType | Тип добавляемого издателя (значения: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Издатель для служб Marketplace
resourceGroupId | Уникальный идентификатор для группы ресурсов, связанной с ресурсом
resourceGroupName | Имя группы ресурсов, связанные с ресурсом
resourceId | Уникальный идентификатор для экземпляра ресурса
resourceType | Тип экземпляра ресурса
resourceLocation | Расположение центра обработки данных, где выполняется ресурс.
location | Нормализованный путь к ресурсу, если расположения различных ресурсов настроены для тех же регионах
quantity | Число единиц, приобретенных или обрабатываются
unitOfMeasure | Единица измерения для выставления счетов для службы. Например службы вычислений действует почасовая оплата.
ChargeType | Тип оплаты. Значения: <ul><li>AsCharged использование: Платежи, вычисленные на основе использования службы Azure. Это включает использование на виртуальных машинах, которые не взимается, из-за зарезервированные экземпляры.</li><li>AsCharged-PurchaseMarketplace: Фиксированный или одноразовый регулярные платежи от покупки в Marketplace</li><li>AsCharged-UsageMarketplace: Плата за службы Marketplace, оплачиваемых в соответствии с единиц потребления</li></ul>
isAzureCreditEligible | Флаг, указывающий, подходит ли плата в службе к оплате за использование кредиты Azure (значения: True, False)
serviceInfo1 | Метаданные о службе
serviceInfo2 | Устаревшее поле, в которое записываются необязательные метаданные службы
additionalInfo | Дополнительные метаданные для конкретной службы.
теги | Теги, присваиваемый ресурсу

### <a name="make-sure-that-charges-are-correct"></a>Убедитесь в правильности расходов

Если требуется, чтобы убедиться в правильности расходов в файле с подробными данными об использовании, можно проверить их. См. в разделе [основная информация о расходах на профиль выставления счетов](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Проверьте доступ к соглашению клиента Microsoft
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Дальнейшие действия

- [Просмотр и загрузка счете Microsoft Azure](billing-download-azure-invoice.md)
- [Просматривать и скачивать данные об использовании Microsoft Azure и затратах](billing-download-azure-daily-usage.md)
