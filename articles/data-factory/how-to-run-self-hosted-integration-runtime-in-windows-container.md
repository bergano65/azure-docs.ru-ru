---
title: Запуск Integration Runtime Self-Hosted в контейнере Windows
description: Узнайте, как запускать Integration Runtime Self-Hosted в контейнере Windows.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927582"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Запуск Integration Runtime Self-Hosted в контейнере Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

В этой статье объясняется, как запустить Self-Hosted Integration Runtime в контейнере Windows.
Фабрика данных Azure предоставляет официальную поддержку контейнеров Windows Self-Hosted Integration Runtime. Вы можете скачать исходный код сборки DOCKER и объединить процесс создания и запуска в собственном конвейере непрерывной доставки. 

## <a name="prerequisites"></a>Предварительные требования 
- [Требования к контейнеру Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- DOCKER версии 2,3 и более поздней 
- Self-Hosted Integration Runtime версии 4.11.7512.1 и более поздней 
## <a name="get-started"></a>Начало работы 
1.  Установка DOCKER и включение контейнера Windows 
2.  Загрузка исходного кода со страницы https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Скачайте последнюю версию Шир в папку "Шир" 
4.  Откройте папку в оболочке: 
```console
cd "yourFolderPath"
```

5.  Создание образа Windows docker: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Запустить контейнер docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> Для этой команды AUTH_KEY является обязательным. NODE_NAME, ENABLE_HA и HA_PORT являются необязательными. Если значение не задано, команда будет использовать значения по умолчанию. Значение по умолчанию ENABLE_HA равно false, а HA_PORT — 8060.

## <a name="container-health-check"></a>Проверка работоспособности контейнера 
Через 120 секунд средство проверки работоспособности будет выполняться периодически каждые 30 секунд. Он обеспечит состояние работоспособности IR для модуля контейнера. 

## <a name="limitations"></a>Ограничения
В настоящее время не поддерживаются следующие функции при запуске Self-Hosted Integration Runtime в контейнере Windows:
- Прокси-сервер HTTP 
- Зашифрованный обмен данными между узлами с помощью TLS/SSL-сертификата 
- Создание и Импорт резервной копии 
- Служба управляющей программы 
- Автоматическое обновление 

### <a name="next-steps"></a>Дальнейшие действия
- Ознакомьтесь с [основными понятиями среды выполнения интеграции в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Узнайте, как [создать локальную среду выполнения интеграции на портале Azure](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


