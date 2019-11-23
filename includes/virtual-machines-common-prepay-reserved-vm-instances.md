---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/21/2019
ms.openlocfilehash: f583796fc353852ef3898e28fa96524e08cfb4ad
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74414533"
---
When you commit to an Azure reserved VM instance you can save money. Скидка на резервирование автоматически применяется к количеству работающих виртуальных машин, которые соответствуют области и атрибутам резервирования. You don't need to assign a reservation to a virtual machine to get the discounts. A reserved instance purchase covers only the compute part of your VM usage. For Windows VMs, the usage meter is split into two separate meters. There's a compute meter, which is same as the Linux meter, and a Windows IP meter. The charges that you see when you make the purchase are only for the compute costs. Charges don't include Windows software costs. For more information about software costs, see [Software costs not included with Azure Reserved VM Instances](../articles/billing/billing-reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Определение нужного размера виртуальной машины перед покупкой

Before you buy a reservation, you should determine the size of the VM that you need. The following sections will help you determine the right VM size.

### <a name="use-reservation-recommendations"></a>Use reservation recommendations

You can use reservation recommendations to help determine the reservations you should purchase.

- Purchase recommendations and recommended quantity are show when you purchase a VM reserved instance in the Azure portal.
- Azure Advisor provides purchase recommendations for individual subscriptions.  
- You can use the APIs to get purchase recommendations for both shared scope and single subscription scope. For more information, see [Reserved instance purchase recommendation APIs for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- For Enterprise Agreement (EA) and Microsoft Customer Agreement (MCA) customers, purchase recommendations for shared and single subscription scopes are available with the [Azure Consumption Insights Power BI content pack](/power-bi/service-connect-to-azure-consumption-insights).

### <a name="services-that-get-vm-reservation-discounts"></a>Службы, которые получают скидки на резервирование виртуальных машин

Резервирование виртуальных машин может применяться к использованию виртуальной машины, созданной из нескольких служб, а не только для развертываний виртуальных машин. Ресурсы, которые получают скидки на резервирование, изменяются в зависимости от параметра гибкости размера экземпляра.

#### <a name="instance-size-flexibility-setting"></a>Параметр гибкости размера экземпляра

Параметр гибкость размера экземпляра определяет, какие службы получают скидки на зарезервированные экземпляры.

Независимо от того, включен ли параметр или выключен, скидки на резервирование автоматически применяются к любому соответствующему использованию виртуальной машины, если параметр *ConsumedService* равен `Microsoft.Compute`. Поэтому проверьте данные об использовании для значения *ConsumedService*. Некоторые примеры:

- Виртуальные машины
- Масштабируемые наборы виртуальных машин
- Служба контейнеров
- Развертывания пакетной службы Azure (в режиме пользовательских подписок)
- Служба Azure Kubernetes (AKS)
- Service Fabric

Если эта настройка включена, скидки на резервирование автоматически применяются к соответствующему использованию виртуальных машин, когда *ConsumedService* является одним из следующих элементов:

- Microsoft.Compute;
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Проверьте значение *ConsumedService* в данных об использовании, чтобы определить, подходит ли использование для скидки на резервирование.

Дополнительные сведения о гибкости размера экземпляров см. в статье [Гибкость размеров виртуальных машин при использовании зарезервированных экземпляров виртуальных машин](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analyze your usage information
Analyze your usage information to help determine which reservations you should purchase.

Usage data is available in the usage file and APIs. Use them together to determine which reservation to purchase. Check for VM instances that have high usage on daily basis to determine the quantity of reservations to purchase.

Avoid the `Meter` subcategory and `Product` fields in usage data. They don't distinguish between VM sizes that use premium storage. If you use these fields to determine the VM size for reservation purchase, you may buy the wrong size. Then you won't get the reservation discount you expect. Instead, refer to the `AdditionalInfo` field in your usage file or usage API to determine the correct VM size.

### <a name="purchase-restriction-considerations"></a>Purchase restriction considerations

Reserved VM Instances are available for most VM sizes with some exceptions. Reservation discounts don't apply for the following VMs:

- **VM series** - A-series, Av2-series, or G-series.

- **Preview or Promo VMs** - Any VM-series or size that is in preview or uses promotional meter.

- **Clouds** - Reservations aren't available for purchase in Germany or China regions.

- **Insufficient quota** - A reservation that is scoped to a single subscription must have vCPU quota available in the subscription for the new RI. Например, если целевая подписка имеет квоту на 10 виртуальных ЦП для серии D, вы не сможете приобрести резервирование для 11 экземпляров Standard_D1. При проверке квоты для резервирования учитываются все виртуальные машины, уже размещенные в подписке. Например, если подписка имеет квоту на 10 виртуальных ЦП для серии D и два развернутых экземпляра Standard_D1, в этой подписке можно приобрести резервирование для 10 экземпляров Standard_D1. You can [create quote increase request](../articles/azure-supportability/resource-manager-core-quotas-request.md) to resolve this issue.

- **Capacity restrictions** - In rare circumstances, Azure limits the purchase of new reservations for subset of VM sizes, because of low capacity in a region.

## <a name="buy-a-reserved-vm-instance"></a>Приобретение Reserved VM Instance

You can buy a reserved VM instance in the [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Платите за резервирование [наперед или ежемесячными платежами](../articles/billing/billing-monthly-payments-reservations.md).
These requirements apply to buying a reserved VM instance:

- You must be in an Owner role for at least one EA subscription or a subscription with a pay-as-you-go rate.
- For EA subscriptions, the **Add Reserved Instances** option must be enabled in the [EA portal](https://ea.azure.com/). Or, if that setting is disabled, you must be an EA Admin for the subscription.
- В рамках программы для поставщиков облачных решений (CSP) только агенты администрирования или агенты продаж могут приобрести резервирования.

Требования для приобретения экземпляра.

1. Войдите на [портале Azure](https://portal.azure.com).
1. Выберите **Все службы** > **Резервирование**.
1. Select **Add** to purchase a new reservation and then click **Virtual machine**.
1. Enter required fields. Работающие экземпляры виртуальной машины, соответствующие выбранным атрибутам, подходят для получения скидки на резервирование. Фактическое число экземпляров виртуальной машины, получающих скидку, зависит от области и выбранного количества.

If you have an EA agreement, you can use the **Add more option** to quickly add additional instances. The option isn't available for other subscription types.


| Поле      | Описание|
|------------|--------------|
|Subscription|Подписка, используемая для оплаты резервирования. The payment method on the subscription is charged the costs for the reservation. The subscription type must be an enterprise agreement (offer numbers: MS-AZR-0017P or MS-AZR-0148P) or Microsoft Customer Agreement or an individual subscription with pay-as-you-go rates (offer numbers: MS-AZR-0003P or MS-AZR-0023P). The charges are deducted from the monetary commitment balance, if available, or charged as overage. For a subscription with pay-as-you-go rates, the charges are billed to the credit card or invoice payment method on the subscription.|    
|Область действия       |Область резервирования может охватывать одну или несколько подписок (общая область). Если выбрать: <ul><li>**Одна группа ресурсов** — скидка по резервированию применяется к подходящим ресурсам только в выбранной группе ресурсов.</li><li>**Одна подписка** — скидка по резервированию применяется к подходящим ресурсам только в выбранной подписке.</li><li>**Общая область резервирования** — скидка по резервированию применяется к подходящим ресурсам во всех допустимых подписках в контексте выставления счетов. For EA customers, the billing context is the enrollment. Для отдельных подписок с оплатой по мере использования областью выставления счетов считаются все допустимые подписки, созданные администратором учетной записи.</li></ul>|
|Регион    |Регион Azure, охватываемый резервированием.|    
|Размер виртуальной машины     |Размер экземпляров виртуальной машины.|
|Оптимизировать для     |VM instance size flexibility is selected by default. Click **Advanced settings** to change the instance size flexibility value to apply the reservation discount to other VMs in the same [VM size group](../articles/virtual-machines/windows/reserved-vm-instance-size-flexibility.md). Приоритет емкости назначает приоритет емкости центра обработки данных для развертываний. It offers additional confidence in your ability to launch the VM instances when you need them. Приоритет емкости доступен только в том случае, если область резервирования является одиночной подпиской. |
|Срок действия        |Один или три года.|
|Количество    |Число экземпляров, приобретаемых в рамках резервирования. Это количество работающих экземпляров виртуальной машины, которые могут получить скидку. For example, if you are running 10 Standard_D2 VMs in the East US, then you would specify quantity as 10 to maximize the benefit for all running VMs. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Usage data and reservation utilization

В данных об использовании указана нулевая эффективная цена за использование, на которое распространяется скидка на резервирование. You can see which VM instance received the reservation discount for each reservation.

For more information about how reservation discounts appear in usage data, see [Understand Azure reservation usage for your Enterprise enrollment](../articles/billing/billing-understand-reserved-instance-usage-ea.md) if you are an EA customer. If you have an individual subscription, see [Understand Azure reservation usage for your Pay-As-You-Go subscription](../articles/billing/billing-understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Change a reservation after purchase

После приобретения в резервирование можно внести следующие типы изменений:

- Обновить область резервирования
- Instance size flexibility (if applicable)
- Ownership

You can also split a reservation into smaller chunks and merge already split reservations. None of the changes cause a new commercial transaction or change the end date of the reservation.

You can't make the following types of changes after purchase, directly:

- An existing reservation’s region
- SKU
- Количество
- Duration

However, you can *exchange* a reservation if you want to make changes.

## <a name="cancel-exchange-or-refund-reservations"></a>Отмена, обмен резервирования, возмещение средств за резервирование

Вы можете отменить и обменять резервирования, а также вернуть вложенные в резервирование средства, но при этом применяются определенные ограничения. Дополнительные сведения см. в статье [Самостоятельное выполнение обмена и возмещения средств для Azure Reserved Virtual Machine Instances](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Нужна помощь? Связаться с нами.

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Дальнейшие действия

- Сведения об управлении резервированием см. в разделе [Управление резервированиями в Azure](../articles/billing/billing-manage-reserved-vm-instance.md).
- Дополнительные сведения о резервировании в Azure см. в следующих статьях.
    - [Основные сведения о резервировании в Azure](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Управление резервированиями в Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Сведения о применении скидки на зарезервированный экземпляр виртуальной машины](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Общие сведения об использовании резервирования Azure для отдельной подписки с оплатой по мере использования](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Затраты на программное обеспечение Windows, которые не включены в стоимость зарезервированных экземпляров Azure](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Sell Microsoft Azure Reserved Instances](https://docs.microsoft.com/partner-center/azure-reservations) (Продажа зарезервированных экземпляров Microsoft Azure)
