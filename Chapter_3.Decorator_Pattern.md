## Определение
`Паттерн Декоратор` динамически наделяет объект новыми возможностями и являются гибкой альтернативой субклассированию в области расширения функциональности
## Принцип проектирования:
1. Классы должны быть открыты для расширения, но и закрыты для изменения (Также, данный принцип является один из принципов SOLID (O - open-closed principle))
## Диаграмма классов паттерна
![[Chapter_3.Decorator_diagramm.png]]
## Пример реализации
Для того, чтобы паттерн заработал, нужно создать абстрактный базовый тип (интерфейс/абстрактный класс). Как по мне, интерфейс лучше.
```java
public abstract class Beverage {
	private final String description;
	{
		this.description = "Unknown";
	} 

	public String getDescription() {
		return this.description;
	}

	public abstract Double cost();
}

public interface Beverage {
	String getDescription();
	Double getCost();
}
```
Далее, создадим #Decorator
```java
public abstract class Supplies implements Beverage { /* или же extends */
	private final Beverage beverage;
}
```
Объекты, которые будем оборачивать в декоратор, имплементируют `Beverage`
```java
public class Espresso implements Beverage {
	private final String description;
	private final Double cost
	{
		this.description = "Espresso";
		this.cost = 0.89;
	}

	public Espresso() {}

	@Override
	public String getDescription() {
		return this.description;
	}

	@Override
	public Double getCost() {
		return this.cost;
	}
}
```
Реализации #Decorator 
```java
public class Mocha extends Supplies {
	private final Double cost;
	{
		this.cost = 0.20;
	}

	public Mocha(Beverage beverage) {
		this.beverage = beverage;
	}

	@Override
	public String getDescription() {
		return this.beverage.getDescription() + ", Mocha";
	}

	@Override
	public Double getCost() {
		return this.beverage.getCost() + this.cost;
	}
}

public class Whip extends Supplies {
	private final Double cost;
	{
		this.cost = 0.29;
	}

	public Whip(Beverage beverage) {
		this.beverage = beverage;
	}

	@Override
	public String getDescription() {
		return this.beverage.getDescription() + ", Whip";
	}

	@Override
	public Double getCost() {
		return this.beverage.getCost() + this.cost;
	}
}
```
Пример функционала:
```java
public static void main() {
	Beverage beverage = new Espresso();
	beverage = new Mocha(beverage);
	beverage = new Whip(beverage);
	System.out.println(beverage.getCost());
}
```