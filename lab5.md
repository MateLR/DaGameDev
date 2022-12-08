# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #5 - Интеграция экономической системы в проект Unity и обучение ML-Agent
- Утенков Руслан Мехманович
- РИ210940
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;
dfdf
Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Измените параметры файла. yaml-агента и определить какие параметры и как влияют на обучение модели.
- Открываем наш проект, разбираемся в коде, устанавливаем необходимые параметры

![image](https://user-images.githubusercontent.com/77449049/206520072-6826ca60-a5ad-4ddc-8461-409832982baa.png)
- Добавляем в проект конфигуратор обучающего тренажёра Economic.yaml

```
behaviors:
  Economic:
    trainer_type: ppo
    hyperparameters:
      batch_size: 1024
      buffer_size: 10240
      learning_rate: 3.0e-4
      learning_rate_schedule: linear
      beta: 1.0e-2
      epsilon: 0.2
      lambd: 0.95
      num_epoch: 3      
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    checkpoint_interval: 500000
    max_steps: 750000
    time_horizon: 64
    summary_freq: 5000
    self_play:
      save_steps: 20000
      team_change: 100000
      swap_steps: 10000
      play_against_latest_model_ratio: 0.5
      window: 10
```
- Запускаем обучение нашего млагента ```mlagents-learn Economic.yaml --run-id=Default --force```

![image](https://user-images.githubusercontent.com/77449049/206526789-48b9982c-01e0-42df-9a66-0e2cb7438c91.png)

![image](https://user-images.githubusercontent.com/77449049/206526671-c956c589-7f54-4b43-ae2a-7f1e6a1c01b2.png)

```
[INFO] Economic. Step: 5000. Time Elapsed: 45.997 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 10000. Time Elapsed: 70.843 s. Mean Reward: 0.000. Std of Reward: 1.000. Training. ELO: 1196.007.
[INFO] Economic. Step: 15000. Time Elapsed: 98.162 s. Mean Reward: 0.333. Std of Reward: 0.943. Training. ELO: 1192.519.
[INFO] Economic. Step: 20000. Time Elapsed: 125.449 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 25000. Time Elapsed: 153.310 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1189.615.
[INFO] Economic. Step: 30000. Time Elapsed: 177.865 s. Mean Reward: 0.500. Std of Reward: 0.866. Training. ELO: 1187.628.
[INFO] Economic. Step: 35000. Time Elapsed: 204.008 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 40000. Time Elapsed: 294.346 s. Mean Reward: 0.333. Std of Reward: 0.943. Training. ELO: 1184.307.
```

![image](https://user-images.githubusercontent.com/77449049/206540246-61bfd03d-4830-440a-a204-8c15bce19ec9.png)

- Теперь устанавливаем TensorBoard для демонстрации результатов на графиках

![image](https://user-images.githubusercontent.com/77449049/206529254-80f71a2f-0842-4774-838d-07e1b5d49ddb.png)

## Задание 2
### Опишите результаты, выведенные в TensorBoard. 
- Выведем результаты нашего обучения без изменений в TensorBoard
```tensorboard --logdir=results```

![image](https://user-images.githubusercontent.com/77449049/206540624-4d7acc95-3984-40ce-9706-048b4f4c932f.png)

![image](https://user-images.githubusercontent.com/77449049/206541190-d2f96625-c068-4ce1-8209-8a52976e6eec.png)

- Видим, что в какой момент накопительное вознаграждение (первый график) перестаёт расти и начинает убывать, попробуем добиться роста с помощью изменения параметров файла yaml

- Для начала попробум изменить network_settings -> hidden_units, количество единиц в скрытых слоях нейронной сети с 128 до 256
![image](https://user-images.githubusercontent.com/77449049/206542140-0fa1dd2e-02b9-4615-b13b-fcce49d36c61.png)

- Запустим обучение и посмотрим, как изменятся наши графики

```
[INFO] Economic. Step: 5000. Time Elapsed: 48.950 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 10000. Time Elapsed: 74.194 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.006.
[INFO] Economic. Step: 15000. Time Elapsed: 102.127 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.018.
[INFO] Economic. Step: 20000. Time Elapsed: 129.843 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 25000. Time Elapsed: 157.653 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.030.
[INFO] Economic. Step: 30000. Time Elapsed: 182.432 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.042.
[INFO] Economic. Step: 35000. Time Elapsed: 208.499 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 40000. Time Elapsed: 233.585 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.054.
```

![image](https://user-images.githubusercontent.com/77449049/206543706-a23766ef-8c88-400f-a42a-2682d89b8e2f.png)

- У нас везде стали получаться максимальные награды, теперь попробуем наоборот уменьшить количество единиц в скрытых слоях нейронной сети до 64

```
[INFO] Economic. Step: 5000. Time Elapsed: 42.174 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 10000. Time Elapsed: 67.379 s. Mean Reward: 0.000. Std of Reward: 1.000. Training. ELO: 1197.507.
[INFO] Economic. Step: 15000. Time Elapsed: 94.965 s. Mean Reward: 0.667. Std of Reward: 0.745. Training. ELO: 1193.102.
[INFO] Economic. Step: 20000. Time Elapsed: 122.021 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 25000. Time Elapsed: 149.582 s. Mean Reward: 0.500. Std of Reward: 0.866. Training. ELO: 1190.281.
[INFO] Economic. Step: 30000. Time Elapsed: 174.659 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1188.211.
[INFO] Economic. Step: 35000. Time Elapsed: 200.746 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 40000. Time Elapsed: 226.480 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1187.557.
```

![image](https://user-images.githubusercontent.com/77449049/206545200-8e176186-eceb-48dd-9f10-d753ef130d5a.png)

![image](https://user-images.githubusercontent.com/77449049/206546312-28d62201-a4ce-403e-8ff6-57f3a1f1ee9d.png)

- Заметим, что наш график несильно отличается от графика с обычными настройками, но в какой-то момент после падения опять начинает расти. При этом значения потреь также стало меньше

- Теперь вернём все настройки по умолчанию, но уменьшим значение энтропии (случайности) с 1e-2 до 1e-3 и num_epoch (количество эпох с 3 до 5)

```
[INFO] Economic. Step: 5000. Time Elapsed: 42.585 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 10000. Time Elapsed: 68.040 s. Mean Reward: 0.000. Std of Reward: 1.000. Training. ELO: 1196.590.
[INFO] Economic. Step: 15000. Time Elapsed: 97.477 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1193.186.
[INFO] Economic. Step: 20000. Time Elapsed: 125.117 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 25000. Time Elapsed: 155.005 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1192.281.
[INFO] Economic. Step: 30000. Time Elapsed: 180.446 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1192.044.
[INFO] Economic. Step: 35000. Time Elapsed: 208.564 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 40000. Time Elapsed: 234.001 s. Mean Reward: 0.833. Std of Reward: 0.553. Training. ELO: 1191.057.
```

![image](https://user-images.githubusercontent.com/77449049/206549445-e6153bae-4d9e-48e6-83c5-8d37e6966a76.png)

![image](https://user-images.githubusercontent.com/77449049/206549576-aca2c83e-f24f-49c8-ad61-37769c9428e1.png)

- Заметим, что случаёность как и предпологалось стала меньше, при этом мы получили более стабильный и красивый рост, думаю, что это изменение можно считать самым эффективным


## Выводы

Благодаря данной лабороторной работе я научился сравнивать результаты работы самобучающейся модели, и научился её анализировать.
Также закрепил навык работы с млагентами
| Plugin | README |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
