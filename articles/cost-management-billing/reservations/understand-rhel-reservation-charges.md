---
title: Скидки на план резервирования Red Hat в Azure
description: Узнайте, как скидки на план Red Hat применяются к программному обеспечению Red Hat на виртуальных машинах.
author: yashesvi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 46a1fd9be35abb19d920e2a3bd34f88c557f40b1
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84735121"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Общие сведения о применении скидок на план резервирования программного обеспечения Red Hat Linux Enterprise в Azure

После покупки плана Red Hat Linux скидка автоматически применяется для развернутых виртуальных машин под управлением Red Hat, которые соответствуют резервированию. План Red Hat Linux покрывает стоимость работы программного обеспечения Red Hat на виртуальной машине Azure.

Чтобы приобрести подходящий план Red Hat Linux, необходимо понять, какие виртуальные машины под управлением Red Hat вы запускаете и число виртуальных ЦП на таких виртуальных машинах. Следующие разделы помогут вам определить подходящий план на основе CSV-файла об использовании.

## <a name="discount-applies-to-different-vm-sizes"></a>Применение скидки для различных размеров виртуальной машины

Как и зарезервированные экземпляры виртуальных машин, план Red Hat предлагает гибкость размера экземпляра. Это означает, что ваша скидка применяется даже в случае развертывания виртуальной машины с разным количеством виртуальных процессоров. Скидка применяется к виртуальным машинам разных размеров в рамках плана программного обеспечения.

Сумма скидки зависит от коэффициента, указанного в следующих таблицах. Коэффициент представляет собой сравнение относительной занимаемой памяти для каждого размера виртуальной машины в этой группе. Коэффициент зависит от виртуальных ЦП виртуальной машины. Используйте значение коэффициента для вычисления того, на сколько экземпляров виртуальных машин можно получить скидку по плану Red Hat Linux.

Например, если вы покупаете план для Red Hat Linux Enterprise Server для виртуальной машины с количеством виртуальных ЦП от 1 до 4, то коэффициент для этого резервирования — 1. Скидка покрывает стоимость программного обеспечения Red Hat для:

- одной развернутой виртуальной машины с количеством виртуальных ЦП от 1 до 4;
- или 0,46 либо же около 46 % суммы затрат на Red Hat Enterprise Linux для виртуальной машины с 5 или более виртуальных ЦП.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Имена на портале Azure Marketplace:
- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 8.2.

[Просмотреть единицы измерения Red Hat Enterprise Linux, к которым относится план](https://isfratio.blob.core.windows.net/isfratio/RHELRatios.csv)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о резервировании см. в следующих статьях:

- [Общие сведения об Azure Reserved VM Instances](save-compute-costs-reservations.md)
- [Предоплата за планы программного обеспечения Red Hat с резервированиями Azure](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Предоплата виртуальных машин с помощью Azure Reserved Virtual Machine Instances](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Управление резервированиями для Azure](manage-reserved-vm-instance.md)
- [Общие сведения об использовании резервирования Azure для подписки с оплатой по мере использования](understand-reserved-instance-usage.md)
- [Общие сведения об использовании зарезервированных экземпляров Azure с Соглашением о регистрации Enterprise](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>Требуется помощь? Свяжитесь с нами

Если у вас есть вопросы или вам нужна помощь, [создайте запрос в службу поддержки](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
