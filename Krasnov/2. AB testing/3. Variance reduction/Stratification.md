Основная цель стратификации - снижение дисперсии выборок, для повышения чувствительности или снижения размера требуемой выборки

Стратификация - есть вес страты умноженный на метрику. 

1. Считаем стратифицированную дисперсию (дисперсия страты * на её вес)
2. Считаем стратифицированное среднее (среднее страты * на её вес)
3. Считаем стандартное отклонение
4. Считаем t статистику  
5. Находим p_value

![[Pasted image 20230605142639.png]]

## Make stratification
**Ковариата** - метрика, которая коррелирует с целевой метрикой, может быть измерена до эксперимента (строго говоря, нет) и не зависит от других экспериментов. В нашем случае факт регистрации в программе лояльности до эксперимента будет ковариатой.

```Python
import pandas as pd
import numpy as np

def get_strats(df: pd.DataFrame):
    """Возвращает страты объектов.
    :return (list | np.array | pd.Series): список страт объектов размера len(df).
    """
    
    result = df.copy()
    
    for step in range(0,10):
        low = df['x2'].quantile(0.1*step)
        high = df['x2'].quantile(0.1*(step+1))
        result.loc[(result['x2'] >= low) & (result['x2'] <= high),'strata'] = (step+1)
    
    return result['strata']


def calculate_strat_var(df):
    """Вычисляет стратифицированную дисперсию популяции."""
    strat_vars = df.groupby('strat')['y'].var()
    weights = df['strat'].value_counts(normalize=True)
    stratified_var = (strat_vars * weights).sum()
    return stratified_var


if __name__ == "__main__":
    bound = 50000
    df = pd.read_csv('stratification_task_data_public.csv')
    strats = get_strats(df.drop('y', axis=1))
    assert len(strats) == len(df), "Неверный размер списка страт"
    min_part = pd.Series(strats).value_counts(normalize=True).min()

    assert min_part >= 0.05, "Минимальная доля одной из страт меньше 5%"
    df['strat'] = strats
    strat_var = calculate_strat_var(df)
    err_msg = f"Дисперсия равна {strat_var:0.1f}, её нужно снизить до {bound}"
    assert strat_var <= bound, err_msg
    print(f'Отлично! Дисперсия равна {strat_var:0.1f}, меньше порога {bound}')
    None
```

Если не придумывать осмысленное разбиение на страты, а стараться максимально снизить дисперсию, то можно использовать следующий подход:

1. Построить модель, которая по признакам прогнозирует значение метрики.

2. По прогнозным значениям разбить объекты на страты. С маленькими значениями прогноза в одну страту, с большими — в другую.

При качественной реализации такой подход даёт большее снижение дисперсии стратифицированного среднего. Но его сложнее реализовать и интерпретировать. Подробно рассматривать способ построения предсказания не будем, так как это за рамками нашего курса.

С помощью такого подхода в нашей задаче можно снизить дисперсию примерно до 42000.

``` Python
from lightgbm import LGBMRegressor

df_train = df.iloc[:len(df) // 2].copy()
df_test = df.iloc[len(df) // 2:].copy()

model = LGBMRegressor(num_leaves=3)
feature_names = [f'x{i}' for i in range(1, 11)]
model.fit(df_train[feature_names].values, df_train['y'].values)
predict_test = model.predict(df_test[feature_names].values)

n_strat = 10
quantiles = np.quantile(predict_test, np.linspace(0, 1 - 1 / n_strat, n_strat))
df_test['strat'] = [np.sum(predict >= quantiles) for predict in predict_test]
print(int(calculate_strat_var(df_test)))
```

## Split stratified

Если выборки уже разбиты на страты, то нужно сформировать контрольную и экспериментальные группы. С этим поможет код

``` Python
import numpy as np
import pandas as pd


def split_stratified(strats: np.array) -> np.array:
    """Распределяет объекты по группам (контрольная и экспериментальная).
    
    :param strats: массив с разбиением на страты.
    :return: массив из 0 и 1, 0 - контрольная группа, 1 - экспериментальная.
    """
    a = set()
    result = []
    for element in strats:
        if element in a:
            result.append(1)
            a.remove(element)
        else:
            result.append(0)
            a.add(element)
            
    return np.array(result)


def check_split(df: pd.DataFrame):
    """Проверяет корректность разбиения на страты.
    
    :param df: датафрейм с двумя столбцами: ['strat', 'group'].
    """
    df_agg = (
        df
        .groupby(['strat', 'group'])[['group']].count()
        .rename(columns={'group': 'count'})
        .reset_index()
    )
    df_pivot = df_agg.pivot(index='strat', values='count', columns='group')
    max_delta = (df_pivot[0] - df_pivot[1]).abs().max()
    assert max_delta <= 1, "Размеры страт между группами отличаются более чем на 1."


if __name__ == "__main__":
    df = pd.DataFrame({'strat': [1, 2, 2, 2, 1, 1, 1, 3, 3]})
    df['group'] = split_stratified(df['strat'].values)
    check_split(df)
    print('simple test passed')
```

