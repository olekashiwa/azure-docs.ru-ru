---
title: Данные в Машинное обучение Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как Машинное обучение Azure взаимодействует с данными и как они используются в экспериментах машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: 4149e90e07bbcd03a0df41060b42b8902b89e774
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541885"
---
# <a name="data-access-in-azure-machine-learning"></a>Доступ к данным в Машинное обучение Azure

В этой статье вы узнаете о Машинное обучение Azure решений по управлению данными и интеграции для задач машинного обучения. В этой статье предполагается, что вы уже создали [учетную запись хранения Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) и [службу хранилища Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction).

Когда вы будете готовы к использованию данных в хранилище, рекомендуем вам

1. Создайте хранилище данных Машинное обучение Azure.
2. На основе этого хранилища данных создайте Машинное обучение Azure DataSet. 
3. Используйте этот набор данных в эксперименте машинного обучения, выполнив 
    1. Подключение к цели вычислений эксперимента для обучения модели

        **OR** 

    1. Его использование непосредственно в Машинное обучение Azure решениях, таких как Автоматизированные запуски экспериментов машинного обучения, конвейеры машинного обучения и [конструктор машинное обучение Azure](concept-designer.md).
4. Создание мониторов набора данных для выходного набора данных модели для обнаружения смещения данных. 
5. Если будет обнаружено смещение данных, обновите входной набор данных и соответствующим образом выполните обучение модели.

На следующей схеме показана визуальная демонстрация этого рекомендуемого рабочего процесса доступа к данным.

