---
title: Настройка Windows приложения Java — служба приложений Azure | Документация Майкрософт
description: Узнайте, как настроить приложения Java под управлением Windows экземпляров по умолчанию в службе приложений Azure.
keywords: Служба приложений Azure, веб-приложения, Microsoft windows, открытым исходным кодом java
services: app-service
author: jasonfreeberg
manager: jeconnock
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe;cephalin
ms.custom: seodec18
ms.openlocfilehash: 82e8936a888cbc99088ab18423e55dd57a3c2e77
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604145"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Настройка Windows приложения Java для службы приложений Azure

Служба приложений Azure позволяет разработчикам быстро создавать, развертывать и масштабировать их Tomcat Java или Java Standard Edition (SE) упаковываются веб-приложения на полностью управляемую службу на базе Windows. Возможно развертывание приложений с подключаемыми модулями Maven из командной строки или в редакторах, например Visual Studio Code, Eclipse или IntelliJ.

Это руководство содержит основные понятия и инструкции для разработчиков Java, использование в службе приложений. Если вы раньше не использовали службы приложений Azure, мы рекомендуем ознакомиться с [быстрого запуска Java](app-service-web-get-started-java.md) первого. На общие вопросы об использовании службы приложений, не требующих разработки Java в [приложения службы Windows, часто задаваемые вопросы о](faq-configuration-and-management.md).

> [!NOTE]
> Не можете найти нужную информацию? См. в разделе [ОС Windows часто задаваемые вопросы о](faq-configuration-and-management.md) или [руководство по настройке Java Linux](containers/configure-language-java.md) сведения о развертывании и обеспечении безопасности приложения Java.

## <a name="configuring-tomcat"></a>Настройка Tomcat

Внесение изменений в Tomcat `server.xml` или другие файлы конфигурации сначала запишите ваш основной номер версии Tomcat на портале.

1. Найдите в домашнем каталоге Tomcat для вашей версии, выполнив `env` команды. Поиск переменной среды, которая начинается с `AZURE_TOMCAT`и соответствует вашей основной номер версии. Например `AZURE_TOMCAT85_HOME` указывает на каталог Tomcat для Tomcat 8.5.
1. После определения в домашнем каталоге Tomcat для вашей версии, скопируйте в каталог конфигурации для `D:\home`. Например если `AZURE_TOMCAT85_HOME` значение `D:\Program Files (x86)\apache-tomcat-8.5.37`, полный путь каталога, скопированного конфигурации будет иметь `D:\home\tomcat\conf`.

Наконец, перезапустите службу приложений. Ваши развертывания должно передаваться `D:\home\site\wwwroot\webapps` как и раньше.

## <a name="java-runtime-statement-of-support"></a>Заявление о поддержке среды выполнения Java

### <a name="jdk-versions-and-maintenance"></a>Версии JDK и обслуживание

Azure поддерживает пакет Java Development Kit (JDK) [Zulu](https://www.azul.com/downloads/azure-only/zulu/), предоставляемый компанией [Azul Systems](https://www.azul.com/).

Основной номер версии обновления будут предоставляться через новые параметры среды выполнения в службе приложений Azure для Windows. Пользователи, устанавливающие более новые версии Java посредством настройки развернутой службы приложений, несут ответственность за тестирование выбранных обновлений для основного номера версии и их соответствие своим потребностям.

Каждый квартал в поддерживаемые пакеты JDK автоматически вносятся исправления. Это происходит в январе, апреле, июле и октябре.

### <a name="security-updates"></a>обновления для системы безопасности;

Исправления для устранения серьезных уязвимостей в системе безопасности будут выпускаться по мере выпуска компанией Azul Systems. "Серьезными" считаются уязвимости с базовым индексом не меньше 9.0 в [NIST Common Vulnerability Scoring System версии 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Нерекомендуемые версии и прекращение использования

Если планируется прекращение использования какой-либо поддерживаемой среды выполнения Java, то разработчики для Azure, использующие эту среду выполнения, получат соответствующее уведомление по крайней мере за шесть месяцев до прекращения использования.

### <a name="local-development"></a>Локальная разработка

Разработчики могут скачать выпуск Production Edition пакета Azul Zulu Enterprise JDK для локальной разработки с [сайта загрузки Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Поддержка разработки

Поддержка [с поддержкой Azure Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) доступна через Microsoft при разработке для Azure или [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) с [указанием план поддержки Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Поддержка времени выполнения

Разработчики могут [сообщить о проблеме](/azure/azure-supportability/how-to-create-azure-support-request) с Azul Zulu JDK в службу поддержки Azure при наличии [соответствующего плана поддержки](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Дальнейшие действия

Этот раздел содержит инструкции среда выполнения Java поддержки для службы приложений Azure на Windows.

- Дополнительные сведения о размещении веб-приложений в службе приложений Azure см. в разделе [Обзор службы приложений](overview.md).
- Сведения о Java на Azure разработки см. в разделе [Azure для Java центра разработки](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
