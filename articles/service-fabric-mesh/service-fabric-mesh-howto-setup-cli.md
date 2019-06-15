---
title: Настройка CLI для сетки Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке CLI для Сетки Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: c716ae0a2bb30e7e8eb249a1d230097efc0d3795
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097799"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric
Интерфейс командной строки (CLI) вложенной службы "Сетка Service Fabric" требуется для развертывания и администрирования ресурсов локально в этой службе. 

Существует три вида интерфейса командной строки, который может использоваться. Они перечислены в таблице, приведенной ниже. 

| Модуль CLI | Целевое окружение |  Описание | 
|---|---|---|
| az mesh | Служба "Сетка Azure Service Fabric" | Основной интерфейс командной строки для развертывания приложений и управления ресурсами в среде сетки Azure Service Fabric. 
| sfctl | Локальные кластеры | Служба Service Fabric CLI позволяет развертывать и тестировать ресурсы Service Fabric для локальных кластеров.  
| Интерфейс командной строки Maven | Локальные кластеры и Сетка Azure Service Fabric | Это оболочка вокруг `az mesh` и `sfctl` , разработчики Java могут использовать возможности знакомых командной строки для среды разработки локальных и Azure.  

В режиме предварительной версии CLI для Сетки Azure Service Fabric записывается как расширение для Azure CLI. Вы можете установить его в Azure Cloud Shell или локально в Azure CLI. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Установка интерфейса командной строки Сетки Azure Service Fabric
1. Необходимо установить Azure CLI версии 2.0.43 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если необходимо установить или обновить CLI, ознакомьтесь с [этим разделом][azure-cli-install].

2. Установите модуль расширения CLI для Сетки Azure Service Fabric. 

    ```azurecli-interactive
    az extension add --name mesh
    ```

3. Обновите существующий модуль службы "Сетка Azure Service Fabric", используя следующую команду.

    ```azurecli-interactive
    az extension update --name mesh
    ```

## <a name="install-the-service-fabric-cli-sfctl"></a>Установка интерфейса командной строки Service Fabric (sfctl) 

Следуйте инструкциям раздела [Настройка интерфейса командной строки Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli). Модуль **sfctl** можно использовать для развертывания приложений на основе модели ресурсов для кластеров Service Fabric на локальном компьютере. 

## <a name="install-the-maven-cli"></a>Установка интерфейса командной строки Maven 

Чтобы использовать интерфейс командной строки Maven, необходимо установить на компьютере: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Интерфейс командной строки Сетка Azure (az mesh) для Сетки Azure Service Fabric 
* SFCTL (sfctl) для локальных кластеров 

Интерфейс командной строки Maven для Service Fabric все еще находится в предварительной версии. 

Чтобы использовать подключаемый модуль Maven в приложении Maven Java, добавьте следующий фрагмент кода в файл pom.xml:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Дополнительные сведения см. в разделе [Руководство по интерфейсу командной строки Maven](service-fabric-mesh-reference-maven.md).

## <a name="next-steps"></a>Дальнейшие действия

Вы также можете настроить [среду разработки Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md).

Найдите ответы на [часто задаваемые вопросы и способы решения распространенных проблем](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
