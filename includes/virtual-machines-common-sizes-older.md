---
title: включение файла
description: включение файла
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541532"
---
В этом разделе содержатся сведения о старых поколениях размеров виртуальных машин. Эти размеры по-прежнему поддерживаются, но не могут получить дополнительную емкость. Существуют более новые или альтернативные размеры, доступные в целом. Ознакомьтесь с [размерами виртуальных машин Windows в Azure](../articles/virtual-machines/windows/sizes.md) или [размерами виртуальных машин Linux в Azure](../articles/virtual-machines/linux/sizes.md) , чтобы выбрать размеры виртуальной машины, которые лучше подходят для ваших нужд.  

Дополнительные сведения об изменении размера виртуальных машин Linux см. в статье [изменение размера виртуальной машины Linux с помощью Azure CLI](../articles/virtual-machines/linux/change-vm-size.md). Если вы используете виртуальные машины Windows и предпочитаете использовать PowerShell, см. раздел [изменение размера виртуальной машины Windows](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Новый рекомендуемый размер**: [Серия Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

Размеры уровня "Базовый" используются преимущественно при разработке рабочих нагрузок и других приложений, для которых не требуется балансировка нагрузки, автомасштабирование и виртуальные машины с высоким потреблением памяти.

|Размер: размер и имя | Виртуальный ЦП |Память|Сетевые адаптеры (макс.)|Максимальный размер временного диска |Макс. количество дисков данных (по 1023 ГБ)|Макс. количество операций ввода-вывода в секунду (300 на диск)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1|768 МБ|2| 20 ГБ|1|1x300|
|A1\Basic_A1|1|1,75 ГБ|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 ГБ|2| 60 ГБ|4|4x300|
|A3\Basic_A3|4|7 ГБ|2| 120 ГБ |8|8x300|
|A4\Basic_A4|8|14 ГБ|2| 240 ГБ |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 поддерживает интерфейс командной строки и PowerShell.

В классической модели развертывания названия некоторых размеров виртуальных машин немного отличаются в интерфейсе командной строки и PowerShell:

* Standard_A0 — "Очень мелкий".
* Standard_A1 — "Мелкий".
* Standard_A2 — "Средний".
* Standard_A3 — "Крупный".
* Standard_A4 — "Очень крупный".

### <a name="a-series"></a>Серия A  

**Новый рекомендуемый размер**: [Серия Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 50—100

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

| Size | Виртуальный ЦП | Память: ГиБ | Временное хранилище (HDD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность дисков данных: Операций ввода-вывода в сек. | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1 |0,768 |20 |1 |1x500 |2 / 100 |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> Размер A0 характеризуется превышением лимита подписки на физическом оборудовании. Только при использовании этого размера другие клиентские развертывания могут повлиять на производительность выполняющейся рабочей нагрузки. Относительная производительность будет ниже ожидаемой (с приблизительной изменчивостью в 15 %).

<br>

### <a name="a-series---compute-intensive-instances"></a>Серия А: экземпляры для ресурсоемких вычислений  

**Новый рекомендуемый размер**: [Серия Av2](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU: 225

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

Экземпляры A8–A11 и серия Н также называются *экземплярами с интенсивным использованием вычислительных ресурсов*. Оборудование, на котором выполняются эти типоразмеры, разработано и оптимизировано для ресурсоемких приложений, в том числе приложений высокопроизводительного вычислительного кластера (HPC) и моделирования. Экземпляры A8-A11 используют процессор Intel Xeon E5-2670 с частотой 2,6 ГГц, а экземпляры серии H используют Intel Xeon E5-2667 v3 с частотой 3,2 ГГц.  

| Size | Виртуальный ЦП | Память: ГиБ | Временное хранилище (HDD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность дисков данных: Операций ввода-вывода в сек. | Максимальное число сетевых адаптеров|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> Для приложений MPI в сети FDR InfiniBand включена выделенная внутренняя сеть RDMA, которая обеспечивает сверхнизкие задержки и высокую пропускную способность.  

<br>

### <a name="d-series"></a>Серия D  

**Новый рекомендуемый размер**: [Серия Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

| Size         | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальная пропускная способность временного хранилища: операций ввода-вывода в секунду / операций чтения в Мбит/с / операций записи в Мбит/с | Максимальное число дисков данных / пропускная способность: Операций ввода-вывода в сек. | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1         | 3.5         | 50             | 3000 / 46 / 23                                           | 4 / 4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |

<sup>1</sup> семейство виртуальных машин может работать на одном из следующих ЦП: 2,2 ГГц Intel Xeon® 2660 v2, 2,4 ГГц Intel Xeon® Haswell-2673 v3 () или 2,3 ГГц Intel XEON® Broadwell-2673 V4 ()  

<br>

### <a name="d-series---memory-optimized"></a>Серия D: оптимизированные для операций в памяти  

**Новый рекомендуемый размер**: [Серия Dv3](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU: 160-250 <sup>1</sup>

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

| Size         | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальная пропускная способность временного хранилища: операций ввода-вывода в секунду / операций чтения в Мбит/с / операций записи в Мбит/с | Максимальное число дисков данных / пропускная способность: Операций ввода-вывода в сек. | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000 / 93 / 46                                           | 8 / 8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8/8000                |

<sup>1</sup> семейство виртуальных машин может работать на одном из следующих ЦП: 2,2 ГГц Intel Xeon® 2660 v2, 2,4 ГГц Intel Xeon® Haswell-2673 v3 () или 2,3 ГГц Intel XEON® Broadwell-2673 V4 ()  

<br>

### <a name="ds-series"></a>Серия DS  

**Новый рекомендуемый размер**: [Серия DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1</sup>

Хранилище класса "Премиум":  Поддерживается

Кэширование хранилища класса Premium:  Поддерживается

| Size | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность временного хранилища с кэшированием: операций ввода-вывода / Мбит/с (размер кэша в Гиб) | Максимальная пропускная способность дисков без кэширования: операций ввода-вывода в секунду / МБит/с | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |4 |4000 / 32 (43) |3200 / 32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16 000 / 128 (172) |12 800 / 128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32 000 / 256 (344) |25 600 / 256 |8 / 4000 |

<sup>1</sup> семейство виртуальных машин может работать на одном из следующих ЦП: 2,2 ГГц Intel Xeon® 2660 v2, 2,4 ГГц Intel Xeon® Haswell-2673 v3 () или 2,3 ГГц Intel XEON® Broadwell-2673 V4 ()  

<br>

### <a name="ds-series---memory-optimized"></a>Серия DS: оптимизированные для операций в памяти  

**Новый рекомендуемый размер**: [Серия DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU: 160-250 <sup>1, 2</sup>

Хранилище класса "Премиум":  Поддерживается

Кэширование хранилища класса Premium:  Поддерживается

| Size | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность временного хранилища с кэшированием: операций ввода-вывода / Мбит/с (размер кэша в Гиб) | Максимальная пропускная способность дисков без кэширования: операций ввода-вывода в секунду / МБит/с | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8000 / 64 (72) |6400 / 64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16 000 / 128 (144) |12 800 / 128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32 000 / 256 (288) |25 600 / 256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64 000 / 512 (576) |51 200 / 512 |8/8000 |

<sup>1</sup> Максимальная возможная пропускная способность дисков (в операциях ввода-вывода в секунду или Мбит/с) виртуальных машин серии DS может быть ограничена из-за количества, размера и чередования подключенных дисков.  Дополнительные сведения см. в статье [Хранилище Azure класса Premium. Проектирование для обеспечения высокой производительности](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> семейство виртуальных машин может работать на одном из следующих ЦП: 2,2 ГГц Intel Xeon® 2660 v2, 2,4 ГГц Intel Xeon® Haswell-2673 v3 () или 2,3 ГГц Intel XEON® Broadwell-2673 V4 ()  

<br>

### <a name="ls-series"></a>Серия Ls

Они оснащены 32 виртуальными ЦП, [процессором Intel® Xeon® семейства E5 версии 3](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Серия Ls обеспечивает такую же производительность ЦП, как и серия виртуальных машин G/GS, и располагает объемом памяти в 8 ГиБ на виртуальный ЦП.

Серия Ls не поддерживает создание локального кэша для увеличения скорости IOPS, достигаемой постоянными дисками данных. Высокая пропускная способность и операции ввода-вывода на локальном диске делают виртуальные машины серии LS идеальными для хранилищ NoSQL, таких как Apache Cassandra и MongoDB, которые реплицируют данные между несколькими виртуальными машинами для обеспечения сохранности в случае сбоя одной виртуальной машины.

ACU: 180–240

Хранилище класса "Премиум":  Поддерживается

Кэширование хранилища класса Premium:  Не поддерживается
 
| Size          | Виртуальный ЦП | Память, ГиБ | Временное хранилище, Гиб | Максимальное число дисков данных | Макс. пропускная способность временного хранилища, операций ввода-вывода в секунду/Мбит/с | Максимальная пропускная способность дисков без кэширования, операций ввода-вывода в секунду/МБит/с | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16 000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20 000 | 

Максимально возможная пропускная способность дисков виртуальных машин серии Ls может быть ограничена из-за числа, размера и чередования любых подключенных дисков. Дополнительные сведения см. в статье [Хранилище Azure класса Premium. Проектирование для обеспечения высокой производительности](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> Экземпляр изолирован на оборудовании, выделенном единственному заказчику.

### <a name="gs-series"></a>Серия GS 

ACU: 180–240 <sup>1</sup>

Хранилище класса "Премиум":  Поддерживается

Кэширование хранилища класса Premium:  Поддерживается

| Size | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальное число дисков данных | Максимальная пропускная способность временного хранилища с кэшированием: операций ввода-вывода / Мбит/с (размер кэша в Гиб) | Максимальная пропускная способность дисков без кэширования: операций ввода-вывода в секунду / МБит/с | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10 000 / 100 (264) |5000 / 125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20 000 / 200 (528) |10 000 / 250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40 000 / 400 (1056) |20 000 / 500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80 000 / 800 (2112) |40 000 / 1,000 |8 / 16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160 000 / 1600 (4224) |80 000 / 2000 |8 / 20 000 |

<sup>1</sup> Максимальная возможная пропускная способность дисков (в операциях ввода-вывода в секунду или Мбит/с) виртуальных машин серии GS может быть ограничена из-за количества, размера и чередования подключенных дисков. Дополнительные сведения см. в статье [Хранилище Azure класса Premium. Проектирование для обеспечения высокой производительности](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> Экземпляр изолирован на оборудовании, выделенном единственному заказчику.

<sup>3</sup> Список доступных размеров ядер ограничен.

<br>

### <a name="g-series"></a>Серия G

ACU: 180–240

Хранилище класса "Премиум":  Не поддерживается

Кэширование хранилища класса Premium:  Не поддерживается

| Size         | Виртуальный ЦП | Память: ГиБ | Временное хранилище (SSD): ГиБ | Максимальная пропускная способность временного хранилища: операций ввода-вывода в секунду / операций чтения в Мбит/с / операций записи в Мбит/с | Максимальное число дисков данных / пропускная способность: Операций ввода-вывода в сек. | Максимальное количество сетевых адаптеров / ожидаемая пропускная способность сети (Мбит/с) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000 / 93 / 46                                           | 8 / 8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000 / 187 / 93                                         | 16 / 16x500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1536          | 24000 / 375 / 187                                        | 32 / 32x500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3072          | 48000 / 750 / 375                                        | 64 / 64x500                     | 8 / 16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96000 / 1500 / 750                                       | 64 / 64x500                     | 8 / 20 000           |

<sup>1</sup> Экземпляр изолирован на оборудовании, выделенном единственному заказчику.
<br>
