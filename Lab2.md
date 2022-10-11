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
### Python
- Создаём новый файл в google.colab ![image](https://user-images.githubusercontent.com/77449049/192769051-501ae1fe-0944-4a0a-9f4e-603e38fd52d0.png)
- Пишем код программы для вывода надписи и запускаем её 
![image](https://user-images.githubusercontent.com/77449049/192768891-f228fda3-242d-4f7f-b242-ffd2129eaeff.png)
- Сохраняем нашу программу на облачном диске

![image](https://user-images.githubusercontent.com/77449049/192769159-13f02403-af1c-4dcf-8da3-a0c426193eeb.png)

### Unity
- Создаём проект в unity и добавляем в него компонент new script ![image](https://user-images.githubusercontent.com/77449049/192769639-c2229907-f66d-42fb-8497-f2febfe104ed.png)
![image](https://user-images.githubusercontent.com/77449049/192770032-d670ead8-85cd-42a5-99f1-55f38eb61579.png)
- В созданном компоненте с помощью visual studio code пишем программу по выводу консоль надписи и сохраняем её
![image](https://user-images.githubusercontent.com/77449049/192770140-f949b7ee-1e79-48e4-ac09-e3ae8707f173.png)
- После запуска сцены мы должны увидеть надпись в консоле
![image](https://user-images.githubusercontent.com/77449049/192771890-3829a62f-6ae6-4bc9-b8df-5311030aee9d.png)


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
![image](https://user-images.githubusercontent.com/77449049/192773021-db624ed0-2f19-437e-a813-83e62b2648dd.png)

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
