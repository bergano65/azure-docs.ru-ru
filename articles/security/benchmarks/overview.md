---
title: Обзор бенчмарка безопасности Azure
description: Обзор бенчмарка безопасности
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2f4b034948605b0a53a0320863608d284719a96d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587538"
---
# <a name="overview-of-the-azure-security-benchmark"></a>Обзор контрольного показателя безопасности Azure

Контрольный ориентир безопасности Azure содержит рекомендации, которые помогут повысить безопасность приложений и данных в Azure.

Этот бенчмарк фокусируется на облачных областях управления. Эти элементы управления соответствуют хорошо известным критериям безопасности, таким как те, которые описаны ВЦентром По вопросам безопасности в Интернете (CIS) Controls Version 7.1.

Следующие элементы управления используются в тесте безопасности Azure: 

- [Безопасность сети](security-control-network-security.md)
- [Ведение журналов и мониторинг](security-control-logging-monitoring.md)
- [Идентификатор и управление доступом](security-control-identity-access-control.md)
- [Защита данных](security-control-data-protection.md)
- [Управление уязвимостями](security-control-vulnerability-management.md)
- [Инвентаризация и управление ресурсами](security-control-inventory-asset-management.md)
- [Настройка безопасности](security-control-secure-configuration.md)
- [Защита от вредоносных программ](security-control-malware-defense.md)
- [Восстановление данных](security-control-data-recovery.md)
- [Реагирование на инциденты](security-control-incident-response.md)
- [Тесты на проникновение и попытки нарушения безопасности "красной командой"](security-control-penetration-tests-red-team-exercises.md)

Вы также можете скачать [электронную таблицу Azure Security Benchmark v1 excel.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)

## <a name="azure-security-benchmark-recommendations"></a>Рекомендации по бенчмарку безопасности Azure 

Каждая рекомендация включает в себя следующую информацию: 

- **Идентификатор Azure:** Идентификатор теста безопасности Azure, соответствующий рекомендации. 
- **CIS ID (ы)**: Рекомендация по бенчмарку СНГ (ы), соответствующая этой рекомендации.  
- **Ответственность**: Независимо от того, отвечает ли заказчик или поставщик услуг (или оба) ответственность за реализацию этой рекомендации. Обязанности по обеспечению безопасности распределяются в общедоступном облаке. Некоторые элементы управления безопасностью доступны только поставщику облачных услуг, и поэтому поставщик несет ответственность за их решение. Это общие наблюдения – для некоторых отдельных служб ответственность будет отличаться от той, что указана в benchmark Безопасности Azure. Эти различия описаны в базовых рекомендациях для отдельной службы. 
- **Детали**: Обоснование рекомендации и ссылки на рекомендации о том, как ее реализовать. Если рекомендация поддерживается Центром безопасности Azure, эта информация также будет указана.

Мы приветствуем вашу подробную обратную связь и активное участие в работе Azure Security Benchmark. Если вы хотите предоставить группе Benchmark прямой вход, [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark)пожалуйста, заполните форму по адресу .

## <a name="next-steps"></a>Next Steps

- Смотрите первый контроль безопасности: [Сетевая безопасность](security-control-network-security.md)
- Прочитайте [введение контрольного показателя безопасности Azure](introduction.md)
- Скачать [электронную таблицу Azure Security Benchmark v1 Excel](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
