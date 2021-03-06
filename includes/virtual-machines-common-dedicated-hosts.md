---
title: включение файла
description: включение файла
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 31fdd85fdcc40b38738d33e2c0c13797db7b1d42
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390546"
---
## <a name="benefits"></a>Преимущества 

Резервирование всего узла обеспечивает следующие преимущества:

-   Изоляция оборудования на физическом уровне сервера. На узлах не будут размещаться другие виртуальные машины. Выделенные узлы развертываются в одном и том же центре обработки данных и используют одну и ту же сеть и базовую инфраструктуру хранения как другие, не изолированные узлы.
-   Управление событиями обслуживания, инициированными платформой Azure. Хотя большинство событий обслуживания почти не оказывает влияния на виртуальные машины, существуют некоторые конфиденциальные рабочие нагрузки, в которых каждая секунда приостановки может оказать влияние на работу. С помощью выделенных узлов можно принять участие в окне обслуживания, чтобы снизить влияние на службу.
-   Благодаря преимуществам гибридного использования Azure вы можете использовать собственные лицензии для Windows и SQL в Azure. Использование гибридных преимуществ предоставляет дополнительные преимущества. Дополнительные сведения см. в разделе [преимущество гибридного использования Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Группы, узлы и виртуальные машины  

![Просмотр новых ресурсов для выделенных узлов.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

**Группа узлов** — это ресурс, представляющий коллекцию выделенных узлов. Вы создаете группу узлов в регионе и зону доступности и добавляете к ней узлы.

**Узел** — это ресурс, сопоставленный с физическим сервером в центре обработки данных Azure. Физический сервер выделяется при создании узла. Узел создается в группе узлов. Узел имеет номер SKU, описывающий, какие размеры виртуальных машин можно создать. На каждом узле может размещаться несколько виртуальных машин разного размера, если они находятся в одном и том же наборе.

При создании виртуальной машины в Azure можно выбрать выделенный узел, который будет использоваться для виртуальной машины. Вы имеете полный контроль над тем, какие виртуальные машины размещаются на узлах.


## <a name="high-availability-considerations"></a>Рекомендации по высокой доступности 

Для обеспечения высокой доступности необходимо развернуть несколько виртуальных машин, распределенных по нескольким узлам (не менее 2). С помощью выделенных узлов Azure у вас есть несколько вариантов для подготовки инфраструктуры к формированию границ изоляции ошибок.

### <a name="use-availability-zones-for-fault-isolation"></a>Использование Зоны доступности для изоляции сбоев

Зоны доступности являются уникальными физическими расположениями в пределах региона Azure. Каждая зона состоит из одного или нескольких центров обработки данных, оснащенных независимыми системами электроснабжения, охлаждения и сетевого взаимодействия. Группа узлов создается в одной зоне доступности. После создания все узлы будут помещены в эту зону. Чтобы обеспечить высокий уровень доступности в разных зонах, необходимо создать несколько групп узлов (по одной на каждую зону) и соответствующим образом распространить узлы.

При назначении группы узлов в зону доступности все виртуальные машины, созданные на этом узле, должны быть созданы в той же зоне.

### <a name="use-fault-domains-for-fault-isolation"></a>Использование доменов сбоя для изоляции сбоев

Узел можно создать в определенном домене сбоя. Как и в случае с виртуальными машинами в масштабируемом наборе или группе доступности, узлы в разных доменах сбоя будут размещаться в разных физических стойках в центре обработки данных. При создании группы узлов необходимо указать количество доменов сбоя. При создании узлов в группе узлов необходимо назначить домен сбоя для каждого узла. Виртуальным машинам не требуется назначение домена сбоя.

Домены сбоя не совпадают с размещением. Наличие того же домена сбоя для двух узлов не означает, что они находятся в близком друг к другу.

Домены сбоя входят в область группы узлов. Не следует делать никаких предположений относительно сглаживания между двумя группами узлов (если они находятся в разных зонах доступности).

Виртуальные машины, развернутые на узлах с разными доменами сбоя, будут иметь свои базовые службы управляемых дисков на нескольких метках хранилища, чтобы повысить защиту от сбоев в изоляции.

### <a name="using-availability-zones-and-fault-domains"></a>Использование Зоны доступности и доменов сбоя

Обе возможности можно использовать совместно для обеспечения еще большей изоляции сбоя. В этом случае необходимо указать зону доступности и число доменов сбоя в для каждой группы узлов, назначить домен сбоя каждому из узлов в группе и назначить зону доступности для каждой виртуальной машины.

Приведенный [здесь](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) пример шаблона диспетчер ресурсов использует зоны и домены сбоя для распространения узлов для обеспечения максимальной устойчивости в регионе.

## <a name="maintenance-control"></a>Управление обслуживанием

Инфраструктура, поддерживающая виртуальные машины, иногда может быть обновлена для повышения надежности, производительности, безопасности и запуска новых функций. Платформа Azure пытается максимально уменьшить влияние обслуживания платформы, если это возможно, но клиенты с *конфиденциальными* рабочими нагрузками не могут допускать даже несколько секунд, которые виртуальная машина должна быть заморожена или отключена для обслуживания.

**Управление обслуживанием** предоставляет клиентам возможность пропускать регулярные обновления платформы, запланированные на их отдельных узлах, а затем применять их во время выбора в течение интервала в течение 35 дней.

> [!NOTE]
>  Управление обслуживанием в настоящее время находится на ограниченном этапе предварительной версии и требует процесса адаптации. Примените этот предварительный просмотр, отправив [опрос предварительного утверждения](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u).

## <a name="capacity-considerations"></a>Рекомендации по емкости

После подготовки выделенного узла Azure назначает его физическому серверу. Это гарантирует доступность емкости при необходимости подготовки виртуальной машины. Azure использует всю емкость в регионе (или зоне) для выбора физического сервера для узла. Это также означает, что клиенты могут иметь возможность увеличивать объем выделенного узла, не заботясь о нехватке места в кластере.

## <a name="quotas"></a>Квоты

Квота по умолчанию равна 3000 виртуальных ЦП для выделенных узлов в каждом регионе. Но количество узлов, которые можно развернуть, также ограничивается квотой для семейства размеров виртуальных машин, используемых для узла. Например, подписка с **оплатой по мере** использования может иметь квоту 10 виртуальных ЦП, доступную для ряда Dsv3 size в регионе Восточная часть США. В этом случае необходимо запросить увеличение квоты по крайней мере до 64 виртуальных ЦП, прежде чем можно будет развернуть выделенный узел. Нажмите кнопку **запросить увеличение** в правом верхнем углу, чтобы при необходимости файл запрашивался.

![Снимок экрана со страницей "использование и квоты" на портале](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Дополнительные сведения см. в статье [квоты виртуальной машины виртуальных ЦП](/azure/virtual-machines/windows/quotas).

Бесплатная пробная версия и подписки MSDN не имеют квоты для выделенных узлов Azure.

## <a name="pricing"></a>Стоимость

За каждый выделенный узел начисляются пользователи независимо от того, сколько виртуальных машин развернуто. В месячной выплате вы увидите новый тип ресурса для оплаты узлов. Виртуальные машины на выделенном узле по-прежнему будут отображаться в вашем заявлении, но будут иметь цену 0.

Цена узла устанавливается на основе семейства виртуальных машин, типа (размер оборудования) и региона. Цена узла определяется по сравнению с максимальным размером виртуальной машины, поддерживаемой на узле.

Плата за использование программного обеспечения, хранение и сеть выставляется отдельно от узла и виртуальных машин. Эти оплачиваемые элементы не меняются.

Дополнительные сведения см. на странице [цен на выделенный узел Azure](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>Семейства виртуальных машин и поколения оборудования

Номер SKU определяется для узла и представляет собой ряд размеров и тип виртуальной машины. Вы можете смешивать несколько виртуальных машин различных размеров на одном узле, если они имеют один и тот же размер. Тип — это поколение оборудования, которое в настоящее время доступно в регионе.

Разные `types` для одной и той же серии виртуальных машин будут принадлежать разным поставщикам ЦП и разным поколениям ПРОЦЕССОРов и количеству ядер.

Дополнительные сведения см. на [странице цен](https://aka.ms/ADHPricing) на узел.

Во время действия предварительной версии мы поддерживаем следующие узлы Host Ску\типес: DSv3_Type1 и ESv3_Type1

 
## <a name="host-life-cycle"></a>Жизненный цикл узла


Azure отслеживает состояние работоспособности узлов и управляет им. При запросе к узлу будут возвращены следующие состояния:

| Состояние работоспособности   | Описание       |
|----------|----------------|
| Узел доступен     | Нет известных проблем с вашим узлом.   |
| Основное приложение в расследовании  | У нас возникли проблемы с ведущим узлом, о котором мы ищем. Это переходное состояние, необходимое для того, чтобы Azure попытается определить область и основную причину обнаруженной проблемы. Могут повлиять виртуальные машины, работающие на узле. |
| Ожидание освобождения узла   | Azure не удается восстановить работоспособное состояние узла и попросит повторно развернуть виртуальные машины из этого узла. Если `autoReplaceOnFailure` включен, виртуальные *машины будут* восстановлены в работоспособном оборудовании. В противном случае виртуальная машина может работать на узле, который собирается завершиться сбоем.|
| Узел освобожден  | Все виртуальные машины были удалены с узла. Вы больше не платите за этот узел, так как оборудование было вычислено из вращения.   |

