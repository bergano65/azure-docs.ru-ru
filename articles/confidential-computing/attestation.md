---
title: Аттестация енклавес в Azure
description: Узнайте, как можно использовать аттестацию для проверки безопасности доверенной среды конфиденциальных вычислений.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/22/2020
ms.author: JenCook
ms.openlocfilehash: 70a17aacde67744eae74ca263200f2c65fbd300a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90997832"
---
# <a name="attesting-sgx-enclaves"></a>Аттестация Енклавес SGX

Конфиденциальные вычисления в Azure предлагают виртуальные машины на основе Intel SGX, которые могут изолировать часть кода или данных. При работе с этими [енклавесми](confidential-computing-enclaves.md)необходимо проверить и проверить надежность доверенной среды. Такая проверка выполняется в ходе аттестации. 

## <a name="overview"></a>Обзор 

Аттестация позволяет проверяющей стороне получить дополнительные гарантии того, что программное обеспечение: (1) выполняется только в анклаве и (2) этот анклав содержит актуальное и защищенное содержимое. Например, анклав требует, чтобы аппаратное обеспечение создавало учетные данные, которые подтверждают наличие анклава на используемой платформе. Такой отчет можно передать в другой анклав, который подтвердит, что отчет был создан на той же платформе.

![код аттестации в анклава](media/attestation/attestation.png)



Аттестация должна быть реализована с использованием защищенной службы аттестации, которая совместима с программным и аппаратными обеспечением системы. Ниже приведены некоторые примеры служб, которые можно использовать.

- [Аттестация Microsoft Azure (Предварительная версия)](https://docs.microsoft.com/azure/attestation/overview) или
- [Службы аттестации и подготовки Intel](https://software.intel.com/sgx/attestation-services)


они совместимы с конфиденциальными вычислениями в Azure с использованием инфраструктуры Intel SGX. 

## <a name="next-steps"></a>Дальнейшие шаги
Попробуйте [Microsoft Azure примеры аттестации для приложений, поддерживающих анклава](https://docs.microsoft.com/samples/azure-samples/microsoft-azure-attestation/sample-code-for-intel-sgx-attestation-using-microsoft-azure-attestation/).