![Схема концепции данных](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>Доступ к данным в хранилище

Для доступа к данным в учетной записи хранения Машинное обучение Azure предлагает хранилища данных и наборы данных. Хранилища данных отвечают на вопрос: как безопасно подключаться к моим данным в хранилище Azure? Хранилища данных обеспечивают уровень абстракции для службы хранилища. Это помогает в обеспечении безопасности и простоты доступа к хранилищу, так как сведения о соединении хранятся в хранилище данных и не предоставляются в скриптах. 

Наборы данных отвечают на вопрос: как получить определенные файлы с данными в моем хранилище? Наборы данных указывают на конкретный файл или файлы в базовом хранилище, которые вы хотите использовать для эксперимента машинного обучения. Вместе хранилища данных и наборы данных предлагают защищенный, масштабируемый и воспроизводимый рабочий процесс доставки для задач машинного обучения.

### <a name="datastores"></a>Хранилища данных

Хранилище данных Машинное обучение Azure — это абстракция хранилища для служб хранилища Azure. [Зарегистрируйте и создайте хранилище](how-to-access-data.md) данных, чтобы легко подключиться к учетной записи хранения Azure и получить доступ к данным в базовых службах хранилища Azure.

Поддерживаемые службы хранилища Azure, которые можно зарегистрировать как хранилища данных:
+ Контейнер больших двоичных объектов Azure
+ Общая папка Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ База данных SQL Azure
+ База данных Azure для PostgreSQL
+ Файловая система Databricks
+ База данных Azure для MySQL

### <a name="datasets"></a>Наборы данных

[Создайте машинное обучение Azure набор данных](how-to-create-register-datasets.md) для взаимодействия с данными в хранилищах данных и упаковки данных в объект, который можно использовать для задач машинного обучения. Зарегистрируйте набор данных в рабочей области, чтобы поделиться им и повторно использовать его в разных экспериментах без сложностей приема данных.

Наборы данных можно создавать из локальных файлов, общедоступных URL-адресов, [открытых наборов данных Azure](#open)или конкретных файлов в хранилищах данных. Чтобы создать набор данных из таблицы данных Pandas в памяти, запишите данные в локальный файл, например в CSV-файл, и создайте набор данных из этого файла. Наборы данных не копируются, но представляют собой ссылки, указывающие на данные в службе хранилища, поэтому дополнительные затраты на хранение не взимается. 

На следующей схеме показано, что если у вас нет службы хранилища Azure, можно создать набор данных непосредственно из локальных файлов, общедоступных URL-адресов или открытого набора данных Azure. При этом набор данных подключается к хранилищу, по умолчанию созданному автоматически с [рабочей областью машинное обучение Azure](concept-workspace.md)эксперимента.

![Схема концепции данных](./media/concept-data/dataset-workflow.svg)

Дополнительные возможности наборов данных можно найти в следующей документации:

+ [Версия и отслеживание](how-to-version-track-datasets.md) журнала преобразований данных.
+ [Отслеживайте набор данных](how-to-monitor-datasets.md) , чтобы упростить обнаружение отклонения данных.
+  Документацию по двум типам наборов данных см. в следующих статьях:
    + [Табулардатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) представляет данные в табличном формате путем синтаксического анализа указанного файла или списка файлов. Это позволяет материализовать данные в кадр данных Pandas или Spark для дальнейшей обработки и очистки. Полный список файлов, которые можно создать Табулардатасетс из, см. в разделе [класс табулардатасетфактори](https://aka.ms/tabulardataset-api-reference).

    + [Филедатасет](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) ссылается на один или несколько файлов в хранилищах данных или общедоступных URL-адресах. С помощью этого метода можно скачать или подключить файлы, которые вы выбрали, в целевой объект вычислений как объект Филедатасет.

## <a name="work-with-your-data"></a>Работа с данными

С помощью наборов данных можно выполнять ряд задач машинного обучения благодаря простой интеграции с Машинное обучение Azureными функциями. 

+ [Обучение моделей машинного обучения](how-to-train-with-datasets.md).
+ Использование наборов данных в 
     + [автоматические эксперименты ML](how-to-create-portal-experiments.md)
     + [конструктор](tutorial-designer-automobile-price-train-score.md#import-data) 
+ Доступ к наборам данных для оценки с помощью вывода пакетов в [конвейерах машинного обучения](how-to-create-your-first-pipeline.md).
+ Создайте [проект меток данных](#label).
+ Настройте монитор набора данных для обнаружения [несмещенных данных](#drift) .

<a name="open"></a>

## <a name="azure-open-datasets"></a>Открытые наборы данных Azure

[Открытые наборы данных Azure](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets) — это проверенные общедоступные наборы данных, которые можно использовать для добавления функций конкретных сценариев в решения машинного обучения для создания более точных моделей. Открытые наборы данных находятся в облаке на Microsoft Azure и интегрированы в Машинное обучение Azure. Кроме того, вы можете получать доступ к наборам данных с помощью интерфейсов API, а также использовать их в других продуктах, например в Power BI и Фабрике данных Azure.

В число открытых наборов данных Azure входят данные общего домена для погоды, переписи, праздников, общедоступная безопасность и расположение, которое поможет вам обучать модели машинного обучения и расширять прогнозные решения. Вы также можете делиться своими общедоступными наборами данных в Azure.

<a name="label"></a>

## <a name="data-labeling"></a>Добавление меток к данным

Добавление меток к большим объемам данных часто усложняет проекты машинного обучения. Для таких компонентов концепции компьютера, как классификация изображений или обнаружение объектов, обычно требуются тысячи изображений и соответствующие метки.

Решение "Машинное обучение Azure" служит централизованным расположением для создания, администрирования и мониторинга проектов маркировки. Проекты маркировки помогают упорядочивать данные, метки и координировать работу команды, то есть более эффективно управлять задачами присвоения меток. Сейчас поддерживаются такие задачи: классификация изображений (с несколькими метками или классами) и идентификация объектов с использованием ограничивающих прямоугольников.

+ Создайте [проект меток данных](how-to-create-labeling-projects.md)и выводите набор данных для использования в экспериментах машинного обучения.

<a name="drift"></a>

## <a name="data-drift"></a>Смещение данных

В контексте машинного обучения смещение данных — это изменение входных данных модели, которое приводит к снижению производительности модели. Это одна из основных причин, с которой точность снижается с течением времени, поэтому отслеживание смещения данных помогает выявить проблемы с производительностью модели.
Дополнительные сведения о способах обнаружения и оповещения о смещении данных для новых данных в наборе данных см. в статье [Создание монитора набора данных](how-to-monitor-datasets.md) .

## <a name="next-steps"></a>Дальнейшие действия 

+ Создайте набор данных в Машинное обучение Azure Studio или с помощью пакета SDK для Python, [выполнив следующие действия.](how-to-create-register-datasets.md)
+ Испытайте примеры обучения наборов данных с помощью [примеров записных книжек](https://aka.ms/dataset-tutorial).
+ Примеры расхождения данных см. в этом [руководстве по смещению данных](https://aka.ms/datadrift-notebook).