Или 

``` Python
def split_stratified(strats: np.array) -> np.array:
    """Распределяет объекты по группам (контрольная и экспериментальная).
    
    :param strats: массив с разбиением на страты.
    :return: массив из 0 и 1, 0 - контрольная группа, 1 - экспериментальная.
    """
    indexes = np.arange(len(strats))
    a_indexes = []
    b_indexes = []
    for strat in np.unique(strats):
        indexes_strat = indexes[strats == strat]
        size_strat = len(indexes_strat)
        # перемешиваем индексы
        np.random.shuffle(indexes_strat)
        # разделяем индексы по группам
        a_indexes += list(indexes_strat[:size_strat // 2])
        b_indexes += list(indexes_strat[size_strat // 2:])
    groups = np.zeros(len(strats), dtype=int)
    groups[b_indexes] = 1
    return groups
```


## Full pipeline AB testing with stratification 

```python
class ExperimentsService:

    @staticmethod
    def _calc_strat_mean(df: pd.DataFrame, weights: pd.Series) -> float:
        """Считает стратифицированное среднее.

        :param df: датафрейм с целевой метрикой и данными для стратификации
        :param weights: маппинг {название страты: вес страты в популяции}
        """
        strat_mean = df.groupby('strat')['metric'].mean()
        return (strat_mean * weights).sum()

    @staticmethod
    def _calc_strat_var(df: pd.DataFrame, weights: pd.Series) -> float:
        """Считает стратифицированную дисперсию.

        :param df: датафрейм с целевой метрикой и данными для стратификации
        :param weights: маппинг {название страты: вес страты в популяции}
        """
        strat_var = df.groupby('strat')['metric'].var()
        return (strat_var * weights).sum()

    def _ttest_strat(self, metrics_strat_a_group, metrics_strat_b_group):
        """Применяет постстратификацию, возвращает pvalue.

        Веса страт считаем по данным обеих групп.
        Предполагаем, что эксперимент проводится на всей популяции.
        Веса страт нужно считать по данным всей популяции.

        :param metrics_strat_a_group (np.ndarray): значения метрик и страт группы A.
            shape = (n, 2), первый столбец - метрики, второй столбец - страты.
        :param metrics_strat_b_group (np.ndarray): значения метрик и страт группы B.
            shape = (n, 2), первый столбец - метрики, второй столбец - страты.
        :param design (Design): объект с данными, описывающий параметры эксперимента
        :return (float): значение p-value
        """
        weights = (
            pd.Series(np.hstack((metrics_strat_a_group[:, 1], metrics_strat_b_group[:, 1],)))
            .value_counts(normalize=True)
        )
        a = pd.DataFrame(metrics_strat_a_group, columns=['metric', 'strat'])
        b = pd.DataFrame(metrics_strat_b_group, columns=['metric', 'strat'])
        a_strat_mean = self._calc_strat_mean(a, weights)
        b_strat_mean = self._calc_strat_mean(b, weights)
        a_strat_var = self._calc_strat_var(a, weights)
        b_strat_var = self._calc_strat_var(b, weights)
        delta = b_strat_mean - a_strat_mean
        std = (a_strat_var / len(a) + b_strat_var / len(b)) ** 0.5
        t = delta / std
        pvalue = 2 * (1 - stats.norm.cdf(np.abs(t)))
        return pvalue

    def get_pvalue(self, metrics_strat_a_group, metrics_strat_b_group, design):
        """Применяет статтест, возвращает pvalue.

        :param metrics_strat_a_group (np.ndarray): значения метрик и страт группы A.
            shape = (n, 2), первый столбец - метрики, второй столбец - страты.
        :param metrics_strat_b_group (np.ndarray): значения метрик и страт группы B.
            shape = (n, 2), первый столбец - метрики, второй столбец - страты.
        :param design (Design): объект с данными, описывающий параметры эксперимента
        :return (float): значение p-value
        """
        if design.statistical_test == 'ttest':
            if design.stratification == 'off':
                _, pvalue = stats.ttest_ind(metrics_strat_a_group[:, 0], metrics_strat_b_group[:, 0])
                return pvalue
            elif design.stratification == 'on':
                return self._ttest_strat(metrics_strat_a_group, metrics_strat_b_group)
            else:
                raise ValueError('Неверный design.stratification')
        else:
            raise ValueError('Неверный design.statistical_test')
```



https://habr.com/ru/companies/X5Tech/articles/596279/
[[AB]] 

