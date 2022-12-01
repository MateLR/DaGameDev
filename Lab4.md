# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 - Перцептрон выполнил(а):
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
### В проекте Unity реализовать перцептрон, который умеет делать вычисления OR\AND\NAND\XOR
Ход работы:
- Создаём новый проект в Unity
![image](https://user-images.githubusercontent.com/77449049/205114978-6e6beb32-c4b2-4a94-99e0-affc28e904c4.png)
- Привязываем пустому объекту скрипт перцептрона
![image](https://user-images.githubusercontent.com/77449049/205120180-cf3ee78d-8958-4685-a795-d905a1e992b8.png)

```py
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8); // общее количество эпох для обучения
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}
```
- Начинаем обучение для Or - ИЛИ, для этого выставим соответствующие для входные и выходные данные. В моём случае на 4-ой итерации перцептрон научился работать с или
![image](https://user-images.githubusercontent.com/77449049/205122040-2f9f610d-5051-47e5-9679-f7e5f474d1f0.png)
- Для AND перцептрон обучился за 6 эпох
![image](https://user-images.githubusercontent.com/77449049/205123948-f6c7c4f2-7192-43ce-9dde-594b9e3d46f5.png)
- NAND обучился на 4-ой эпохе
![image](https://user-images.githubusercontent.com/77449049/205124362-824a07e3-144c-40e8-9835-827a1c1c5157.png)
- XOR не получилось обучить даже за 1000 эпох
![image](https://user-images.githubusercontent.com/77449049/205124825-a6d4b136-93aa-43a0-b05e-6fb91e6aedf0.png)

## Задание 2
### Построить графики зависимости количества эпох от ошибки обучения. Указать от чего зависит необходимое количество эпох обучения
![image](https://user-images.githubusercontent.com/77449049/205127446-83436cf1-70a9-41bd-bf1f-d2717ba63538.png)
- Количество эпох обучения зависит от входных весов w1, w2, а также от возможности линейной раздилимости нашей задачи
## Задание 3
### Построить визуальную модель работы перцептрона на сцене Unity

## Выводы

Благодаря данной работе я научился работать с обучением нейронных сетей с помощью ml агента на практике, что позволит применять машинное обучение для уменьшения затрат
по времени для балансировки игровых процессов, что очень важно для создания хорошей игры.

| Plugin | README |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
