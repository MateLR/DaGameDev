# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Утенков Руслан Мехманович
- РИ210940
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | # | 20 |

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
### познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.
Ход работы:
- Создаём новый проект в Unity, добавлля в него MLAgents и PyTorch
![image](https://user-images.githubusercontent.com/77449049/200846995-87b3ca01-d9b4-4d81-9d54-a9d0958b2ff3.png)
![image](https://user-images.githubusercontent.com/77449049/200848470-71c03d89-07cc-4488-bc27-3c820e82bc73.png)
![image](https://user-images.githubusercontent.com/77449049/200849660-5b040c41-53f1-47c2-822c-a0caf09a3b5f.png)


- Добавляем плоскость, цель в виде куба и сферу ![image](https://user-images.githubusercontent.com/77449049/198318367-7a549859-80a1-49c8-b5ad-2d3b062100a9.png)
- Добавляем скрипт к сфере RollerAgent

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public Transform Target;
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
    public float forceMultiplier = 10;
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
- Добавляем ещё компоненты Rigidbody, Decision Requester, Behavior Parameters, и настроил их следующим образом: 
![image](https://user-images.githubusercontent.com/77449049/198334038-c73a8934-953a-4ef2-8172-b7b5eae3d435.png)
- Начинаем работу MlAgent с использованием файла конфигурации нейронной сети

```
behaviors:
  RollerAgent:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4
      epsilon: 0.2
      lambd: 0.99
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000
    time_horizon: 64
    summary_freq: 10000
```
![image](https://user-images.githubusercontent.com/77449049/200870787-cdb2dc3f-044e-4f62-a704-ffcc80468f90.png)
![image](https://user-images.githubusercontent.com/77449049/200870985-82555238-cf76-44b8-93a8-061e2d1718e3.png)
![image](https://user-images.githubusercontent.com/77449049/200871045-e68f8190-dce3-4666-8611-4b95a20ddb0d.png)

-Далее рассмотрим работу агента с 3,9,27 копиями модели
![image](https://user-images.githubusercontent.com/77449049/200871593-9cfafe7e-cf18-4754-9ea0-1aff9627f5b2.png)
![image](https://user-images.githubusercontent.com/77449049/200871613-5255f70f-600b-4234-bade-1815bab0c4a0.png)
![image](https://user-images.githubusercontent.com/77449049/200871643-015866dd-29bb-4e36-9d26-26ca716ef92f.png)

- Готовую обученную модель добавим к модели поведения сферы
- ![image](https://user-images.githubusercontent.com/77449049/200874244-0abc7b69-6749-423f-b87e-1b10e9214028.png)

## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети
```
behaviors:
  RollerAgent: # имя агента
    trainer_type: ppo # алгоритм обучения Proximal Policy Optimization
    hyperparameters: # гиперпараметры
      batch_size: 10 #
      buffer_size: 100 #
      learning_rate: 3.0e-4 # начальная скорость обучения для градиентного спуска
      beta: 5.0e-4 # параметр регуляции энтропии
      epsilon: 0.2 # допустимый порог разности расхождений при обновлении с градиентным спуском
      lambd: 0.99 # учёт погрешности значений для расчёта предсказаний
      num_epoch: 3 № # количество проходов, которые необходимо выполнить через буфер опыта при выполнении оптимизации
      learning_rate_schedule: linear # линейная скорость обучения
    network_settings: # настройки нейронной сети
      normalize: false # применения нормализации к входным векторам
      hidden_units: 128 # количество нейронов в скрытых слоях в нейронной сети
      num_layers: 2 # количество скрытых слоёв в нейронной сети
    reward_signals: # настройки для сигналов вознаграждения
      extrinsic:
        gamma: 0.99 # коэффициент дисконтирования для будущих вознаграждений, поступающих от окружающей среды
        strength: 1.0 # значения для произведения с награждением
    max_steps: 500000  # количество шагов для завершения обучения
    time_horizon: 64 # сколько шагов опыта нужно собрать для каждого агента
    summary_freq: 10000 # количество опыта, которое необходимо собрать перед созданием и отображением статистики обучения
```
Decision Requester - компонент который запрашивает решения для агента через совершенно разные промежутки времени, вызывая функцию RequestDecision у класса Agent, от которого наследуется скрипт.
Behavior Parameters - компонент который определяет, как объект принимает решения.
## Выводы

Благодаря данной работе я научился работать с обучением нейронных сетей с помощью ml агента на практике, что позволит применять машинное обучение для уменьшения затрат
по времени для балансировки игровых процессов, что очень важно для создания хорошей игры.

| Plugin | README |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
