## Определение
`Паттерн Стратегия` определяет семейство алгоритмов, инкапсулирует каждый из них и обуспечивает их взаимозаменяемость. Он позволяет модифицировать алгоритмы независимо от их использования на стороне клиента.
## Принцип паттерна:
1. Выделите аспекты приложения, которые могут изменяться, и отделите их от тех, которые всегда остаются постоянными
2. Программируйте на уровне интерфейса, а не на уровне реализации
3. Отдавайте предпочтение композиции, а не наследованию
### Программируйте на уровне интерфейса, а не реализации
При наличии часто изменяющегося кода, например, перегруженный методы в одном классе, который может менять свою логику, лучше заменить на интерфейс, который через setter можно изменять.
Также, лучше инкапсулировать данную логику методом в классе, который уже будет дергать метод интерфейса.
```java
public abstract class Person {
	private DestinationShipping destinationShipping;

	public void setDestinationShipping(DestinationShipping destinationShipping) {
		this.destinationShipping = destinationShipping;
	}

	public void getToDestination() {
		this.destinationShipping.performShipping();
	}
}

public interface DestinationShipping {
	void performShipping();
}

public class Walk implements DestinationShipping {
	@Override
	public void performShipping() {
		System.out.println("I'm walking");
	}
}

public class Taxi implements DestinationShipping {
	@Override
	public void performShipping() {
		System.out.println("I'm taking a taxi");
	}
}
```
Делать классы, реализующие `Person` впадлу :)
## Мозговой штурм:
Утиный манок используется охотниками для имитации утиного кряканья. Как бы вы реализовали собственную версию манка?
```java
public abstract class Decoy {
	private SoundBehavior soundBehavior;

	public setSoundBehavior(SoundBehavior soundBehavior) {
		this.soundBehavior = soundBehavior;
	}

	public performSound() {
		this.soundBehavior.makeSound();
	}
}

public interface SoundBehavior {
	void makeSound();
}

public class DuckSound implements SoundBehavior {
	@Override
	public void makeSound() {
		System.out.println("Кря епта");
	}
}
```
