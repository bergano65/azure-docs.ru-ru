---
title: Java и базовая ОС для приложений микрослужб облачной службы Azure для весны
description: Принципы поддержания работоспособности Java и базовой операционной системы для приложений микрослужб облачной службы Azure весны
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: a8e1d43138e0b7481ebb89d747fa26df9470a09f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037141"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java и базовая операционная система для приложений микрослужбы Spring
Ниже приведены принципы поддержания работоспособности Java и базовой операционной системы для приложений пружинных микрослужб.
## <a name="principles-for-healthy-java-and-base-os"></a>Принципы работоспособности Java и базовой ОС
* Должна быть той же базовой операционной системой на разных уровнях — "базовый" | Стандартный | Приоритет.
    * Сейчас приложения в Azure с пружинным облаком используют сочетание Debian 10 и Ubuntu 18,04.
    * Служба сборки VMware использует Ubuntu 18,04.
* Должна быть той же базовой операционной системой независимо от начальных точек развертывания — источник | JAR
    * Сейчас приложения в Azure с пружинным облаком используют сочетание Debian 10 и Ubuntu 18,04.
* Базовая операционная система должна быть свободна от уязвимых мест безопасности.
    * В Debian 10 базовая операционная система 147 Open CVE.
    * Базовая операционная система Ubuntu 18,04 имеет 132 Open CVE.
* Должен использовать JRE-без монитора.
    * Сейчас приложения в Azure с пружинным облаком используют JDK. JRE — это меньшее изображение.
* Используйте самые последние сборки Java.
    * Сейчас приложения в Azure с пружинным облаком используют Java 8 Build 242. Это устаревшая сборка.
 
Azul системы постоянно сканирует изменения базовых операционных систем и регулярно обновляйте последние версии образов. Azure Веснного облака ищет изменения в изображениях и постоянно обновляет их в развертываниях.
 
## <a name="faq-for-azure-spring-cloud"></a>Вопросы и ответы по Azure Spring Cloud

* Какие версии Java поддерживаются? Основная версия и номер сборки.
    * Поддержка версий LTS — Java 8 и 11.
    * Использует последнюю сборку, например, прямо сейчас, Java 8 build 252 и Java 11 Build 7.
* Кто создал эти среды выполнения Java?
    * Azul системы.
* Какова базовая операционная система для образов?
    * Ubuntu 20,04 LTS (фокус Фосса). Приложения продолжат поддерживать последнюю версию LTS Ubuntu.
    * См. раздел [Ubuntu 20,04 LTS (фокус Фосса)](http://releases.ubuntu.com/focal/) .
* Как скачать поддерживаемую среду выполнения Java для локального разработки? 
    * См. статью [Установка JDK для Azure и Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install)
* Как можно получить поддержку проблем на уровне среды выполнения Java?
    * Откройте запрос в службу поддержки Azure.
 
## <a name="default-deployment-on-azure-spring-cloud"></a>Развертывание по умолчанию в Azure Веснного облака

> ![Развертывание по умолчанию](media/spring-cloud-principles/spring-cloud-default-deployment.png)
 
## <a name="next-steps"></a>Дальнейшие действия
* [Краткое руководство. Запуск существующего приложения Azure Spring Cloud с помощью портала Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Долгосрочная поддержка Java для Azure и Azure Stack](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)
