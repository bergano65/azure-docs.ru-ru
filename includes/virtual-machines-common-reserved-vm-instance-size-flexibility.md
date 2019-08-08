---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857476"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Гибкость размеров виртуальных машин при использовании зарезервированных экземпляров виртуальных машин

При использовании зарезервированного экземпляра виртуальной машины, оптимизированного для гибкости размера экземпляра, резервирование, которое приобретается, может применяться к размерам виртуальных машин той же группы серий размеров. Например, если приобретается резервирование для размера виртуальной машины, указанного в таблице серии DSv2, такого как Standard_DS5_v2, скидка на резервирование может применяться к остальным четырем размерам, указанным в этой же таблице:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Но эта скидка не применяется к размерам виртуальных машин, которые перечислены в разных таблицах, например в таблице верхней памяти серии DSv2: Standard_DS11_v2, Standard_DS12_v2 и т. д.

В группе серий размеров количество виртуальных машин, на которые распространяется скидка на резервирование, зависит от размера виртуальной машины, которая выбирается, когда приобретается резервирование. Также оно зависит от размеров используемых виртуальных машин. В столбце коэффициента, который имеется в следующих таблицах, показан результат сравнения относительной нагрузки для каждого размера виртуальной машины этой группы. Используйте значение коэффициента, чтобы вычислить, как скидка на резервирование применяется к используемым виртуальным машинам.

## <a name="examples"></a>Примеры

В следующих примерах используются размеры и коэффициенты таблицы для серии DSv2.

 Вы можете приобрести зарезервированный экземпляр виртуальной машины размера Standard_DS4_v2, в котором коэффициент или относительная нагрузка, в сравнении с другими размерами этого ряда, составляет 8.

- Сценарий 1. Запуск восьми виртуальных машин размера Standard_DS1_v2 с коэффициентом 1. Скидка на резервирование применяется ко всем восьми виртуальным машинам.
- Сценарий 2. Запуск двух виртуальных машин размера Standard_DS2_v2 р с коэффициентом 2 для каждой. Также запускается виртуальная машина размера Standard_DS3_v2 с коэффициентом 4. Общая нагрузка составляет 2+2+4=8. Таким образом, скидка на резервирование применяется ко всем трем виртуальным машинам.
- Сценарий 3. Запуск виртуальной машины размера Standard_DS5_v2 с коэффициентом 16. Скидка на резервирование применяется к половине стоимости вычислений виртуальной машины.

В следующих разделах показано, какие размеры находятся в той же группе серий размеров при покупке зарезервированного экземпляра виртуальной машины, оптимизированного для гибкости размера экземпляра.

## <a name="b-series"></a>Серия B

| Size | Коэффициент|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин Azure серии B с накапливаемыми ресурсами](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Серия B с максимальной памятью

| Size | Коэффициент|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин Azure серии B с накапливаемыми ресурсами](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Серия D

| Size | Коэффициент|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Серия D с максимальной памятью

| Size | Коэффициент|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Серия Ds

| Size | Коэффициент|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Серия Ds с максимальной памятью

| Size | Коэффициент|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Серия DSv2

| Size | Коэффициент|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Серия DSv2 с максимальной памятью

| Size | Коэффициент|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>DSv2 высокой изоляции памяти

| Size | Коэффициент|
|---|---|
|Standard_DS15i_v2|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Серия DSv3

| Size | Коэффициент|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Серия Dv2

| Size | Коэффициент|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Серия Dv2 с максимальной памятью

| Size | Коэффициент|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Dv2 высокой изоляции памяти

| Size | Коэффициент|
|---|---|
|Standard_D15i_v2|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Серия Dv3

| Size | Коэффициент|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин общего назначения](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>Серия ESv3

| Size | Коэффициент|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>Ряд изолированных рядов ESv3

| Size | Коэффициент|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Серия Ev3

| Size | Коэффициент|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Ряд изолированных рядов Ev3

| Size | Коэффициент|
|---|---|
|Standard_E64i_v3|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Серия F

| Size | Коэффициент|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Серия FS

| Size | Коэффициент|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Дополнительные сведения см. в статье [Размеры виртуальных машин предыдущих поколений](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Серия Fsv2

| Size | Коэффициент|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для вычислений](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>Серия H

| Size | Коэффициент|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Серия H с максимальной памятью

| Size | Коэффициент|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>Серия HCS

| Size | Коэффициент|
|---|---|
|Standard_HC44rs|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Серия ХБС

| Size | Коэффициент|
|---|---|
|Standard_HB60rs|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Серия ХБС

| Size | Коэффициент|
|---|---|
|Standard_HB60rs|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин Linux, оптимизированных для HPC](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>Серия H с низкой задержкой

| Size | Коэффициент|
|---|---|
|Standard_H16r|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в хранилище](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>Большая память серии H с низкой задержкой

| Size | Коэффициент|
|---|---|
|Standard_H16mr|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в хранилище](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>Серия H

| Size | Коэффициент|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в хранилище](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>Серия LSv2

| Size | Коэффициент|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в хранилище](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>Серия M

| Size | Коэффициент|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Дробная серия M

| Size | Коэффициент|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Дробная серия M с максимальной памятью

| Size | Коэффициент|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Долгосрочная дробная серия M

| Size | Коэффициент|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Серия M с максимальной памятью

| Size | Коэффициент|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>Серия MS с частичными ресурсами, небольшая

| Size | Коэффициент|
|---|---|
|Standard_M32ls|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>Большой объем памяти для ряда MSv2

| Size | Коэффициент|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>Серия MSv2

| Size | Коэффициент|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для операций в памяти](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Серия NC

| Size | Коэффициент|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Серия NCv2

| Size | Коэффициент|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Серия NCv3

| Size | Коэффициент|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Серия ND

| Size | Коэффициент|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Серия NV

| Size | Коэффициент|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>Серия NVSv3

| Size | Коэффициент|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Низкая задержка серии NDS

| Size | Коэффициент|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>Серия NCSv3 с низкой задержкой

| Size | Коэффициент|
|---|---|
|Standard_NC24rs_v3|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>Серия NCSv2 с низкой задержкой

| Size | Коэффициент|
|---|---|
|Standard_NC24rs_v2|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>Серия с низкой задержкой для ряда NC

| Size | Коэффициент|
|---|---|
|Standard_NC24r|1|

Дополнительные сведения см. в статье [Размеры виртуальных машин, оптимизированных для GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





