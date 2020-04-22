---
title: Руководство по интеграции с Power Virtual Agents (QnA Maker)
titleSuffix: Azure Cognitive Services
description: В этом руководстве рассматривается улучшение качества базы знаний с помощью активного обучения. Просматривайте, подтверждайте, отклоняйте и добавляйте вопросы без удаления или изменения существующих.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402818"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Руководство по добавлению базы знаний в Power Virtual Agents
Создайте бот [Power Virtual Agents](https://powervirtualagents.microsoft.com/) и дополните его для предоставления ответов из базы знаний.

В этом руководстве описано следующее:

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание бота Power Virtual Agents
> * Создание раздела системного возврата
> * Добавление QnA Maker в качестве действия в раздел потока Power Automate
> * Создание решения Power Automate
> * Добавление потока Power Automate в решение
> * Публикация Power Virtual Agents
> * Тестирование Power Virtual Agents и получение ответа из базы знаний QnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Интеграция агента с базой знаний

Служба [Power Virtual Agents](https://powervirtualagents.microsoft.com/) позволяет специалистам создавать мощные боты с помощью интерактивного графического интерфейса без написания кода. Вам не потребуются разработчики или специалисты по обработке и анализу данных.

В Power Virtual Agents создается агент с набором разделов (предметных областей) для ответов на вопросы пользователей путем выполнения действий. Если ответ не найден, системный возврат может вернуть ответ.

Настройте в агенте отправку вопроса в базу знаний в составе действия для раздела или в пути *Системный возврат*. В обоих случаях используется действие для подключения к базе знаний и получения ответа.

## <a name="power-automate-connects-to-generateanswer-action"></a>Подключение Power Automate к действию `GenerateAnswer`

Чтобы подключить агент к базе знаний, для создания действия используйте Power Automate. Power Automate предоставляет поток процесса, который подключается к API `GenerateAnswer` QnA Maker.

После создания и сохранения потока он становится доступен в решении Power Automate. Используйте это решение в качестве действия в агенте.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Подключение агента к базе знаний

Ниже перечислены шаги по подключению агента в Power Virtual Agents к базе знаний в QnA Maker.

* На портале [QnA Maker](https://www.qnamaker.ai/) сделайте следующее:
    * Создайте и опубликуйте базу знаний.
    * Скопируйте сведения о базе знаний, включая идентификатор, ключ конечной точки среды выполнения и узел конечной точки среды выполнения.
* На портале [Power Virtual Agents](https://powerva.microsoft.com/) сделайте следующее:
    * Создайте раздел агента.
    * Вызовите действие (для потока Power Automate).
* На портале [Power Automate](https://us.flow.microsoft.com/) сделайте следующее:
    * Создайте поток с соединителем к API [GenerateAnswer в QnA Maker](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * Информация о базе знаний, опубликованной в QnA Maker:
            * Идентификатор базы знаний
            * Узел конечной точки ресурса QnA Maker
            * Ключ конечной точки ресурса QnA Maker
        * Входные данные — пользовательский запрос
        * Выходные данные — ответ базы знаний
    * Создайте решение и добавьте поток.
* Вернитесь к Power Virtual Agents и сделайте следующее:
    * Выберите выходные данные решения в качестве сообщения для раздела.

## <a name="create-and-publish-a-knowledge-base"></a>Создание и публикация базы знаний

1. Чтобы создать базу знаний, следуйте указаниям в [кратком руководстве](../Quickstarts/create-publish-knowledge-base.md). Не выполняйте последний раздел, посвященный созданию бота. Вместо этого вернитесь к этому руководству для создания бота в Power Virtual Agents.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с параметрами опубликованной базы знаний](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Введите параметры опубликованной базы знаний, которые указаны на странице **Параметры** на портале [QnA Maker](https://www.qnamaker.ai/). На шаге [Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) вы примените эти сведения, чтобы настроить подключение к `GenerateAnswer` в QnA Maker.

1. На странице **Параметры** портала QnA Maker найдите ключ конечной точки, узел конечной точки и идентификатор базы знаний.

## <a name="create-an-agent-in-power-virtual-agents"></a>Создание агента в Power Virtual Agents

1. [Выполните вход в Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Укажите рабочий или учебный адрес электронной почты.
1. Если это ваш первый бот, откроется **домашняя страница** агента. Если у вас уже есть боты, выберите нужный в правой части страницы и щелкните **+ New Bot** (+ Создать бота).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с домашней страницей Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Введите параметры опубликованной базы знаний, которые указаны на странице **Параметры** на портале [QnA Maker](https://www.qnamaker.ai/).

## <a name="topics-provided-in-the-bot"></a>Предоставленные в боте разделы

Агент использует коллекцию разделов для ответа на вопросы в нужной предметной области. В этом примере агент содержит вам много разделов, которые делятся на пользовательские и системные.

> [!div class="mx-imgBorder"]
> ![Снимок экрана со списком предоставленных в агенте разделов](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Создание раздела системного возврата

Агент может подключаться к базе знаний из любого раздела, но в этом примере используется только *системный раздел возврата*. Раздел "Возврат" используется в том случае, если агенту не удается найти ответ. Агент передает текст пользователя в API `GenerateAnswer` в QnA Maker, получает ответ из базы знаний и отображает его пользователю в виде сообщения.

1. На портале [Power Virtual Agents](https://powerva.microsoft.com/#/) в правом верхнем углу выберите **Параметры** (значок шестеренки). Теперь выберите **System Fallback** (Системный возврат).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с элементом меню "System Fallback" (Системный возврат) в Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Щелкните **+ Add** (+ Добавить), чтобы добавить раздел системного возврата.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана для добавления раздела возврата.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. После добавления раздела выберите команду **Go to Fallback topic** (Перейти к разделу возврата), чтобы создать раздел возврата на холсте разработки.

    > [!TIP]
    > Если вам нужно вернуться к разделу возврата, он доступен в области **Разделы** в составе группы **Системные**.

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Использование холста разработки для добавления действия

Используйте холст разработки Power Virtual Agents, чтобы подключить раздел возврата к базе знаний. Этот раздел начинается с нераспознанного пользовательского текста. Добавьте действие, которое передает этот текст в QnA Maker и отображает ответ в виде сообщения. Последний этап отображения ответа обрабатывается в видео [отдельного шага](#add-your-solutions-flow-to-power-virtual-agents), как описано далее в этом руководстве.

В этом разделе создается поток беседы раздела возврата.

1. В новом действии возврата могут уже присутствовать элементы потока беседы. Удалите элемент **Escalate** (Передать), выбрав меню **Параметры**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с разделом возврата в Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Выберите соединитель **+** , который идет от блока **Message** (Сообщение), а затем выберите команду **Call an action** (Вызов действия).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с командой вызова действия](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Выберите элемент **Создать поток**. После этого откроется портал Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с командой создания потока](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Создание потока Power Automate для подключения к базе знаний

Следующая процедура позволяет создать поток Power Automate, который:
* принимает входящий пользовательский текст и отправляет его в QnA Maker;
* сохраняет лучший ответ QnA Maker в переменную и отправляет эту переменную с лучшим ответом в агент.

1. В **Power Automate** запускается **Шаблон потока**. В элементе потока **Power Virtual Agents** выберите пункт **Изменить**, чтобы настроить входную переменную, поступающую от агента в базу знаний. Входная текстовая переменная — это отправленный пользователем текстовый вопрос от агента.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с действием Power Automate для настройки входной переменной в виде текстовой строки](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Добавьте входной текст и присвойте переменной имя `InputText` и описание `IncomingUserQuestion`. Такое именование помогает отличать входной текст от выходного текста, который вы создадите позже.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с действием Power Automate для настройки имени и описания входной переменной](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Выберите соединитель **+** , который идет от блока **Power Virtual Agents**, чтобы вставить новый шаг в поток перед группой **Возвратить значения в Power Virtual Agents**. Затем выберите команду **Добавить действие**.

1. Выполните поиск по `Qna`, чтобы найти действия **QnA Maker**, а затем выберите **Generate answer** (Создать ответ).

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с командой создания ответа](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    Обязательные параметры подключения к QnA Maker отображаются в параметрах действия и параметрах вопроса, полученных от агента.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с обязательными параметрами подключения](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Настройте для этого действия идентификатор базы знаний, узел и ключ конечной точки. Они находятся на странице **Параметры** базы знаний на портале QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с параметрами опубликованной базы знаний](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Чтобы настроить **Вопрос**, щелкните текстовое поле и выберите `InputText` в списке.

1. Чтобы вставить новый шаг в поток, выберите соединитель **+** в поле действия **Generate answer** (Создать ответ). Затем выберите команду **Добавить действие**.

1. Чтобы добавить переменную для записи текста ответа, возвращаемого из интерфейса `GenerateAnswer`, найдите и выберите действие `Initialize variable`.

    Присвойте переменной имя `OutgoingQnAAnswer` и выберите тип **String**. Не задавайте **Значение**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с инициализацией выходной переменной](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Чтобы вставить новый шаг в поток, выберите соединитель **+** в поле действия **Инициализировать переменную**. Затем выберите команду **Добавить действие**.

1. Чтобы назначить в переменную весь ответ JSON из базы знаний, найдите и выберите действие `Apply to each`. Выберите `GenerateAnswer` `answers`.

1. Чтобы вернуть только лучший ответ, в том же поле **Применить к каждому**, выберите команду **Добавить действие**. Найдите и выберите элемент **Задание переменной**.

    В поле **Set variable** (Задание переменной) выберите текстовое поле **Имя**, а затем выберите **OutgoingQnAAnswer** в списке.

    Выберите текстовое поле **Value** (Значение), а затем в списке выберите значение **Answers Answer**.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана для процесса настройки имени и значения переменной ](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Чтобы вернуть переменную (и ее значение), выберите элемент потока **Возвратить значения в Power Virtual Agents**. Затем выберите **Edit** > **Add an output** (Изменить > Добавить выходные данные). Выберите тип выходных данных **Text** (Текст), а затем в поле **Title** (Заголовок) введите значение `FinalAnswer`. Выберите текстовое поле **Значение**, а затем выберите переменную `OutgoingQnAAnswer`.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана для настройки возвращаемого значения](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Выберите **Сохранить**, чтобы сохранить поток.

## <a name="create-a-solution-and-add-the-flow"></a>Создание решения и добавление потока

Чтобы агент мог находить поток и подключаться к нему, следует добавить этот поток в решение Power Automate.

1. На портале Power Automate, выберите **Решения** в области навигации слева.

1. Выберите команду **Создать решение**.

1. Введите отображаемое имя. Список решений содержит все решения в вашей организации или учебном заведении. Выберите соглашение об именовании, чтобы отфильтровать только нужные решения. Например, можно добавить адрес электронной почты в качестве префикса к имени решения: `jondoe-power-virtual-agent-qnamaker-fallback`.

1. Выберите издателя в списке вариантов.

1. Примите значения по умолчанию для имени и версии.

1. Нажмите кнопку **Создать**, чтобы завершить процесс.

## <a name="add-your-flow-to-the-solution"></a>Добавление потока в решение

1. В списке решений выберите только что созданное решение. Оно должно находиться в верхней части списка. Если это не так, выполните поиск по адресу электронной почты, который является частью имени решения.

1. В решении выберите команду **+ Добавить существующий**, а затем в списке выберите **Поток**.

1. Найдите нужный поток и нажмите кнопку **Добавить**, чтобы завершить процесс. Если потоков много, просмотрите столбец **Изменен**, чтобы найти самый последний поток.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Добавление потока решения в Power Virtual Agents

1. Вернитесь на вкладку браузера, где открыта странице агента в Power Virtual Agents. Созданный холст по-прежнему должен быть открыт.

1. Чтобы вставить новый шаг в поток, в поле действия **Message** (Сообщение) выберите соединитель **+** . Затем выберите команду **Call an action** (Вызов действия).

1. В новом действии выберите входное значение **UnrecognizedTriggerPhrase**. При этом текст будет передан из агента в поток.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с параметром Power Virtual Agents для выбора нераспознанной фразы активации](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Чтобы вставить новый шаг в поток, в поле **Action** (Действие) выберите соединитель **+** . Затем щелкните **Show a message** (Показать сообщение).

1. Введите текст сообщения: `Your answer is:`. Выберите `FinalAnswer` в качестве переменной контекста, используя функцию на панели инструментов.

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана для операции ввода текста сообщения в Power Virtual Agents](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. На контекстной панели инструментов нажмите кнопку **Сохранить**, чтобы сохранить сведения с холста разработки для раздела.

Так выглядит окончательное состояние агента на холсте.

> [!div class="mx-imgBorder"]
> ![Снимок экрана с окончательной версией агента на холсте](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Тестирование агента

1. На панели тестирования установите переключатель **Track between topics** (Отслеживать по разделам). Это позволяет отслеживать ход выполнения по нескольким разделам или в пределах одного раздела.

1. Протестируйте агент, введя пользовательский текст в следующем порядке. На холсте разработки успешно выполненные шаги будут отмечены зеленой галочкой.

    |Порядок вопросов|Тестовые вопросы|Назначение|
    |--|--|--|
    |1|Привет|Начало беседы|
    |2|Время хранения|Пример раздела. Он настраивается без дополнительных усилий с вашей стороны.|
    |3|Да|Ответ на `Did that answer your question?`|
    |4|Высокая|Ответ на `Please rate your experience.`|
    |5|Да|Ответ на `Can I help with anything else?`|
    |6|Что представляет собой база знаний|Этот вопрос активирует действие возврата, которое отправляет текст в базу знаний для ответа. После этого отображается ответ. |

> [!div class="mx-imgBorder"]
> ![Снимок экрана с окончательной версией агента на холсте](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Публикация бота

Чтобы агент стал доступным для всех участников учебного заведения или организации, его необходимо опубликовать.

1. Щелкните **Publish** (Опубликовать) в меню навигации слева. На открывшейся странице щелкните **Publish** (Опубликовать).

1. Проверьте работу бота на демонстрационном веб-сайте (ссылку можно найти в разделе **Publish** (Публикация)).

    Откроется новая веб-страница с ботом. Задайте боту тот же тестовый вопрос: `What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Снимок экрана с окончательной версией агента на холсте](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Публикация своего бота

Чтобы предоставить общий доступ к демонстрационному веб-сайту, настройте его как канал.

1. В меню навигации слева последовательно выберите **Manage** > **Channels** (Управление > Каналы).

1. В списке каналов выберите **Демонстрационный веб-сайт**.

1. Скопируйте ссылку и нажмите кнопку **Сохранить**. Вставьте ссылку на свой демонстрационный веб-сайт в сообщение электронной почты для пользователей в учебном заведении или организации.

## <a name="clean-up-resources"></a>Очистка ресурсов

Завершив работу с базой знаний, удалите ресурсы QnA Maker на портале Azure.

## <a name="next-step"></a>Следующий шаг

[Анализ базы знаний](../How-To/get-analytics-knowledge-base.md)

См. также:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Соединитель QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) и [настройки соединителя](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)