---
title: Управление зависимостями JAR - Azure HDInsight
description: В этой статье рассматриваются рекомендации по управлению зависимостями Java Archive (JAR) для приложений HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: da3387dd9846847f7643ded43c8cbff8ed8b166e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77135737"
---
# <a name="jar-dependency-management-best-practices"></a>Лучшие практики управления зависимыми отправлениями JAR

Компоненты, установленные в кластерах HDInsight, зависят от сторонних библиотек. Как правило, на конкретную версию общих модулей, таких как Guava, ссылаются эти встроенные компоненты. Когда вы отправляете заявку с его зависимостями, это может привести к конфликту между различными версиями одного и того же модуля. Если версия компонента, на которую вы ссылаетесь в classpath во-первых, встроенные компоненты могут бросать исключения из-за несовместимости версии. Однако, если встроенные компоненты сначала вводят свои зависимости в `NoSuchMethod`classpath, приложение может выбрасывать ошибки типа.

Чтобы избежать конфликта версий, подумайте о затенении зависимостей приложений.

## <a name="what-does-package-shading-mean"></a>Что означает затенение пакетов?
Затенение обеспечивает способ включения и переименования зависимостей. Он перемещает классы и перезаписывает затронутые типкода и ресурсы для создания частной копии ваших зависимостей.

## <a name="how-to-shade-a-package"></a>Как затенить пакет?

### <a name="use-uber-jar"></a>Используйте убер-банк
Uber-банк представляет собой единый файл банки, который содержит как банку приложения, так и его зависимости. Зависимости в Uber-банке по умолчанию не затенены. В некоторых случаях это может привести к конфликту версии, если другие компоненты или приложения ссылаются на другую версию этих библиотек. Чтобы избежать этого, вы можете создать файл Uber-Jar с некоторыми (или всеми) затененных зависимостями.

### <a name="shade-package-using-maven"></a>Пакет теней с использованием Maven
Maven может создавать приложения, написанные как на Java, так и в Scala. Maven-shade-plugin может помочь вам создать затененный убер-банк легко.

Приведенный ниже пример `pom.xml` показывает файл, который был обновлен для тени пакета с помощью maven-тени-плагина.  Раздел XML `<relocation>…</relocation>` перемещает классы из пакета `com.google.guava` в пакет, `com.google.shaded.guava` перемещая соответствующие записи файлов JAR и переписывая затронутый байткод.

После `pom.xml`изменения, вы `mvn package` можете выполнить, чтобы построить затененный убер-банк.

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>shade</goal>
            </goals>
            <configuration>
              <relocations>
                <relocation>
                  <pattern>com.google.guava</pattern>
                  <shadedPattern>com.google.shaded.guava</shadedPattern>
                </relocation>
              </relocations>
            </configuration>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
```

### <a name="shade-package-using-sbt"></a>Пакет теней с использованием SBT
SBT также является инструментом сборки для Scala и Java. SBT не имеет тени плагин, как Maven-тени-плагин. Можно изменить `build.sbt` файл в теневые пакеты. 

Например, для `com.google.guava`затеняния можно `build.sbt` добавить нижеприведенную команду в файл:

```scala
assemblyShadeRules in assembly := Seq(
  ShadeRule.rename("com.google.guava" -> "com.google.shaded.guava.@1").inAll
)
```

После этого `sbt clean` вы `sbt assembly` можете побежать и построить затененный архив сосуды. 

## <a name="next-steps"></a>Дальнейшие действия

* [Используйте инструменты HDInsight IntelliJ](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox)

* [Создание приложения Scala Maven для Spark в IntelliJ](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-create-standalone-application)
