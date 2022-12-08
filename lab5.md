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
### Измените параметры файла. yaml-агента и определить какие параметры и
как влияют на обучение модели.
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
![image](https://user-images.githubusercontent.com/77449049/206526789-48b9982c-01e0-42df-9a66-0e2cb7438c91.png)

![image](https://user-images.githubusercontent.com/77449049/206526671-c956c589-7f54-4b43-ae2a-7f1e6a1c01b2.png)
```
[INFO] Economic. Step: 5000. Time Elapsed: 43.754 s. No episode was completed since last summary. Training.
[INFO] Economic. Step: 10000. Time Elapsed: 68.998 s. Mean Reward: 1.000. Std of Reward: 0.000. Training. ELO: 1200.006.
```
![image](https://user-images.githubusercontent.com/77449049/206535494-01ebac34-da3c-47c3-a9bb-8db9d3595136.png)

![image](https://user-images.githubusercontent.com/77449049/206529254-80f71a2f-0842-4774-838d-07e1b5d49ddb.png)

## Задание 2
### Опишите результаты, выведенные в TensorBoard. 
![image](https://user-images.githubusercontent.com/77449049/206538017-92851340-8363-44e1-9966-83a12b1868d6.png)


## Выводы

С помощью этой работы я узнал работу перцептрона и научился использовать его в проекта unity, что в будущем окажет влияния на мои умения внедрения
нейронов в игровые проекты

| Plugin | README |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
