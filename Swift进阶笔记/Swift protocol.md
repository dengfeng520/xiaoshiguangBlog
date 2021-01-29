```
class Hero: NSObject {
    var name: String = String()
    var profession: String = String()
}

class Archer: Hero {
    func archerying() {
        print("---------archerying")
    }
}

class Mage: Hero {
    func castsSpells() {
        print("---------mage casts spells")
    }
}

class Tank: Hero {
    func takeDamage() {
        print("---------tank take damage")
    }
}
```

现在我有一个全能，射手、法师、tank全都会，那该如何处理呢？

```
class Almighty: NSObject {
    
}
```

对OOP只关心对象是谁

POP并不关心对象是谁，只关心