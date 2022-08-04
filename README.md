# Competition on credit history data

В данном репозитории представлено решение, занявшее 7 место в "Соревновании на данных кредитных историй" от ODS.ai и Альфа Банка.

Ссылка на соревнование: https://ods.ai/competitions/dl-fintech-bki

**Задача:**

В этом соревновании участникам предлагалось решить задачу кредитного скоринга клиентов Альфа-Банка, используя только данные кредитных историй.

**Данные:**
Датасет соревнования устроен таким образом, что кредиты для тренировочной выборки взяты за период в М месяцев, а кредиты для тестовой выборки взяты за последующие K месяцев.

Каждая запись кредитной истории содержит самую разнообразную информацию о прошлом кредите клиента, например, сумму, отношение клиента к кредиту, дату открытия и закрытия, информацию о просрочках по платежам и др. Все публикуемые данные тщательно анонимизированы.

Целевая переменная – бинарная величина, принимающая значения 0 и 1, где 1 соответствует дефолту клиента по кредиту.

Подробное описание файлов и полей датасета соревнования участники могут найти по ссылке: https://ods.ai/competitions/dl-fintech-bki/data, а также в файле "description".

**Решение:**
Для решения поставленной задачи был реализован стекинг из 9 моделей(6 нейросетевых моделей и 3 модели, основанных на градиентном бустинге).

***Нейросетевые модели:*** 
 - 4 модели, основанных на RNN, с разными вариациями архитектур,
 - 1 модель, основанная на архитектуре Transformers
 - 1 модель, основанная на сверточных нейронных сетях.

Подготовка данных для нейросетевых моделей представлена в файлах "dataset_preprocessing_utils_with_mask.py" и "data_generators_with_mask.py",
 
архитектуры моделей прописаны в файле "models.py", 

циклы обучения прописаны в "train_models.py",

вспомогательные функции в "utils.py" и "training_aux.py",

подготовка данных и обучение нейросетевых моделей - в файле "Neural_network_training.ipynb".

***Модели, основанные на бустинге:***
 - Catboost Classifier,
 - Catboost Ranker,
 - LGBMClassifier.

Генерация признаков для бустинга представлена в файле "Prepare_data_for_boosting.ipynb", обучение моделей - в "Boosting_training.ipynb".

***Мета-модели***

Все описанные выше модели были обучены на фолдах (нейросети на 5 фолдах, бустинги - на 10 фолдах). Для тренировочных данных были получены прогнозы методом out-of-bag. Для тестовых данных метапризнаки были получены как среднее арифметическое прогнозов отдельных моделей, обученных на фолдах. В экспериментах пробовала применять среднее геометрическое, но это не дало прироста итогового качества.

Далее были обучены две модели:
 - Логистическая регрессия на прогнозах описанных выше 9 моделей + попарных произведениях этих прогнозов.
 - Catboost Classifier. В качестве признаков выступали прогнозы 9 моделей, полиномиальные признаки степени 4 и различные статистики на них (подробное описание в файле "Ensemble_training.ipynb").

Данные модели обучались на 10 фолдах. 
Итоговый прогноз был получен как средневзвешенное прогнозов логистической регрессии и catboost (веса 15% и 85%, получены экспериментально).

Данное решение показало ROC_AUC_score  0,779138 на public leaderboard (9 место). На private leaderboard с ROC_AUC = 0,777522 решение поднялось на 7 место.

 
