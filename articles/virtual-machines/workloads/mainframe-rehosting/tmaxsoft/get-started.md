---
title: Начало работы с TmaxSoft OpenFrame на виртуальных машинах Azure
description: Повторное размещение вашей IBM z/OS рабочих нагрузок мэйнфреймов с помощью TmaxSoft OpenFrame среды на виртуальных машинах Azure (ВМ).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61485543"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Начало работы с TmaxSoft OpenFrame в Azure

Использовать существующие ресурсы мэйнфреймов и переместите их в Microsoft Azure с помощью TmaxSoft OpenFrame. Это популярное решение повторного размещения создает эмуляции среды в Azure, что позволяет быстро перенести приложения. Переформатирование не является обязательным.

## <a name="openframe-rehosting-environment"></a>OpenFrame повторного размещения среды

Настройка OpenFrame среды в Azure для разработки, демонстрации, тестирования или производственных рабочих нагрузок. Как показано на следующем рисунке, OpenFrame включает несколько компонентов, которые создают среде эмуляции мэйнфреймов в Azure. Например веб-служб OpenFrame замените по промежуточного слоя мэйнфреймов например IBM клиента сведения управления System (CICS). Пакет OpenFrame, с его компонентом TJES заменяет подсистемы запись задания мэйнфреймов IBM (JES). 

![Процесс повторного размещения OpenFrame](media/openframe-01.png)

> [!NOTE]
> Чтобы запустить OpenFrame среды в Azure, необходимо иметь лицензию продукта или пробной лицензии из TmaxSoft.

## <a name="openframe-components"></a>Компоненты OpenFrame

Следующие компоненты являются частью OpenFrame среды в Azure.

- **Средства миграции** включая OFMiner, это решение, которое анализирует ресурсы мэйнфреймов и затем переносит их в Azure.
- **Компиляторы**, включая OFCOBOL, компилятор интерпретирует программы мэйнфрейме COBOL. OFPLI, который интерпретирует мэйнфреймов PL / я программы; и OFASM, компилятор интерпретирует программы ассемблер мэйнфреймов.
- **Внешний интерфейс** компонентов, включая решения пользователя Enterprise Java (JEUS) сервера веб-приложений, который сертифицирован для Java Enterprise Edition 6.OFGW и OpenFrame компонент шлюза, который предоставляет 3270 прослушиватель.
- **Приложение** среды. Основной OpenFrame является по промежуточного слоя, который управляет всей системы. OpenFrame сервера типа C Запускает заменяет по промежуточного слоя и IBM CICS для мэйнфреймов.
- **Реляционная база данных**, например Tibero (как показано), базы данных Oracle, Microsoft SQL Server, IBM Db2 или MySQL. Приложения OpenFrame использовать протокол Open Database Connectivity (ODBC) для взаимодействия с базой данных.
- **Безопасность** через TACF, модуль службы, который управляет доступом пользователей к системам и ресурсам. 
- **OFManager** — решение, которое включает OpenFrame операции и функции управления в среде веб.

![Архитектура OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Дальнейшие действия

- [Установка TmaxSoft OpenFrame в Azure](./install-openframe-azure.md)
