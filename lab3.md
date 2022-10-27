# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
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
### познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.
Ход работы:
- Создаём новый проект в Unity, добавлля в него MLAgents и PyTorch
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

- 

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
Ход работы:

- Адаптируем код с прошлой лабораторной для передачи данных в таблицу

```py
import gspread
import numpy as np
x = [2,23,23,33,54,34,52,67,89,99]
x = np.array(x)
y = [1,12,24,61,19,81,55,130,110,293]
y = np.array(y)
gc = gspread.service_account(filename='unitydatascience-365212-e1ab4836a0eb.json')
sh = gc.open("UnitySheet")
def model(w, b, x):
    return w*x + b
def lossFun(w, b, x, y):
    num = len(x)
    predic = model(w, b, x)
    return (0.5/num) * (np.square(predic-y)).sum()
def optimization(lr, w, b, x, y):
    num = len(x)
    predic = model(w, b, x)
    dw = (1.0/num) * ((predic - y) * x).sum()
    db = (1.0/num) * ((predic - y).sum())
    w = w - lr*dw
    b = b - lr*db
    return w, b
def iter(lr, w, b, x, y, times):
    for i in range(times):
        w, b = optimization(lr, w, b, x, y)
    return w,b
lr = 0.000001
a_f = np.random.rand(1)
print(a_f)
b_f = np.random.rand(1)
print(b_f)
a = np.copy(a_f)
b = np.copy(b_f)
n_a = np.arange(1, 6) * 200
n_a = np.concatenate([[1], n_a])
a_loss = []
for i, n in enumerate(n_a):
    a, b = iter(lr, a, b, x, y, n)
    pred = model(a, b, x)
    loss = lossFun(a, b, x, y)
    a_loss.append(loss)
    print(a, b, loss)
    sh.sheet1.update(('A' + str(i + 1)), str(n))
    sh.sheet1.update(('B' + str(i + 1)), f"{a[0]:.3f}".replace('.',','))
    sh.sheet1.update(('C' + str(i + 1)), f"{b[0]:.3f}".replace('.',','))
    sh.sheet1.update(('D' + str(i + 1)), f"{loss:.3f}".replace('.',','))
```
![image](https://user-images.githubusercontent.com/77449049/195128628-51ec4434-632b-4183-9d1d-ace35ff0d5e6.png)

![image](https://user-images.githubusercontent.com/77449049/195128758-ec718d8c-cc8c-48da-bc3f-211f4f9e1e5f.png)


## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

- Будем считывать данные с 4 колонки таблицы, то есть будем работать со значением loss

```py
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[3]));
```

- Также изменим сценарий воспроизведения звука, например, x > 3000 - Normal; x > 4000 - Great; x <= 3000 - Bad;

```py
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] > 4000 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
        if (dataSet["Mon_" + i.ToString()] > 3000 & dataSet["Mon_" + i.ToString()] < 4000 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
        if (dataSet["Mon_" + i.ToString()] <= 3000 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }
```

## Выводы

С помощью данной работы я научился взаимодействовать с api и передачей данных в таблицу, а также считыванием информации с таблицы

| Plugin | README |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
