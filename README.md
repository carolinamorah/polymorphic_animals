# README

# PART II (diagrama de modelos está en un archivo .png en la raíz)

Cómo implementar polimorfismo a la clase Animal

## Primer Paso
Creamos un nuevo proyecto

 ```$ rails new animals_polymorphic``` 
 
## Segundo Paso
Creamos los modelos. El modelo *Animal* hará referencia de tipo polimórfica a las 3 especies, así que al campo le daremos el nombre "specie"(también podemos llamarlo como indica la convención "animalable")

 ``` $ rails g model  Animals specie:references{polymorphic} ```
 
 ``` $ rails g model Cat ```  
 
 ``` $ rails g model Dog ```
 
 ``` $ rails g model Cow ``` 

## Tercer Paso
Hacemos las relaciones en los modelos

```ruby
class Animal < ApplicationRecord
   belongs_to :specie, polymorphic: true
end

class Cat < ApplicationRecord
   has_many :animals , as: :specie
end

class Dog < ApplicationRecord
   has_many :animals , as: :specie
end

class Eagle < ApplicationRecord
   has_many :animals , as: :specie
end
```

## Cuarto Paso
Ahora creamos un Animal con sus especies instanciadas desde la consola.

### Consola 
 ```$ rails c``` 

```
Animal.create(specie:Cat.new)

=> #<Animal id: 1, specie_type: "Cat", specie_id: 1, created_at: "", updated_at: "">

Animal.create(specie:Dog.new)

=> #<Animal id: 2, specie_type: "Dog", specie_id: 1, created_at: "", updated_at: "">

Animal.create(specie:Cow.new)

=> #<Animal id: 3, specie_type: "Cow", specie_id: 1, created_at: "", updated_at: "">

```

También podemos hacer lo mismo creando las instancias de los hijos y luego insertarlas al padre.

```
dog = Dog.new
Animal.create(specie:dog)

cat = Cat.new
Animal.create(specie:cat)

cow = Cow.new
Animal.create(specie:cow)
```

## Quinto Paso

Cómo hacer interactuar a la clase padre (Animal) con sus hijos (Cat, Dog, Cow) a través de un mètodo heredable.

```ruby
class Animal < ApplicationRecord
  def talk 
    puts 'I am an animal, teach me how to talk' 
  end 
end

class Cat < Animal 
  def talk 
    puts 'Miau miau' 
  end 
end 

class Dog < Animal 
  def talk 
    puts 'Guau guau' 
  end     
end
```

### Consola 
 ```$ rails c``` 

 instanciamos las clases en una variable y a la variable le damos el método talk. 

```
 animal = Animal.new 
 animal.talk
 => I'm an animal, teach me to talk

 animal = Cat.new
 animal.talk
 => Miau miau

 animal = Dog.new
 animal.talk
 => Guau guau
```

- También podemos agregar un comportamiento de manera dinámica a un objeto sin afectar el comportamiento de otros objetos de la misma clase, a través del *decorador*. Este decorador lleva como argumento al objeto de tipo Animal.

```ruby
class Animal 
  def talk 
    puts 'I am an animal, teach me how to talk'
  end 
end

class Dog 
  def initialize(animal) 
    @animal = animal 
  end
  
  def talk 
    @animal.talk 
      puts 'Guau guau' 
  end 
end

class Cat 
  def initialize(animal) 
    @animal = animal 
  end

  def talk 
    @animal.talk 
      puts 'Miau miau' 
  end 
end
```

### Consola
Instanciamos la clase Animal. Luego instanciamos las otras clases y le damos el parámetro referenciado(animal) y el método.

```
animal = Animal.new 
Dog.new(animal).talk
=> I'am an animal, teachme how to talk"
=> Guau guau

Cat.new(animal).talk
=> I'am an animal, teachme how to talk"
=> Miau miau
```

Y finalmente, podemos crear polimorfismo a través de "Duck Typing". Que es cuando un objeto que es pasado como parámetro a una función, soporta todos los métodos y atributos.

```ruby
class Animal 
  def talk(animal) 
    animal.talk 
  end 
end

class Dog 
  def talk 
    puts 'Guau guau' 
  end 
end

class Cat 
  def talk 
    puts 'Miau miau' 
  end 
end
```

### Consola

Instanciamos el objeto principal en una variable y a esa variable le damos el método talk que tendrá como parámero la instancia de Dog o Cat.

```
animal = Animal.new 
animal.talk(Dog.new)
=> Guau guau

animal = Animal.new
animal.talk(Cat.new)
=> Miau miau
```



