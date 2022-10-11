# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
- Утенков Руслан Мехманович
- РИ210940
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

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
Ознакомиться с основными операторами языка Python на примере реализации линейной регрессии.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
Ход работы:
### Python - Google-Sheets
- Создаём новый проект в google console

![image](https://user-images.githubusercontent.com/77449049/195097032-a354c9fa-3dbc-43b9-b789-1a67441826c9.png)

- Подключаем необходимые api 

![image](https://user-images.githubusercontent.com/77449049/195100524-0ee7666b-a584-45ec-8c37-105a13a80017.png)

- Предоставляем доступ к созданной таблице почты с сервисного аккаунта из google console 

![image](https://user-images.githubusercontent.com/77449049/195102611-237318b0-8070-4c30-9f3f-8b53cd5fa61d.png)

- Устанавливаем пакет gspread,numpy в наш проект

![image](https://user-images.githubusercontent.com/77449049/195103554-e5ef216e-00e8-42e4-85ed-e3a67942ea3e.png)

- Реализовываем запись данных из питона в нашу таблицу

```py
import gspread
import numpy as np
gc = gspread.service_account(filename='unitydatascience-365212-e1ab4836a0eb.json')
sh = gc.open("UnitySheet")
price = np.random.randint(2000, 10000, 11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i-1]-price[i-2])/price[i-2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.',',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)

```
- Теперь в нашу таблицу загружаются необходимые данные

![image](https://user-images.githubusercontent.com/77449049/195106336-038ec2aa-64b6-4c2e-9ee9-e55922a21b3b.png)

### Google-Sheets – Unity

- Создаём ключ api в google console и открываем доступ к таблице по ссылке

![image](https://user-images.githubusercontent.com/77449049/195107382-7c5dcdff-8a7b-4fef-b840-899c66f03053.png)

- Импортируем в Unity наш проект звуки и скрипты

![image](https://user-images.githubusercontent.com/77449049/195109254-ac9c85d3-d740-45a7-a728-66b435c6f420.png)

- Пишем скрипт для воспроизведение звуков в зависимости от значений в таблице


```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
    private bool statusStart = false;
    private int i = 1;

    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/1OV78kABwxT5I6IH4F******8t3CFgoUOOdNl6Jo1J2o/values/Лист1?key=AIzaS*****pFc24klkYDh2jsbTOuXufgW4crNc");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```
![image](https://user-images.githubusercontent.com/77449049/195125091-5bba6594-6373-41fe-8914-cb0800b3987c.png)

![image](https://user-images.githubusercontent.com/77449049/192773021-db624ed0-2f19-437e-a813-83e62b2648dd.png)

![image](https://user-images.githubusercontent.com/77449049/195125194-62a901ab-5ccb-4baf-bc7b-58a38d41dc42.png)

![image](https://user-images.githubusercontent.com/77449049/195125274-d7b3c5c1-f232-4513-a914-fd06cd9756c4.png)


## Задание 2
### Пошагово выполнить каждый пункт раздела "ход работы" с описанием и примерами реализации задач
Ход работы:
- Производим подготовку данных для работы с алгоритмом линейной регрессии
- 10 видов данных были установлены случайным образом, и данные находились в линейной зависимости. Данные преобразуются в формат массива, чтобы их можно было вычислить напрямую при использовании умножения и сложения.
```py
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
x = [3,21,22,34,54,34,55,67,89,99]
x = np.array(x)
y = [2,22,24,65,79,82,55,130,150,199]
y = np.array(y)
plt.scatter(x,y)
```


- Определяем связанные функции. Функция модели: определяет модель линейной регрессии wx+b. Функция потерь: функция потерь среднеквадратичной ошибки. Функция оптимизации: метод градиентного спуска для нахождения частных производных w и b.

```py
def model(a, b, x):
    return a * x + b
def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()
def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b
def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b
    
```
- Начинаем итерацию, меняем значение переменной times

1. Инициализация и модель итеративной оптимизации

```py
a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001
a, b = iterate(a, b, x, y, 1)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

![image](https://user-images.githubusercontent.com/77449049/192777275-09de1d38-5091-4ad9-9778-4fd1c5a8506f.png)

2. На второй итерации отображаются значения параметров, значения потерь и эффекты визуализации после итерации

```py
a,b = iterate(a,b,x,y,2)
#Меняем последнее значение, которое посылаем в функцию iterate
```

![image](https://user-images.githubusercontent.com/77449049/192776051-ccb6881e-0d75-434b-ae65-e8a128fd2be6.png)

3. Третья итерация показывает значения параметров, значения потерь и визуализацию после итерации

![image](https://user-images.githubusercontent.com/77449049/192776280-8ae2a7c4-72e8-4fb2-8d41-8c8d50974f4e.png)

4. На четвертой итерации отображаются значения параметров, значения потерь и эффекты визуализации

![image](https://user-images.githubusercontent.com/77449049/192776371-02236dc5-8545-4735-841d-62c8bea81ddf.png)

5. Пятая итерация показывает значение параметра, значение потерь и эффект визуализации после итерации

![image](https://user-images.githubusercontent.com/77449049/192777187-4a26bafe-64fd-4946-97b9-354d0d28aa21.png)

6. 10000-я итерация, показывающая значения параметров, потери и визуализацию после итерации

![image](https://user-images.githubusercontent.com/77449049/192776626-a0983687-ca6e-4b65-b0cf-a9ba08a2fc16.png)

## Задание 3
### Должна ли величина loss стремиться к нулю при изменении исходных данных? Какова роль параметра Lr?

- Должна ли величина loss стремиться к нулю при изменении исходных данных?
Как мы можем увидеть из предыдущих выводов в консоли, то при увеличении количества итерации величина loss уменьшается
При изменении переменных x,y


![image](https://user-images.githubusercontent.com/77449049/192779342-97a26015-62f1-4203-92ab-b05cb26e7f27.png)


Можем увидить, что значение loss также уменьшается


![image](https://user-images.githubusercontent.com/77449049/192779660-9fa99c00-930c-4678-912b-68171925882d.png)


Но возможно при большом количестве итераций, оно примет какоё-то стабильное значение

- Какова роль параметра Lr?
Предполагаю, что роль параметра Lr в том, чтобы регулировать общую величину значений x, y
Так как при его изменении меняется максимальные значения по осям


## Выводы

Выполнив данную лабораторную работу, я улучшил навыки анализа программ, базово освоил устройство работы unity, google.colab и текстового редактора github

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
