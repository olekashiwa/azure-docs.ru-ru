---
title: Обучение моделей формирователя цепочки глубокого обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как выполнять сценарии обучения PyTorch в масштабе предприятия с помощью класса оценщика Машинное обучение Azure Chain.  Пример сценария классифицирует рукописные цифры для создания нейронной сети глубокого обучения с помощью библиотеки Python на основе NumPy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.reviewer: sdgilley
ms.date: 08/02/2019
ms.openlocfilehash: e2840a6295140e0dc22a032fa844c0488403c5a5
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536620"
---
# <a name="train-and-register-chainer-models-at-scale-with-azure-machine-learning"></a>Обучение и регистрация моделей цепочек в масштабе с помощью Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Из этой статьи вы узнаете, как выполнять сценарии обучения для программы " [цепочка](https://chainer.org/) " в масштабах предприятия с помощью класса [оценщика машинное обучение Azure Chain](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) . В примере обучающего скрипта в этой статье используется популярный [набор данных MNIST](http://yann.lecun.com/exdb/mnist/) для классификации рукописных цифр с помощью глубокой нейронной сети (DNN), созданной с помощью библиотеки Python, которая выполняется поверх [NumPy](https://www.numpy.org/).

Независимо от того, обходите ли вы модель цепочки глубокого обучения с нуля или используете существующую модель в облаке, вы можете использовать Машинное обучение Azure для масштабирования заданий обучения с открытым исходным кодом с помощью эластичных облачных ресурсов. Вы можете создавать, развертывать, выполнять версии и отслеживать модели производственного уровня с помощью Машинное обучение Azure. 

Дополнительные сведения о [глубоком обучении и машинном обучении](concept-deep-learning-vs-machine-learning.md).

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

## <a name="prerequisites"></a>Технические условия

Запустите этот код в любой из этих сред:

- Машинное обучение Azure вычислительных экземпляров — Загрузка или установка не требуется

    - Выполните инструкции из [учебника Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md) , чтобы создать выделенный сервер записной книжки, предварительно загруженный с помощью пакета SDK и примера репозитория.
    - В папке примеры глубокого обучения на сервере записной книжки найдите готовую записную книжку и файлы в окне "использование **> ML-Frameworks" > > развертывание > Train-i Parameter-Настройка-deploy-с помощью папки-chaining** .  Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

