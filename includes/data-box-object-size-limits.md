---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88655151"
---
Ниже приведены размеры объектов Azure, которые могут быть записаны. Убедитесь, что все передаваемые файлы соответствуют этим ограничениям.

| Тип объекта Azure | Ограничение по умолчанию                                             |
|-------------------|-----------------------------------------------------------|
| Блочный BLOB-объект        | ~4,75 ТиБ                                                 |
| Страничный BLOB-объект         | 8 ТиБ <br> Каждый файл, отправляемый в формате страничного BLOB-объекта, должен быть выровнен по размеру 512 байт (целое кратное). В противном случае произойдет сбой передачи. <br> VHD и VHDX выровнены по размеру 512 байт. |
| Файлы Azure        | 1 ТиБ                                                      |
| Управляемые диски     | 4 ТиБ <br> Дополнительные сведения о размере и ограничениях см. в следующих статьях: <li>[Целевые показатели масштабируемости для стандартных твердотельных накопителей](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Целевые показатели масштабируемости твердотельных накопителей Premium](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[Целевые показатели масштабируемости для жестких дисков уровня "Стандартный"](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[Цены и выставление счетов за управляемые диски](../articles/virtual-machines/disks-types.md#billing)</li>  
