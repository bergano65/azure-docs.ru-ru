---
title: Краткое руководство по использованию клиентской библиотеки API "Распознавание лиц" (Go)
description: Клиентская библиотека API Распознавания лиц для Go позволяет обнаруживать лица, находить похожие лица (поиск лиц по изображению), идентифицировать лица (поиск с распознаванием лиц) и переносить данные о лицах.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 09/17/2020
ms.author: pafarley
ms.openlocfilehash: 382a04021053bef0b5d3378231e38453885b0ef2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323010"
---
В этом руководстве показано, как начать работу с клиентской библиотекой API Распознавания лиц для Go. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. В службе "Распознавание лиц" доступны передовые алгоритмы обнаружения и распознавания лиц на изображениях.

Используйте клиентскую библиотеку службы "Распознавание лиц" для следующих действий:

* [Определение лиц на изображении](#detect-faces-in-an-image)
* [поиск похожих лиц](#find-similar-faces);
* [создание и обучение на основе изображения группы людей](#create-and-train-a-person-group);
* [опознание лица](#identify-a-face);
* [создание моментального снимка для переноса данных](#take-a-snapshot-for-data-migration).

[Справочная документация](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [Скачивание пакета SDK](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Предварительные требования

* Последняя версия [Go](https://golang.org/dl/).
* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/cognitive-services/).
* Получив подписку Azure, перейдите к <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Создание ресурса Распознавания лиц"  target="_blank">созданию ресурса Распознавания лиц<span class="docon docon-navigate-external x-hidden-focus"></span></a> на портале Azure, чтобы получить ключ и конечную точку. После развертывания щелкните **Перейти к ресурсам**.
    * Для подключения приложения к API Распознавания лиц потребуется ключ и конечная точка из созданного ресурса. Ключ и конечная точка будут вставлены в приведенный ниже код в кратком руководстве.
    * Используйте бесплатную ценовую категорию (`F0`), чтобы опробовать службу, а затем выполните обновление до платного уровня для рабочей среды.
* После получения ключа и конечной точки [задайте переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и конечной точки с именами `FACE_SUBSCRIPTION_KEY` и `FACE_ENDPOINT` соответственно.

## <a name="setting-up"></a>Настройка

### <a name="create-a-go-project-directory"></a>Создание каталога проекта Go

В окне консоли (cmd, PowerShell, Терминал или Bash) создайте новую рабочую область для проекта Go с именем `my-app`, и перейдите в нее.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

Рабочая область будет содержать три папки:

* каталог **src** будет содержать исходный код и пакеты. Все пакеты, установленные с помощью команды `go get`, попадут в эту папку.
* каталог **pkg** будет содержать скомпилированные объекты пакета Go. Все эти файлы имеют расширение `.a`.
* каталог **bin** будет содержать двоичные исполняемые файлы, создаваемые при запуске `go install`.

> [!TIP]
> Дополнительные сведения о структуре рабочей области Go см. в [документации по языку Go](https://golang.org/doc/code.html#Workspaces). В этом руководством содержатся сведения о настройке `$GOPATH` и `$GOROOT`.

### <a name="install-the-client-library-for-go"></a>Установка клиентской библиотеки для Go

Далее, установите клиентскую библиотеку для Go:

```bash
go get -u github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

Или, если вы используете dep, выполните в репозитории такую команду:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Создание приложения Go.

Затем создайте файл в каталоге **src** с именем `sample-app.go`.

```bash
cd src
touch sample-app.go
```

Откройте `sample-app.go` в любой среде разработки или текстовом редакторе. Затем добавьте пакет и импортируйте следующие библиотеки.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

Далее вы будете добавлять код для выполнения различных операций службы "Распознавание лиц".

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы воплощают некоторые основные функции клиентской библиотеки службы "Распознавание лиц" для Go.

|Имя|Описание|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | Этот класс реализует авторизацию для использования Распознавания лиц и требуется для реализации всех ее функций. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов. |
|[Клиент](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|Этот класс обрабатывает основные задачи по обнаружению и распознаванию лиц. |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|Этот класс представляет все данные об отдельном лице, обнаруженном на изображении. Его можно использовать для получения подробных сведений о лице.|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|Этот класс управляет хранимыми в облаке конструкциями **FaceList**, которые включают систематизированную коллекцию лиц. |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| Этот класс управляет хранимыми в облаке конструкциями **Person**, в которых хранится коллекция лиц одного человека.|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| Этот класс управляет хранимыми в облаке конструкциями **PersonGroup**, в которых хранится систематизированная коллекция объектов **Person**. |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|Этот класс управляет функциональностью моментального снимка. Его можно использовать для временного сохранения всех хранимых в облаке данных о лицах и переноса этих данных в новую подписку Azure. |

## <a name="code-examples"></a>Примеры кода

В этих примерах кода показано, как выполнять основные задачи с использованием службы "Распознавание лиц" для Go:

* [аутентификация клиента](#authenticate-the-client);
* [Определение лиц на изображении](#detect-faces-in-an-image)
* [поиск похожих лиц](#find-similar-faces);
* [создание и обучение на основе изображения группы людей](#create-and-train-a-person-group);
* [опознание лица](#identify-a-face);
* [создание моментального снимка для переноса данных](#take-a-snapshot-for-data-migration).

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE] 
> В этом кратком руководстве предполагается, что вы уже [создали переменные среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа и конечной точки Распознавания лиц с именами `FACE_SUBSCRIPTION_KEY` и `FACE_ENDPOINT` соответственно.

Создайте функцию **main** и добавьте к ней следующий код, чтобы создать экземпляр клиента с конечной точкой и ключом. Создайте объект **[ServerognitiveServices Authorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** с ключом и используйте его с конечной точкой, чтобы создать **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** . Этот код также создает экземпляр объекта контекста, который необходим для создания клиентских объектов. Он также определяет удаленное расположение, в котором можно найти некоторые примеры изображений из этого краткого руководства.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>Определение лиц на изображении

Добавьте в метод **main** следующий код. Этот код определяет удаленный образец изображения и указывает, какие черты лица можно извлечь из изображения. Он также указывает, какую модель ИИ использовать для извлечения данных об обнаруженных лицах. Сведения о доступных вариантах см. в разделе [Указание модели распознавания](../../Face-API-How-to-Topics/specify-recognition-model.md). Наконец, метод **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** выполняет обнаружение лиц на изображении и сохраняет результаты в памяти программы.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>Отображение обнаруженных данных о лицах

Следующий блок кода принимает первый элемент массива объектов **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** и выводит его атрибуты в консоль. Если вы использовали изображение с несколькими лицами, то вместо этого следует выполнить итерацию по массиву.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>Поиск похожих лиц

Следующий код принимает одно обнаруженное лицо (источник) и выполняет поиск совпадений (поиск лиц по изображению) в коллекции других лиц (цель). При обнаружении совпадения в консоль выводится идентификатор лица, с которым найдено совпадение.

### <a name="detect-faces-for-comparison"></a>Обнаружение лиц для сравнения

Сначала сохраните ссылку на лицо, обнаруженное в ходе работы с разделом [Определение лиц в изображении](#detect-faces-in-an-image). Оно будет исходным.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

Затем введите следующий код, чтобы обнаружить набор лиц на другом изображении. Эти лица будут целевыми объектами.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>Поиск совпадений

В следующем коде используется метод **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** , чтобы найти все целевые лица, соответствующие исходному лицу.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>Вывод совпадений

Следующий код выводит в консоль сведения о совпадениях.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>Создание и обучение на основе изображения группы людей

Для выполнения этого сценария необходимо сохранить следующие изображения в корневом каталоге проекта https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Эта группа изображений содержит три коллекции одиночных изображений лиц, соотносящихся с лицами трех разных людей. Код определит три объекта **PersonGroup Person** и соотнесет их с файлами изображений, которые начинаются с `woman`, `man` и `child`.

### <a name="create-persongroup"></a>Создание PersonGroup

После скачивания изображений добавьте следующий код в нижнюю часть метода **main**. Этот код выполняет проверку подлинности объекта **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** , а затем использует его для определения нового объекта **PersonGroup**.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>Создание объектов Persons в PersonGroup

Следующий блок кода выполняет проверку подлинности **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** и использует его для определения трех новых объектов **PersonGroup Person**. Каждый из этих объектов представляет одного человека в наборе изображений.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>Назначение лиц объектам Person

Следующий код сортирует изображения по префиксу, обнаруживает лица и связывает лица с каждым соответствующим объектом **PersonGroup Person** на основе имени файла изображения.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>Обучение PersonGroup

После назначения лиц необходимо обучить объект **PersonGroup**, чтобы он мог опознавать визуальные черты, связанные с каждым из его объектов **Person**. Следующий код вызывает асинхронный метод **train**, запрашивает результат и выводит состояние в консоль.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>опознание лиц;

Операция идентификации (Identify) принимает изображение человека или нескольких людей и пытается опознать каждое лицо на этом изображении (поиск с распознаванием лиц). Он сравнивает каждое обнаруженное лицо с **PersonGroup**, которая является базой данных объектов **Person** с известными характеристиками лиц.

> [!IMPORTANT]
> Чтобы выполнить этот пример, сначала необходимо выполнить код из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Получение тестового изображения

Следующий код ищет в корневом каталоге проекта изображение _test-image-person-group.jpg_ и загружает его в память программы. Это изображение можно найти в том же репозитории, что и изображения, используемые в разделе [Создание и обучение группы людей](#create-and-train-a-person-group): https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>Обнаружение исходных лиц на тестовом изображении

Следующий блок кода выполняет обычное обнаружение лиц на тестовом изображении, чтобы получить все лица и сохранить их в массиве.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>Идентификация лиц

Метод **[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** принимает массив обнаруженных лиц и сравнивает их с заданным объектом **PersonGroup** (определенным и обученным в предыдущем разделе). Если метод находит совпадение с **Person** в группе, то он сохраняет результат.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

Затем этот код выводит в консоль подробные сведения о совпадении.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>Проверка лиц

Операция проверки принимает идентификатор лица, идентификатор другого лица или объекта **Person**, а затем определяет, связаны ли они с одним и тем же человеком.

Следующий код определяет лица на двух исходных изображениях, а затем сопоставляет каждое из них с лицом, обнаруженном на целевом изображении.

### <a name="get-test-images"></a>Получение тестовых изображений

Следующий код блокирует объявление переменных, которые указывают на целевое и исходное изображения для операции проверки.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>Определение лиц для проверки

Следующий код определяет лица на исходном и целевом изображениях и сохраняет их в переменных.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>Получение результатов проверки

Следующий код сравнивает все исходные изображения с целевым изображением и выводит сообщение, которое указывает, связаны ли они с одним и тем же человеком.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>создание моментального снимка для переноса данных.

Функция моментальных снимков позволяет перемещать сохраненные данные о лицах, например обученный объект **PersonGroup**, в другую подписку Azure Cognitive Services. Эту функцию можно использовать, если вы, например, создали объект **PersonGroup** в бесплатной подписке и теперь хотите перенести его в платную подписку. Подробный обзор функции создания моментальных снимков см. в руководстве по [переносу данных о лицах](../../Face-API-How-to-Topics/how-to-migrate-face-data.md).

В этом примере выполняется перенос объекта **PersonGroup**, созданного при выполнении инструкций из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group). Вы можете сначала выполнить эти инструкции или использовать собственные конструкции API Распознавания лиц.

### <a name="set-up-target-subscription"></a>Создание целевой подписки

Во-первых, у вас должна быть вторая подписка Azure с ресурсом Распознавания лиц. Для этого выполните инструкции из раздела [Настройка](#setting-up). 

Затем создайте следующие переменные в верхней части метода **main**. Вам также потребуется создать новые переменные среды для идентификатора подписки учетной записи Azure, ключ, конечную точку и идентификатор подписки для новой (целевой) учетной записи.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

Затем поместите значение идентификатора подписки в массив для следующих шагов.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>Проверка подлинности целевого клиента

Далее в скрипте сохраните свой исходный объект клиента в качестве исходного клиента, а затем проверьте подлинность нового объекта клиента для целевой подписки. 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>Создание моментального снимка

Следующий шаг предусматривает создание моментального снимка с помощью операции **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** , которая сохраняет данные о лицах из исходной подписки во временном облачном расположении. Этот метод возвращает идентификатор, который используется для запроса состояния операции.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

Затем запрашивайте идентификатор, пока операция не завершится.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>Применение снимка

Выполните операцию **[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** для записи только что отправленных данных о лицах в целевую подписку. Этот метод также возвращает идентификатор.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

Снова запрашивайте идентификатор, пока операция не завершится.

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

После выполнения этих действий вы сможете получать доступ к конструкциям данных о лицах из новой (целевой) подписки.

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение распознавания лиц из каталога приложения с помощью команды `go run <app-name>`.

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

При изучении этого краткого руководства вы создали объект **PersonGroup**. Если хотите его удалить, вызовите метод **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** . Если при изучении этого краткого руководства вы перенесли данные с помощью функции создания моментальных снимков, вам также нужно удалить объект **PersonGroup**, сохраненный в целевой подписке.

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве показано, как с помощью клиентской библиотеки API Распознавания лиц для Go выполнять базовые задачи по распознаванию лиц. Далее ознакомьтесь со справочной документацией, чтобы узнать больше о библиотеке.

> [!div class="nextstepaction"]
> [Справочник по API "Распознавание лиц" (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Что представляет собой служба "Распознавание лиц"?](../../overview.md)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go).
