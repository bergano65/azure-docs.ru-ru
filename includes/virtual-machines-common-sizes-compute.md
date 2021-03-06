---
title: включение файла
description: включение файла
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 11/04/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 21b982389b186e949b21352f4b11bd6b4aa06dcb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279211"
---
<!-- F-series, Fs-series* -->

Размеры виртуальных машин, оптимизированных для вычислений, имеют высокое соотношение ресурсов ЦП и памяти. Эти размеры хорошо подходят для веб-серверов среднего трафика, сетевых устройств, процессов пакетной обработки и серверов приложений. В этой статье содержатся сведения о количестве виртуальных ЦП, дисков данных и сетевых карт. Он также содержит сведения о пропускной способности хранилища и пропускной способности сети для каждого размера в этой группе.

Серия серия fsv2 основана на процессоре Intel® Xeon® Platinum 8168. В нем реализована постоянная частота ядра Turbo 3,4 ГГц и максимальная частота одноядерных Turbo с частотой в 3,7 ГГц. Intel® AVX-512. инструкции являются новыми для масштабируемых процессоров Intel. Эти инструкции обеспечивают увеличение производительности до двух рабочих нагрузок векторной обработки как с одиночной, так и с двойной точностью операций с плавающей запятой. Иными словами, они действительно быстро используются для любой вычислительной рабочей нагрузки.

Предлагая более низкую ориентировочную стоимость часа, серия Fsv2 обеспечивает наилучшее соотношение цены и производительности в портфеле Azure в единицах вычисления Azure (ACU) на виртуальный ЦП.

## <a name="fsv2-series-sup1sup"></a>Серия FSv2<sup>1</sup>

ACU: 195–210

Хранилище класса Premium: поддерживается

Кэширование хранилища класса Premium: поддерживается

| Размер             | Число виртуальных ЦП | Память, ГиБ | Временное хранилище (SSD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность хранилища с кэшированием и временного хранилища: операций ввода-вывода в секунду / МБит/с (размер кэша указан в ГиБ) | Максимальная пропускная способность дисков без кэширования: операций ввода-вывода в секунду / МБит/с | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
|------------------|--------|-------------|----------------|----------------|--------------------------|--------------------------|-------------------------|
| Standard_F2s_v2  | 2      | 4\.           | 16             | 4\.              | 4000 / 31 (32)           | 3200 / 47                | 2/875                 |
| Standard_F4s_v2  | 4\.      | 8           | 32             | 8              | 8000 / 63 (64)           | 6400 / 95                | 2 / 1750               |
| Standard_F8s_v2  | 8      | 16          | 64             | 16             | 16000 / 127 (128)        | 12800 / 190              | 4 / 3500               |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 / 255 (256)        | 25600 / 380              | 4 / 7000               |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 / 512 (512)        | 51200 / 750              | 8 / 14000              |
| Standard_F48s_v2 | 48     | 96          | 384            | 32             | 96000/768 (768)        | 76800/1100             | 8 / 21000              |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 / 1024 (1024)     | 80000 / 1100             | 8 / 28000              |
| Standard_F72s_v2<sup>2,&nbsp;3</sup> | 72 | 144 | 576         | 32             | 144000 / 1152 (1520)     | 80000 / 1100             | 8/30 000              |

<sup>1</sup> виртуальные машины серии серия fsv2 имеют технологию Intel® технологии Hyper-Threading.

<sup>2</sup> для использования более 64 виртуальных ЦП требуется одна из этих поддерживаемых гостевых операционных систем: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 и Red Hat Enterprise Linux, CentOS 7,3 или Oracle Linux 7,3 с LIS 4.2.1.

<sup>3</sup> Экземпляр изолирован на оборудовании, выделенном единственному заказчику.
