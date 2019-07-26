---
title: Настройка приложений Windows на Java — служба приложений Azure | Документация Майкрософт
description: Узнайте, как настроить приложения Java для запуска на экземплярах Windows по умолчанию в службе приложений Azure.
keywords: служба приложений Azure, веб-приложение, Windows, OSS, Java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: c1ea306d8a6b5c1876ac6a9288820e1592dbfda6
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498513"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Настройка приложения Windows Java для службы приложений Azure

Служба приложений Azure позволяет разработчикам Java быстро создавать, развертывать и масштабировать Упакованные веб-приложения Tomcat или Java Standard Edition (SE) в полностью управляемой службе на основе Windows. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это краткое описание содержит основные понятия и инструкции для разработчиков Java, использующих службу приложений. Если вы никогда не использовали службу приложений Azure, сначала ознакомьтесь с кратким руководством по [Java](app-service-web-get-started-java.md) . Ответы на часто задаваемые вопросы об использовании службы приложений, не относящейся к разработке Java, приведены в статье [вопросы и ответы по Windows в службе приложений](faq-configuration-and-management.md).

> [!NOTE]
> Не можете найти нужную информацию? Сведения о развертывании приложения Java и обеспечении его безопасности см. в разделе [часто задаваемые вопросы об ОС Windows](faq-configuration-and-management.md) или в [программе настройки Java Linux](containers/configure-language-java.md) .

## <a name="configuring-tomcat"></a>Настройка Tomcat

Чтобы изменить Tomcat `server.xml` или другие файлы конфигурации, сначала запишите основную версию Tomcat на портале.

1. Найдите корневой каталог Tomcat для своей версии, выполнив `env` команду. Найдите переменную среды, которая начинается с `AZURE_TOMCAT`и соответствует основной версии. Например, `AZURE_TOMCAT85_HOME` указывает на каталог Tomcat для Tomcat 8,5.
1. Определив корневой каталог Tomcat для своей версии, скопируйте каталог конфигурации в `D:\home`папку. Например, если `AZURE_TOMCAT85_HOME` имело `D:\Program Files (x86)\apache-tomcat-8.5.37`значение, новый путь к копируемому каталогу будет иметь `D:\home\apache-tomcat-8.5.37`вид.

Наконец, перезапустите службу приложений. Развертывания должны быть так же `D:\home\site\wwwroot\webapps` , как и раньше.

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Обновления основной версии будут предоставляться с помощью новых параметров среды выполнения в службе приложений Azure для Windows. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>Обновления для системы безопасности

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Поддержка разработки

Поддержка продуктов для [Zulu JDK, поддерживаемых Azure](https://www.azul.com/downloads/azure-only/zulu/) , доступна в корпорации Майкрософт при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) с [полным планом поддержки Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Следующие шаги

В этом разделе содержится инструкция поддержки Java для службы приложений Azure в Windows.

- Дополнительные сведения о размещении веб-приложений с помощью службы приложений Azure см. в статье [Обзор службы приложений](overview.md).
- Дополнительные сведения о Java в разработке Azure см. в статье [центр разработки для Java в Azure](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
