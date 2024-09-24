## Определение
`Паттерн абстрактная фабрика` предоставляет интерфейс создания семейств взаимосвязанных объектов или взаимозависимых объектов без указания их конкретных классов
`Фабричный метод` определяет интерфейс создания объекта, но позволяет субклассам выбрать создаваемый экземпляр
## Принцип проектирования
1. Классы должны зависить от абстракций, а не конкретных классов (Также, данный принцип является один из принципов SOLID (D - dependency inversion principle))
## Диаграмма классов паттерна
![[Chapter_4.Factory_diagramm.png]]
## Пример реализации
В книге приводится пример реализации фабричного метода и абстрактной фабрики. Для начала надо сделать перечисление с пиццами для их последующего создания в фабрике
```java
public enum PizzaType {
	CHEESE {
		@Override
		public String getName() {
			return "Cheese pizza";
		}
	},
	VEGGIE {
		@Override
		public String getName() {
			return "Veggie pizza";
		}
	},
	CLAM {
		@Override
		public String getName() {
			return "Clam pizza";
		}
	};

	public abstract String getName();
}
```

Далее, создадим абстракции для фабричного метода и абстрактной фабрики
```java

// Абстрактная фабрика
public abstract class PizzaStore {

	public Pizza orderPizza(PizzaType pizzaType) {
		Pizza pizza = this.createPizza(pizzaType);

		pizza.bake();
		pizza.box();

		return pizza;
	}

	protected abstract Pizza createPizza(PizzaType pizzaType);
}

// Абстракция для фабричного метода
public interface PizzaIngredients {
	Dough getDough();
	Sauce getSauce();
}

// Абстракция объекты которой будет создаваться в абстрактной фабрике
public abstract class Pizza {
	private String name;

	// Ингредиенты
	private Dough dough;
	private Sauce sauce;

	public abstract void prepare();

	public void setName(String name) {
		this.name = name;
	}

	public String getName() {
		return this.name;
	}

	public void bake() {
		// Реализация...
	}

	public void box() {
		// Реализация...
	}
}
```
И, наконец, реализации
```java
// Один ищ примеров реализации абстрактной фабрики
public class NYPizzaStore extends PizzaStore {
	@Override
	protected Pizza createPizza(PizzaType pizzaType) {
		PizzaIngredients ingredients = new NYPizzaIngredients();
		

		Pizza pizza = switch(pizzaType) {
			case PizzaType.CHEESE: return new CheesePizza(ingredients);
			case PizzaType.VEGGIE: return new VeggiePizza(ingredients);
			case PizzaType.CLAM: return new ClamPizza(ingredients);
		};
		pizza.setName(pizzaType.getName());

		return pizza;
	}
}

// Один из примеров реализации абстракции для фабричного метода
public class NYPizzaIngredients implements PizzaIngredients {
	public Dough getDough() {
		return new ThinCrustDough();
	}

	public Sauce getSauce() {
		return new MarinaraSauce();
	}
}

public class CheesePizza extends Pizza {
	private final PizzaIngredients pizzaIngredients;

	// Применение принципа инверсии зависимостей
	public CheesePizza(PizzaIngredients pizzaIngredients) {
		this.pizzaIngredients = pizzaIngredients;
	}

	@Override
	public void prepare() {
		System.out.println("Preparing " + name);

		this.dough = pizzaIngredients.getDough();
		this.sauce = pizzaIngredients.getSauce();
	}
}
```