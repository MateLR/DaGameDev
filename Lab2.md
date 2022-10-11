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

![image](https://user-images.githubusercontent.com/77449049/195125194-62a901ab-5ccb-4baf-bc7b-58a38d41dc42.png)

![image](https://user-images.githubusercontent.com/77449049/195125274-d7b3c5c1-f232-4513-a914-fd06cd9756c4.png)


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

```
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[4]));

```

- Также изменим сценарий воспроизведения звука, например, x > 300 - Normal; x > 800 - Great; x <= 300 - Bad;

```
    void Update()
    {
        if (dataSet["Mon_" + i.ToString()] > 800 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 300 & dataSet["Mon_" + i.ToString()] < 800 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] <= 300 & statusStart == false & i != dataSet.Count)
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
