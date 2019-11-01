---
title: Краткое руководство. Клиентская библиотека API Распознавания лиц для Python | Документация Майкрософт
description: Начало работы с клиентской библиотекой API Распознавания лиц для Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: b265ec68039f03c09d45c9605965de8ce0baba99
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935871"
---
# <a name="quickstart-face-client-library-for-python"></a>Краткое руководство. Клиентская библиотека API Распознавания лиц для Python

Начало работы с клиентской библиотекой API Распознавания лиц для Python. Выполните приведенные здесь действия, чтобы установить пакет и протестировать пример кода для выполнения базовых задач. Служба API Распознавания лиц предоставляет доступ к передовым алгоритмам обнаружения и распознавания лиц на изображениях.

Клиентская библиотека Распознавания лиц для Python позволяет выполнять такие задачи:

* Определение лиц на изображении
* Поиск похожих лиц
* создание и обучение на основе изображения группы людей;
* опознание лиц;
* Проверка лиц
* создание моментального снимка для переноса данных.

[Справочная документация](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Исходный код библиотеки](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Пакет (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Примеры](https://docs.microsoft.com/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Настройка

### <a name="create-a-face-azure-resource"></a>Создание ресурса API Распознавания лиц

Ресурсами Azure, на которые вы подписаны, будет представлено семейство служб Azure Cognitive Services. Создайте ресурс API Распознавания лиц с помощью [портала Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) или [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) на локальном компьютере. Также можно:

* Получить бесплатный [ключ пробной версии](https://azure.microsoft.com/try/cognitive-services/#decision) на 7 дней. После регистрации он будет доступен на [веб-сайте Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Просмотреть этот ресурс на [портале Azure](https://portal.azure.com/).

После получения ключа из своего ресурса или пробной подписки [задайте переменную среды](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) для ключа с именем `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Создание приложения Python

Создайте скрипт Python с произвольным именем, например&mdash;*quickstart-file.py*. Откройте его в редакторе или интегрированной среде разработки и импортируйте следующие библиотеки.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

Затем создайте переменные для конечной точки и ключа ресурса Azure. Может потребоваться изменить первую часть конечной точки (`westus`) в соответствии с вашей подпиской.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Если вы создали переменную среды после запуска приложения, для доступа к переменной следует закрыть и повторно открыть редактор, интегрированную среду разработки или оболочку, где эта переменная была запущена.

### <a name="install-the-client-library"></a>Установка клиентской библиотеки

Клиентскую библиотеку можно установить с помощью следующей команды:

```console
pip install --upgrade azure-cognitiveservices-vision-face
```

## <a name="object-model"></a>Объектная модель

Следующие классы и интерфейсы обрабатывают некоторые основные функции пакета SDK Распознавания лиц для Python.

|ИМЯ|ОПИСАНИЕ|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Этот класс реализует авторизацию для использования Распознавания лиц и требуется для реализации всех ее функций. Вы создаете его экземпляр с информацией о подписке и используете его для создания экземпляров других классов. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Этот класс обрабатывает основные задачи по обнаружению и распознаванию лиц. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Этот класс представляет все данные об отдельном лице, обнаруженном на изображении. Его можно использовать для получения подробных сведений о лице.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Этот класс управляет хранимыми в облаке конструкциями **FaceList**, которые включают систематизированную коллекцию лиц. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Этот класс управляет хранимыми в облаке конструкциями **Person**, в которых хранится коллекция лиц одного человека.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Этот класс управляет хранимыми в облаке конструкциями **PersonGroup**, в которых хранится систематизированная коллекция объектов **Person**. |
|[SnapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Этот класс управляет функцией создания моментального снимка. Его можно использовать для временного сохранения всех хранимых в облаке данных о лицах и переноса этих данных в новую подписку Azure. |

## <a name="code-examples"></a>Примеры кода

Эти фрагменты кода демонстрируют выполнение следующих действий с помощью клиентской библиотеки API Распознавания лиц для Python:

* [аутентификация клиента](#authenticate-the-client);
* [Определение лиц на изображении](#detect-faces-in-an-image)
* [поиск похожих лиц](#find-similar-faces);
* [создание и обучение на основе изображения группы людей](#create-and-train-a-person-group);
* [опознание лица](#identify-a-face);
* [Проверка лиц](#verify-faces)
* [создание моментального снимка для переноса данных](#take-a-snapshot-for-data-migration).

## <a name="authenticate-the-client"></a>Аутентификация клиента

> [!NOTE]
> В этом кратком руководстве предполагается, что вы уже [создали переменную среды](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) для ключа API Распознавания лиц с именем `FACE_SUBSCRIPTION_KEY`.

Создайте экземпляр клиента с конечной точкой и ключом. Создайте объект [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) с ключом и используйте его с конечной точкой, чтобы создать объект [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Определение лиц на изображении

Следующий код обнаруживает лицо на удаленно хранящемся изображении. Он выводит идентификатор обнаруженного лица в консоль, а также сохраняет его в памяти программы. Затем он обнаруживает лица на изображении с несколькими людьми и выводит их идентификаторы в консоль. Изменяя параметры в методе [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), можно получать разные сведения о каждом объекте [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Примеры кода для других сценариев обнаружения доступны на сайте [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).

### <a name="display-and-frame-faces"></a>Отображение лиц и их выделение рамкой

Следующий код выводит заданное изображение на экран и отмечает рамкой присутствующие на нем лица с помощью свойства DetectedFace.faceRectangle.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_frame)]

![Молодая женщина, вокруг лица которой нарисован красный прямоугольник](../images/face-rectangle-result.png)

## <a name="find-similar-faces"></a>Поиск похожих лиц

Следующий код принимает одно обнаруженное лицо и выполняет поиск совпадений в коллекции других лиц. При обнаружении совпадения в консоль выводятся координаты прямоугольника лица, с которым найдено совпадение. 

### <a name="find-matches"></a>Поиск совпадений

Сначала выполните код из предыдущего раздела ([Определение лиц на изображении](#detect-faces-in-an-image)), чтобы сохранить ссылку на отдельное лицо. Затем выполните следующий код, чтобы получить ссылки на несколько лиц на изображении группы людей.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Затем добавьте следующий блок кода, чтобы найти экземпляры первого лица в группе. Сведения о том, как изменить это поведение, см. в описании метода [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Вывод совпадений

Используйте следующий код, чтобы вывести сведения о совпадениях в консоль.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Создание и обучение на основе изображения группы людей

Следующий код создает объект **PersonGroup** с тремя объектами **Person**. Он соотносит каждый объект **Person** с коллекцией примеров изображений, а затем походит обучение, чтобы распознать каждое лицо. 

### <a name="create-persongroup"></a>Создание PersonGroup

Для выполнения этого сценария необходимо сохранить следующие изображения в корневом каталоге проекта https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Эта группа изображений содержит три коллекции изображений лиц, соотносящихся с лицами трех разных людей. Код определит три объекта **Person** и соотнесет их с файлами изображений, которые начинаются с префикса `woman`, `man` и `child`.

После настройки изображений определите метку в верхней части скрипта для создаваемого объекта **PersonGroup**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Добавьте следующий код в нижнюю часть скрипта. Этот код создает объект **PersongGroup** и три объекта **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Назначение лиц объектам Person

Следующий код сортирует изображения по префиксу, обнаруживает лица и назначает лица каждому объекту **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Обучение PersonGroup

После назначения лиц необходимо обучить объект **PersonGroup**, чтобы он мог опознавать визуальные черты, связанные с каждым из его объектов **Person**. Следующий код вызывает асинхронный метод **train**, запрашивает результат и выводит состояние в консоль.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Опознание лица

Следующий код принимает изображение с несколькими лицами и опознает каждое лицо на изображении. Он сравнивает каждое обнаруженное лицо с **PersonGroup**, которая является базой данных объектов **Person** с известными характеристиками лиц.

> [!IMPORTANT]
> Чтобы выполнить этот пример, сначала необходимо выполнить код из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Получение тестового изображения

Следующий код ищет в корневом каталоге проекта изображение _test-image-person-group.jpg_ и обнаруживает на нем лица. Это изображение есть в коллекции изображений, использовавшихся для создания и обучения **PersonGroup**. Оно доступно по адресу https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Идентификация лиц

Метод **identify** принимает массив обнаруженных лиц и сравнивает их с **PersonGroup**. Если он находит совпадение с **Person**, он сохраняет результат. Этот код выводит в консоль подробные сведения о совпадении.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="verify-faces"></a>Проверка лиц

Операция проверки принимает идентификатор лица, идентификатор другого лица или объекта **Person**, а затем определяет, связаны ли они с одним и тем же человеком.

Следующий код определяет лица на двух исходных изображениях, а затем сопоставляет их с лицом, определенным на целевом изображении.

### <a name="get-test-images"></a>Получение тестовых изображений

Следующий код блокирует объявление переменных, которые указывают на исходное и целевое изображения для операции проверки.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_baseurl)]

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_photos)]

### <a name="detect-faces-for-verification"></a>Определение лиц для проверки

Следующий код определяет лица на исходном и целевом изображениях и сохраняет их в переменных.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify_detect)]

### <a name="get-verification-results"></a>Получение результатов проверки

Следующий код сравнивает все исходные изображения с целевым изображением и выводит сообщение, которое указывает, связаны ли они с одним и тем же человеком.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_verify)]

## <a name="take-a-snapshot-for-data-migration"></a>создание моментального снимка для переноса данных.

Функция моментальных снимков позволяет перемещать сохраненные данные о лицах, например обученный объект **PersonGroup**, в другую подписку Azure Cognitive Services. Эту функцию можно использовать, если вы, например, создали объект **PersonGroup** в бесплатной пробной подписке и теперь хотите перенести его в платную подписку. Подробный обзор функции создания моментальных снимков см. в руководстве по [переносу данных о лицах](../Face-API-How-to-Topics/how-to-migrate-face-data.md).

В этом примере выполняется перенос объекта **PersonGroup**, созданного при выполнении инструкций из раздела [Создание и обучение на основе изображения группы людей](#create-and-train-a-person-group). Вы можете сначала выполнить эти инструкции или использовать собственные конструкции API Распознавания лиц.

### <a name="set-up-target-subscription"></a>Создание целевой подписки

Во-первых, у вас должна быть вторая подписка Azure с ресурсом API Распознавания лиц. Для этого выполните инструкции из раздела [Настройка](#setting-up). 

Затем создайте следующие переменные в верхней части скрипта. Вам также потребуется создать новые переменные среды для идентификатора подписки учетной записи Azure, ключ, конечную точку и идентификатор подписки для новой (целевой) учетной записи. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Проверка подлинности целевого клиента

Далее в скрипте сохраните свой текущий объект клиента в качестве исходного клиента, а затем проверьте подлинность нового объекта клиента для целевой подписки. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Использование моментального снимка

Остальные операции с моментальным снимком выполняются в асинхронной функции. 

1. Первым шагом является **создание** моментального снимка, который сохраняет данные о лицах из исходной подписки во временном облачном расположении. Этот метод возвращает идентификатор, который используется для запроса состояния операции.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Затем запрашивайте идентификатор, пока операция не завершится.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Этот код использует функцию `wait_for_operation`, которую следует определить отдельно.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Вернитесь к асинхронной функции. Используйте операцию **apply** для записи данных о лицах в целевую подписку. Этот метод также возвращает идентификатор.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. Снова запрашивайте идентификатор с помощью функции `wait_for_operation`, пока операция не завершится.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

После выполнения этих действий вы сможете получить доступ к конструкциям данных о лицах из новой (целевой) подписки.

## <a name="run-the-application"></a>Выполнение приложения

Запустите приложение, выполнив команду `python` для файла quickstart.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы хотите очистить и удалить подписку Cognitive Services, вы можете удалить ресурс или группу ресурсов. При этом удаляются все ресурсы, связанные с ней.

* [Портал](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [Интерфейс командной строки Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Если при изучении этого краткого руководства вы создали объект **PersonGroup** и хотите его удалить, выполните в скрипте следующий код:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Если при изучении этого краткого руководства вы перенесли данные с помощью функции создания моментальных снимков, вам также нужно удалить объект **PersonGroup**, сохраненный в целевой подписке.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководство вы узнали, как с помощью клиентской библиотеки API Распознавания лиц для Python выполнять базовые задачи. Далее ознакомьтесь со справочной документацией, чтобы узнать больше о библиотеке.

> [!div class="nextstepaction"]
> [Справочник по API Распознавания лиц (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [What is the Face API?](../overview.md) (Общие сведения об API Распознавания лиц)
* Исходный код для этого шаблона можно найти на портале [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).