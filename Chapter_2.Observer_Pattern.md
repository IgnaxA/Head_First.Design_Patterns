## Определение
`Паттерн Наблюдатель` определяет отношение "один-ко-многим" между объектами таким образом, что при изменении состояния одного объекта происходит автоматического оповещение и обновление всех зависимых объектов
## Принцип проектирования:
1. Стремитесь к слабой связности взаимодействующих объектов
### Диаграмма классов паттерна
![[Chapter_2.Pattern_diagramm.png]]

## Пример реализации
Подготовим основые интерфейсы:
- #Subject (Субъект) - объекты классов, реализующий этот интерфейс, уведомляют подписанных на них наблюдателей об изменении данных
- #Observer (Наблюдатель) - объекты классов, реализующий этот интерфейс, получают информацию от #Subject Субъекта об изменении данных
- DisplayElement - мок для примера
```java
public interface Subject {
	public void register(Observer obs);
	public void remove(Observer obs);
	public void notify();
}

public interface Observer {
	public void update();
}

public interface DisplayElement {
	public void display();
}
```

Далее, напишем реализацию для #Observer. В данном случае присутствует логика с реализацией методов интерфейса, которая будет дублироваться для каждого нового класса субъекта.  
Однако, является ли это истинным дублированием? Не сказал бы. Если вынести реализацию интерфейса в отдельный класс, а затем наследовать его для каждого нового #Subject , то рано или поздно в каком-либо субъекте понадобится своя логика для данных методов.  
Думаю, можно применить [[Chapter_1.Strategy_Pattern]]. Надо будет протестить.
```java
public class WeatherData implements Subject {
	private final List<Observer> observers;
	private boolean isChanged;
	private int temperature;
	{
		this.observers = new ArrayList();
		this.isChanged = false;
	}

	public int getTemperature() {
		return this.temperature;
	}

	@Override
	public synchronized void register(@NotNull Observer obs) {
		if (!this.observer.contains(obs)) {
			this.obeservers.add(obs);
		}
	}

	@Override
	public synchronized void remove(@NotNull Observer obs) {
		this.observer.remove(obs);
	}

	// При написании реального кода надо вынести манипуляции с флагом isChanged в отдельные методы
	@Override
	public void notify() {
		synchronized(this) {
			if (!this.isChanged) {
				return;
			}
			this.isChanged = false;
		}
		this.obeservers.forEach(Observer::update);
	}

	public void measurementChanged() {
		this.isChanged = true;
		this.notify();
	}

	// Делать ли данный метод synchronized? Думаю, зависит от задачи 
	public void setMeasurement(int temperature) {
		this.temperature = temperature;

		this.measurementChanged();
	}
}
```
Также, для примера, реализация #Observer 
```java
public class SomeDisplay implements Observer, DisplayElement {
	private final Subject subject;
	private int temperature;
	

	public SomeDisplay(@NotNull Subject subject) {
		this.subject = subject;
		this.subject.register(this);
	}

	// Это реализация из издания 2018 года. Мне она больше нравится, так как позволяет прокидывать разные субъекты. Если использовать >17 версию java, то можно заюзать pattern-matching
	@Override
	public void update() {
		if (this.subject instanceof WeatherData weatherData) {
			this.temperature = weatherData.getTemperature();
			this.display();
		}
	}

	@Override
	public void display() {
		// Реализация... 
	}
}
```