- Собственный сервер Jupyter Notebook

    - [Установите пакет SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).
    - [Создайте файл конфигурации рабочей области](how-to-configure-environment.md#workspace).
    - Скачайте пример файла скрипта [chainer_mnist. корректировки](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer).
     - Вы также можете найти завершенную [Jupyter Notebook версию](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb) этого руководства на странице примеров GitHub. Записная книжка включает в себя развернутые разделы, охватывающие интеллектуальные настройки, развертывание моделей и мини-приложения записных книжек.

## <a name="set-up-the-experiment"></a>Настройка эксперимента

В этом разделе выполняется настройка обучающего эксперимента путем загрузки требуемых пакетов Python, инициализации рабочей области, создания эксперимента и отправки обучающих данных и сценариев обучения.

### <a name="import-packages"></a>Импорт пакетов

Сначала импортируйте библиотеку azureml. Core Python и отобразите номер версии.

```
# Check core SDK version number
import azureml.core

print("SDK version:", azureml.core.VERSION)
```

### <a name="initialize-a-workspace"></a>Инициализация рабочей области

[Машинное обучение Azure Рабочая область](concept-workspace.md) — это ресурс верхнего уровня для службы. Он обеспечивает централизованное расположение для работы со всеми создаваемыми артефактами. В пакете SDK для Python можно получить доступ к артефактам рабочей области, создав объект [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) .

Создайте объект рабочей области, прочитав файл `config.json`, созданный в [разделе Предварительные требования](#prerequisites):

```Python
ws = Workspace.from_config()
```

### <a name="create-a-project-directory"></a>Создание каталога проекта
Создайте каталог, который будет содержать весь необходимый код с локального компьютера, к которому потребуется доступ на удаленном ресурсе. Сюда входит сценарий обучения и все дополнительные файлы, от которых зависит ваш сценарий обучения.

```
import os

project_folder = './chainer-mnist'
os.makedirs(project_folder, exist_ok=True)
```

### <a name="prepare-training-script"></a>Подготовка сценария обучения

В этом руководстве сценарий обучения **chainer_mnist. Корректировка** уже предоставлен. На практике вы можете использовать любой пользовательский сценарий обучения как есть и запускать его с помощью Azure ML без необходимости изменять код.

Чтобы использовать возможности отслеживания и метрик машинного обучения Azure, добавьте небольшой объем кода машинного обучения Azure в сценарий обучения.  В обучающем скрипте **chainer_mnist. Корректировка** показано, как зарегистрировать некоторые метрики в машинном обучении Azure с помощью объекта `Run` в скрипте.

В предоставленном скрипте обучения используются данные примера из функции Chain `datasets.mnist.get_mnist`.  Для собственных данных может потребоваться выполнить такие действия, как [Передача набора данных и скриптов](how-to-train-keras.md#data-upload) , чтобы сделать данные доступными во время обучения.

Скопируйте сценарий обучения **chainer_mnist. копировать** в каталог проекта.

```
import shutil

shutil.copy('chainer_mnist.py', project_folder)
```

### <a name="create-a-deep-learning-experiment"></a>Создание эксперимента глубокого обучения

Создайте эксперимент. В этом примере Создайте эксперимент с именем "Chain-mnist".

```
from azureml.core import Experiment

experiment_name = 'chainer-mnist'
experiment = Experiment(ws, name=experiment_name)
```


## <a name="create-or-get-a-compute-target"></a>Создание или получение целевого объекта вычислений

Для обучения модели требуется [целевой объект вычислений](concept-compute-target.md) . В этом примере используется управляемый центр МАШИНного обучения Azure (Амлкомпуте) для удаленного обучающего ресурса.

**Создание амлкомпуте занимает примерно 5 минут**. Если Амлкомпуте с таким именем уже находится в рабочей области, этот код пропускает процесс создания.  

```Python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6', 
                                                           max_nodes=4)

    # create the cluster
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# use get_status() to get a detailed status for the current cluster. 
print(compute_target.get_status().serialize())
```

Дополнительные сведения о целевых объектах вычислений см. в статье [что такое целевые показатели вычислений](concept-compute-target.md) .

## <a name="create-a-chainer-estimator"></a>Создание оценщика цепочки

Средство [оценки формирователя цепочки](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) предоставляет простой способ запуска заданий обучения формирователя цепочки на целевом объекте вычислений.

Оценщик цепочки реализуется с помощью универсального класса [`estimator`](https://docs.microsoft.com//python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) , который можно использовать для поддержки любой платформы. Дополнительные сведения о учебных моделях с помощью универсального средства оценки см. в статье [обучение моделей с машинное обучение Azure помощью средства оценки](how-to-train-ml-models.md) .

```Python
from azureml.train.dnn import Chainer

script_params = {
    '--epochs': 10,
    '--batchsize': 128,
    '--output_dir': './outputs'
}

estimator = Chainer(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    pip_packages=['numpy', 'pytest'],
                    entry_script='chainer_mnist.py',
                    use_gpu=True)
```

## <a name="submit-a-run"></a>Отправка запуска

[Объект Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) предоставляет интерфейс для журнала выполнения во время выполнения задания и после его завершения.

```Python
run = exp.submit(est)
run.wait_for_completion(show_output=True)
```

При выполнении выполнения он проходит следующие этапы.

- **Подготовка**. образ DOCKER создается в соответствии с оценщиком формирователя цепочки. Образ отправляется в реестр контейнеров рабочей области и кэшируется для последующего выполнения. Журналы также передаются в журнал выполнения и могут быть просмотрены для отслеживания хода выполнения.

- **Масштабирование**. Кластер пытается выполнить масштабирование, если кластеру Batch AI требуется больше узлов для выполнения выполнения, чем в настоящее время доступно.

- **Выполняется**: все скрипты в папке Script передаются в целевой объект вычислений, хранилища данных подключаются или копируются, а entry_script выполняется. Выходные данные из STDOUT и папки/логс передаются в журнал выполнения и могут использоваться для наблюдения за выполнением.

- **Пост-обработка**. папка/Outputs для выполнения копируется в журнал выполнения.

## <a name="save-and-register-the-model"></a>Сохранение и регистрация модели

После обучения модели ее можно сохранить и зарегистрировать в рабочей области. Регистрация модели позволяет хранить и отменять версии моделей в рабочей области, чтобы упростить [Управление моделями и их развертывание](concept-model-management-and-deployment.md).


После завершения обучения модели Зарегистрируйте модель в рабочей области с помощью следующего кода.  

```Python
model = run.register_model(model_name='chainer-dnn-mnist', model_path='outputs/model.npz')
```

> [!TIP]
> Только что зарегистрированная модель развертывается точно так же, как и любая другая Зарегистрированная модель в Машинное обучение Azure, независимо от того, какой механизм оценки использовался для обучения. Руководство по развертыванию содержит раздел, посвященный регистрации моделей, но можно сразу перейти к [созданию целевого объекта вычислений](how-to-deploy-and-where.md#choose-a-compute-target) для развертывания, так как у вас уже есть Зарегистрированная модель.

Также можно скачать локальную копию модели. Это может быть полезно для выполнения дополнительной проверки модели локально. В скрипте обучения `chainer_mnist.py`объект хранителя сохраняет модель в локальной папке (локальную для целевого объекта вычислений). Чтобы скачать копию из хранилища данных, можно использовать объект Run.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы обучили и зарегистрировали нейронную сеть для глубокого обучения, использующую цепочку Машинное обучение Azure. Чтобы узнать, как развернуть модель, перейдите к статье о [развертывании модели](how-to-deploy-and-where.md) .

* [Настройка гиперпараметров](how-to-tune-hyperparameters.md)

* [Отслеживание метрик выполнения во время обучения](how-to-track-experiments.md)

* [Ознакомьтесь с нашей справочной архитектурой по распределенному глубокому обучению в Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
