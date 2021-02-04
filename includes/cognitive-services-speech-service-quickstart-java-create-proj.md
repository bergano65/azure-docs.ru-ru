---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 68e83ca0fc92247a31e840e76fc8019736b71dd9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214163"
---
1. Запустите Eclipse.

1. В средстве запуска Eclipse в поле **Рабочая область** введите имя нового каталога. Затем выберите **Запустить**.

   ![Снимок экрана средства запуска Eclipse](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. После этого отобразится главное окно интегрированной среды разработки Eclipse. Если отобразится **экран приветствия**, закройте его.

1. В строке меню Eclipse создайте новый проект, выбрав **Файл** > **Создать** > **Проект**.

1. Откроется диалоговое окно **Создание проекта** . Выберите **Проект Java**, а затем щелкните **Далее**.

   ![Снимок экрана диалогового окна нового проекта с выделенной строкой "Проект Java"](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. После этого запустится мастер **создания проектов Java**. В поле **Имя проекта** введите **quickstart** и в качестве среды выполнения выберите **JavaSE-1.8**. Нажмите кнопку **Готово**.

   ![Снимок экрана с изображением мастера создания проекта Java](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Если появится окно **Open Associated Perspective?** (Открыть связанную перспективу?), выберите **Open Perspective** (Открыть перспективу).

1. В **обозревателе пакетов** щелкните правой кнопкой мыши проект **quickstart**. В контекстном меню выберите **Настроить** > **Convert to Maven Project** (Преобразовать в проект Maven).

   ![Снимок экрана обозревателя пакетов](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Откроется окно **Create new POM** (Создать новый POM). В поле **Идентификатор группы** введите *com.microsoft.cognitiveservices.speech.samples*, а в поле **Идентификатор артефакта** — *quickstart*. Выберите **Готово**.

   ![Снимок экрана окна создания нового POM](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Откройте файл *pom.xml* и измените его.

   * В конце файла перед закрывающим тегом `</project>` создайте элемент `repositories` со ссылкой на репозиторий Maven для пакета SDK службы "Речь", как показано ниже.

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Кроме того, добавьте элемент `dependencies`, указав пакет SDK службы "Речь" версии 1.15.0 в виде зависимости.

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Сохраните изменения.
