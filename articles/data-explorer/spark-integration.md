---
title: Соединитель Azure Data Explorer для Apache Spark
description: Сведения о том, как использовать соединитель Azure Data Explorer для Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55825191"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Соединитель Azure Data Explorer для Apache Spark

При использовании Azure Data Explorer и Apache Spark вы можете выполнять сборку быстрых и масштабируемых приложений, ориентированных на управляемые данными сценарии, такие как машинное обучение, сценарий извлечения, преобразования и загрузки (ETL) и Log Analytics.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать соединитель, приложение должно включать:

* пакет SDK Java 1.8;
* Maven 3.x;
* Spark, версии 2.3.2 или выше.

## <a name="link-to-data-explorer"></a>Связывание с обозревателем данных

Свяжите приложение Scala и Java, использующее определение проектов Maven, с помощью следующего артефакта:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

В Maven свяжите, используя следующий блок кода:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Команды сборки

Для создания JAR-файла и выполнения всех тестов выполните команду:

```
mvn clean package
```

Для создания JAR-файла, выполнения всех тестов и установки его в локальный репозиторий Maven выполните команду:

```
mvn clean install
```