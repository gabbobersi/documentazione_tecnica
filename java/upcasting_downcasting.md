## Upcasting & Downcasting
![](../resources/upcasting_downcasting_1.png)

### Upcasting
`sottoclasse` --> `superclasse`\
Upcasting significa convertire il riferimento a una sottoclasse, in un riferimento a una superclasse.\
E' **implicito**.

### Downcasting
`superclasse` --> `sottoclasse`\
Downcasting significa convertire il riferimento a una superclasse, in un riferimento a una sottoclasse.\
E' **esplicito**.

### Esempio complessivo
```Java
class Animal {
}

class Cat extends Animal {
    public void doSomething(){
        System.out.println("Hey! I'm a cat!");
    }
}

public class Main {
    public static void main(String[] args){
        // Upcasting (implicito)
        Animal animal = new Cat();

        // Downcasting (esplicito - aka "cast" necessario)
        if (animal instanceof Cat && animal != null) {
            Cat cat = (Cat) animal;
            cat.doSomething();
        }
    }    
}
```
Notate il controllo `instanceof` per verificare che `Cat` sia effettivamente una sottoclasse
di `Animal`.

Supponiamo di non effettuare la verifica con `instanceof` e che `Cat` non è sottoclasse di `Animal`.\
In questo caso, otteniamo una `ClassCastException`.

Notate inoltre che controllo se la classe `animal` è effettivamente istanziata (e non null) prima del confronto... 