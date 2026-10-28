# Lekce: třídy, abstraktní třídy, interface a factory

V této lekci si procvičíš pět věcí, které v Javě budeš potkávat pořád:

1. **třídy** – jak si vytvořit vlastní typ objektu,
2. **abstraktní třídy** – společný základ pro několik podobných tříd,
3. **interface** – popis toho, co objekt *umí*,
4. **factory** – místo, které za tebe objekty vytváří,
5. **service** – třída, která řídí nějaký proces a používá k tomu ostatní objekty.

Lekce má dvě zadání:

- **Zadání 1: RPG hra** – s výkladem a ukázkami, děláme spolu.
- **Zadání 2: Rozvozová služba** – samostatná práce, stejné principy na jiném příběhu.

> Ukázky ve výkladu jsou schválně na jiném tématu, než je zadání. Neopisuj je, ale použij stejný princip.

---

# Zadání 1: RPG hra „Aréna“

Naprogramuješ jednoduchou textovou hru. V aréně proti sobě bojují dvě postavy (válečník, mág nebo lučištník). Střídají se v útocích, dokud jedna z nich neprohraje. Každá postava bojuje jinak, a právě to je důvod, proč pro ně budeme mít různé třídy.

Hru postavíš postupně v šesti krocích. Každý krok přidá jeden nový koncept.

---

## Krok 1: Třídy – zbraň

### Výklad

**Třída** je šablona (plán) pro objekty. Popisuje:

- **atributy** – jaká data objekt má (např. název, poškození),
- **konstruktor** – jak se objekt vytvoří a naplní daty,
- **metody** – co objekt umí dělat.

**Objekt** (instance) je konkrétní „věc“ vytvořená podle třídy pomocí `new`.

Atributy dáváme `private`, aby je nikdo zvenku nemohl jen tak přepsat. Pro čtení přidáme **gettery**.

### Ukázka

```java
public class Book {
    private String title;
    private int pages;

    public Book(String title, int pages) {
        this.title = title;
        this.pages = pages;
    }

    public String getTitle() {
        return title;
    }

    public int getPages() {
        return pages;
    }

    public void printInfo() {
        System.out.println("Kniha: " + title + " (" + pages + " stran)");
    }
}
```

```java
Book hobbit = new Book("Hobit", 310);
Book potter = new Book("Harry Potter", 280);
hobbit.printInfo();   // Kniha: Hobit (310 stran)
```

Všimni si: dvě různé knihy jsou dva **objekty** jedné **třídy**. Nepotřebujeme třídu `Hobbit` a třídu `HarryPotter`, protože se knihy liší jen daty, ne chováním.

### Zadání

Vytvoř třídu **`Weapon`** (zbraň):

| Co | Název | Typ |
|---|---|---|
| atribut | `name` | `String` |
| atribut | `damage` | `int` |
| konstruktor | `Weapon(String name, int damage)` | |
| getter | `getName()` | `String` |
| getter | `getDamage()` | `int` |
| metoda | `printInfo()` | vypíše např. `Zbraň: Meč (poškození 15)` |

V `Main` vytvoř dvě zbraně (např. `Meč` s poškozením 15 a `Sekera` s poškozením 20) a vypiš je.

> **Zamysli se:** Proč nevytváříme třídy `Sword` a `Axe`?

---

## Krok 2: Abstraktní třída – herní postava

### Výklad

Válečník, mág i lučištník mají spoustu věcí společných: jméno, životy a umí dostat zásah. Útočí ale každý jinak. Společné věci napíšeme jen jednou do **rodičovské třídy** a jednotlivé postavy z ní **zdědí** (`extends`).

Rodičovská třída bude **abstraktní** (`abstract`):

- **nejde z ní vytvořit objekt** (`new GameCharacter(...)` nefunguje), protože „obecná postava“ ve hře neexistuje,
- může obsahovat **abstraktní metody**, tedy metody bez těla. Každý potomek je **musí** naprogramovat sám (`@Override`),
- může obsahovat i normální metody s tělem, které potomci prostě zdědí.

Potomek volá konstruktor rodiče pomocí `super(...)`.

### Ukázka

```java
public abstract class Shape {
    private String name;

    public Shape(String name) {
        this.name = name;
    }

    // Každý tvar počítá obsah jinak -> abstraktní metoda
    public abstract double getArea();

    // Tohle je pro všechny tvary stejné -> normální metoda
    public void printArea() {
        System.out.println(name + " má obsah " + getArea());
    }
}
```

```java
public class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        super("Kruh");
        this.radius = radius;
    }

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
}
```

```java
public class Rectangle extends Shape {
    private double width;
    private double height;

    public Rectangle(double width, double height) {
        super("Obdélník");
        this.width = width;
        this.height = height;
    }

    @Override
    public double getArea() {
        return width * height;
    }
}
```

```java
Shape a = new Circle(2);
Shape b = new Rectangle(3, 4);
a.printArea();   // Kruh má obsah 12.566...
b.printArea();   // Obdélník má obsah 12.0
// Shape c = new Shape("Něco");   // CHYBA – abstraktní třídu nejde vytvořit
```

Proměnná typu `Shape` může obsahovat kruh i obdélník. Která verze `getArea()` se zavolá, se rozhodne podle skutečného objektu. Tomu se říká **polymorfismus**.

### Zadání

#### a) Abstraktní třída `GameCharacter`

> Pozor: třídu nepojmenovávej `Character`, to jméno už v Javě existuje (`java.lang.Character`).

| Co | Název | Popis |
|---|---|---|
| atribut | `name` | jméno postavy |
| atribut | `health` | aktuální počet životů |
| atribut | `maxHealth` | maximální počet životů |
| konstruktor | `GameCharacter(String name, int maxHealth)` | na začátku má postava plné životy |
| gettery | `getName()`, `getHealth()` | |
| metoda | `boolean isAlive()` | vrací `true`, pokud má postava víc než 0 životů |
| metoda | `void takeDamage(int damage)` | ubere životy, ale nikdy ne pod 0 |
| metoda | `void restoreHealth(int amount)` | přidá životy, ale nikdy ne nad `maxHealth` |
| metoda | `void printStatus()` | vypíše např. `Thorin: 85/120 HP` |
| **abstraktní** metoda | `void attack(GameCharacter target)` | každá postava útočí jinak |

#### b) Tři potomci

Každá postava má svou vlastní „mechaniku“:

**`Warrior`** (válečník)
- Má zbraň (`Weapon`) a sílu (`strength`).
- Útok: poškození = poškození zbraně + síla.
- Má brnění (`armor`): přepiš (`@Override`) metodu `takeDamage`. Válečník dostane o `armor` menší poškození, ale vždy alespoň 1. Pak zavolej `super.takeDamage(...)`.
- Výpis: `Thorin útočí mečem na Gandalf a způsobí 20 poškození.`

**`Mage`** (mág)
- Má manu (`mana`, na začátku 100).
- Útok: pokud má alespoň 25 many, sešle **ohnivou kouli** za 30 poškození a spotřebuje 25 many.
- Pokud manu nemá, praští holí za 5 poškození a doplní si 15 many.
- Výpis: `Gandalf sesílá ohnivou kouli na Thorin! (30 poškození)` nebo `Gandalf nemá manu, praští holí... (5 poškození)`.

**`Archer`** (lučištník)
- Má omezený počet šípů (`arrows`, na začátku 5).
- Útok: dokud má šípy, vystřelí za 18 poškození a ubere si jeden šíp.
- Když šípy dojdou, bodne dýkou za 4 poškození.
- Výpis: `Legolas střílí šíp na Thorin. (18 poškození, zbývající šípy: 4)`.

Hodnoty (životy, sílu, brnění…) si můžeš upravit, aby byl souboj vyrovnaný. Výchozí návrh: válečník 120 HP, síla 5, brnění 5; mág 80 HP; lučištník 100 HP.

V `Main` si vytvoř po jedné postavě, nech je na sebe párkrát zaútočit a vypisuj jejich stav.

> **Zamysli se:** Proč je `attack` abstraktní, ale `takeDamage` ne?

---

## Krok 3: Interface – léčitelé

### Výklad

**Interface** popisuje, co objekt **umí**, ne co **je**. Obsahuje jen hlavičky metod (bez těla). Třída, která interface implementuje (`implements`), slibuje, že tyto metody naprogramuje.

Rozdíl oproti abstraktní třídě:

| | Abstraktní třída | Interface |
|---|---|---|
| Vyjadřuje | „**je** něco“ (mág *je* postava) | „**umí** něco“ (mág *umí* léčit) |
| Kolik jich třída může mít | jen **jednu** (`extends`) | **libovolně** (`implements`) |
| Atributy a hotové metody | ano | ne (v základu jen hlavičky metod) |

Díky interface můžou stejnou schopnost mít i třídy, které spolu jinak vůbec nesouvisí.

### Ukázka

```java
public interface Printable {
    void print();
}
```

```java
public class Invoice implements Printable {
    private int amount;

    public Invoice(int amount) {
        this.amount = amount;
    }

    @Override
    public void print() {
        System.out.println("Tisknu fakturu na " + amount + " Kč");
    }
}
```

```java
public class Photo implements Printable {
    @Override
    public void print() {
        System.out.println("Tisknu fotku v barvě");
    }
}
```

```java
Printable[] queue = { new Invoice(1500), new Photo() };
for (Printable item : queue) {
    item.print();
}
```

Faktura a fotka spolu nemají nic společného, a přesto je tiskárna umí obsloužit stejně, protože obě jsou `Printable`.

Třída může zároveň dědit i implementovat: `public class Mage extends GameCharacter implements Healer`.

### Zadání

1. Vytvoř interface **`Healer`** s metodou:
   ```java
   void heal(GameCharacter target);
   ```
2. **`Mage`** bude implementovat `Healer`: léčení stojí 30 many a vrátí cíli 25 životů. Když mág nemá dost many, vypíše `Gandalf nemá dost many na léčení.`
3. Vytvoř novou třídu **`HealingPotion`** (léčivý lektvar), která **není postava**, ale také implementuje `Healer`:
   - atribut `amount` (kolik životů vrátí, např. 40),
   - lektvar jde použít jen **jednou**, při dalším pokusu vypíše `Lahvička je prázdná.`
4. V `Main` vytvoř pole `Healer[]`, do kterého dáš mága i lektvar, a pomocí cyklu jimi vyléč zraněnou postavu.

> **Zamysli se:** Proč by nedávalo smysl, aby `HealingPotion` dědil od `GameCharacter`?

---

## Krok 4: Factory – tvorba postav

### Výklad

Zatím vytváříme postavy přímo pomocí `new Warrior(...)`, `new Mage(...)` a musíme přitom znát všechny jejich parametry (zbraň, sílu, brnění…). Když bychom postavy vytvářeli na deseti místech v programu, bylo by to nepřehledné.

**Factory** (továrna) je třída, která má na starosti **vytváření objektů**. Zbytek programu jí jen řekne, *co* chce, a factory ví, *jak* to vyrobit. Výhody:

- nastavení objektů je na **jednom místě**,
- zbytek programu pracuje jen s rodičovským typem (`GameCharacter`) a nemusí znát konkrétní třídy,
- když přidáš novou postavu, upravíš jen factory.

Často se factory dělá jako třída se **statickou** metodou (`static`), takže ji voláme bez vytvoření objektu: `ShapeFactory.create(...)`.

### Ukázka

```java
public class ShapeFactory {
    public static Shape create(String type, double size) {
        switch (type) {
            case "circle":
                return new Circle(size);
            case "square":
                return new Rectangle(size, size);
            default:
                throw new IllegalArgumentException("Neznámý tvar: " + type);
        }
    }
}
```

```java
Shape s = ShapeFactory.create("square", 5);
s.printArea();   // Obdélník má obsah 25.0
```

`throw new IllegalArgumentException(...)` ukončí program s chybou. Hodí se ve chvíli, kdy někdo zadá nesmysl, se kterým nejde pokračovat.

### Zadání

Vytvoř třídu **`CharacterFactory`** se statickou metodou:

```java
public static GameCharacter create(String type, String name)
```

- `"warrior"` → válečník s mečem (poškození 15), silou 5 a brněním 5,
- `"mage"` → mág,
- `"archer"` → lučištník,
- cokoliv jiného → `IllegalArgumentException` se zprávou `Neznámý typ postavy: ...`.

V `Main` už pak **nepoužívej `new Warrior(...)` apod.**, všechny postavy vytvářej přes factory.

**Bonus:** přidej metodu `createRandom(String name)`, která vytvoří náhodnou postavu (použij `Math.random()`).

---

## Krok 5: Service – organizátor arény

### Výklad

Zatím máme dva druhy tříd:

- **datové třídy (modely)** nesou data a své vlastní chování (`Weapon`, `Mage`, `HealingPotion`),
- **factory** objekty **vytváří** (`CharacterFactory`).

Chybí nám ještě místo pro **logiku procesu**: jak probíhá souboj, kdo kdy útočí, kdy se léčí, kdo vyhrál. Kdybychom to celé psali do `Main`, za chvíli by byl dlouhý a nepřehledný. A kdybychom chtěli souboj spustit víckrát (třeba v turnaji), museli bychom kód kopírovat.

**Service** (služba) je třída, která **řídí nějaký proces** a k tomu **používá ostatní objekty**. Sama data o „světě“ nenese (nemá jméno ani životy), jen ví, *jak se věci dělají*.

| Typ třídy | Co dělá | Příklad |
|---|---|---|
| Datová třída / model | nese data a vlastní chování | `Weapon`, `Mage` |
| Factory | **vytváří** objekty | `CharacterFactory` |
| Service | **řídí proces**, používá ostatní | `BattleService` |

Na rozdíl od naší factory se service obvykle vytváří přes `new`. Může tak mít **vlastní stav**, například statistiky toho, co už udělala.

Pomocné metody, které zvenku nikdo volat nemá, dáváme `private`.

### Ukázka

Služba knihovny, která používá třídu `Book` z kroku 1:

```java
import java.util.ArrayList;
import java.util.List;

public class LibraryService {
    private List<Book> availableBooks = new ArrayList<>();
    private int borrowCount = 0;

    public void addBook(Book book) {
        availableBooks.add(book);
    }

    public Book borrow(String title) {
        Book book = findByTitle(title);
        if (book == null) {
            System.out.println("Kniha '" + title + "' není k dispozici.");
            return null;
        }
        availableBooks.remove(book);
        borrowCount++;
        System.out.println("Půjčena kniha: " + title);
        return book;
    }

    public void printStatistics() {
        System.out.println("Celkem výpůjček: " + borrowCount);
        System.out.println("Knih v knihovně: " + availableBooks.size());
    }

    private Book findByTitle(String title) {
        for (Book book : availableBooks) {
            if (book.getTitle().equals(title)) {
                return book;
            }
        }
        return null;
    }
}
```

```java
LibraryService library = new LibraryService();
library.addBook(new Book("Hobit", 310));
library.addBook(new Book("Harry Potter", 280));

library.borrow("Hobit");        // Půjčena kniha: Hobit
library.borrow("Hobit");        // Kniha 'Hobit' není k dispozici.
library.printStatistics();
```

`Main` jen říká, *co* se má stát. *Jak* se to stane, ví service.

### Zadání

Vytvoř třídu **`BattleService`**:

| Co | Název | Popis |
|---|---|---|
| atribut | `battleCount` | kolik soubojů už proběhlo |
| atribut | `totalRounds` | kolik kol se celkem odehrálo |
| metoda | `GameCharacter fight(GameCharacter first, GameCharacter second, Healer potion)` | odehraje celý souboj a vrátí vítěze |
| `private` metoda | `void printRound(int round, GameCharacter first, GameCharacter second)` | vypíše stav postav na konci kola |
| metoda | `void printStatistics()` | vypíše počet soubojů a průměrný počet kol na souboj |

Pravidla souboje v metodě `fight`:

- souboj běží v cyklu `while`, dokud jsou obě postavy naživu,
- na začátku každého kola vypiš `--- Kolo 1 ---`,
- první postava zaútočí na druhou, a pokud druhá přežila, zaútočí zpět,
- když životy některé postavy klesnou pod 30, použije se na ni lektvar (`potion`). Lektvar je jen jeden, takže druhý pokus vypíše `Lahvička je prázdná.`,
- na konci kola se zavolá `printRound(...)`,
- po skončení souboje se aktualizují statistiky a metoda vrátí vítěze.

> **Zamysli se:** Proč `fight` dostává lektvar jako typ `Healer`, a ne `HealingPotion`? Co bys mohl předat místo něj?

---

## Krok 6: Souboj v aréně

Teď všechno spoj dohromady. `Main` by měl být **krátký**: jen připraví postavy a o zbytek se postará service.

V `Main`:

1. Pomocí `CharacterFactory` vytvoř dvě postavy.
2. Vytvoř `BattleService` a jeden `HealingPotion`.
3. Zavolej `fight(...)` a vypiš vítěze: `Vítězem arény je Thorin!`
4. Vytvoř další dvě postavy a nový lektvar a odehraj druhý souboj **se stejnou** `BattleService`.
5. Vypiš statistiky pomocí `printStatistics()`.

### Ukázka možného výstupu

```
--- Kolo 1 ---
Thorin útočí mečem na Gandalf a způsobí 20 poškození.
Gandalf sesílá ohnivou kouli na Thorin! (30 poškození)
Thorin: 95/120 HP
Gandalf: 60/80 HP
--- Kolo 2 ---
...
Vítězem arény je Thorin!
```

(Přesná čísla se u tebe můžou lišit.)

---

## Bonusové úkoly

1. Přidej čtvrtou postavu (např. `Paladin`), která útočí *a* umí léčit. Kolik míst v kódu jsi musel upravit?
2. Válečník má 20% šanci na **kritický zásah** (dvojnásobné poškození).
3. Udělej turnaj: přidej do `BattleService` metodu `tournament(GameCharacter[] fighters)`, ve které spolu čtyři postavy bojují systémem „každý s každým“ (využij `fight`). Před každým soubojem obnov postavám životy a na konci vypiš, kdo vyhrál nejvíc soubojů.
4. Nech uživatele vybrat typ postavy z klávesnice (`Scanner`) a předej jeho volbu do factory.

## Kontrolní otázky

- Jaký je rozdíl mezi třídou a objektem?
- Proč nejde vytvořit objekt abstraktní třídy?
- Kdy použiješ abstraktní třídu a kdy interface?
- Co by se stalo, kdyby `Archer` nenaprogramoval metodu `attack`?
- Proč je lepší vytvářet postavy přes factory než všude psát `new`?
- Jaký je rozdíl mezi factory a service?
- Proč logika souboje patří do `BattleService`, a ne do `Main` nebo do `GameCharacter`?

---
---

# Zadání 2: Rozvozová služba „Java Express“

*Samostatná práce. Použij stejné principy jako v zadání 1.*

Firma Java Express rozváží zásilky po městě. Má k tomu tři druhy kurýrů a každý funguje jinak: má jiná omezení a jinak počítá cenu. Tvým úkolem je naprogramovat systém, který zásilkám přidělí vhodného kurýra, doručí je a vypíše přehled s cenami.

## Krok 1: Zásilka (třída)

Vytvoř třídu **`Parcel`** (zásilka). Každá zásilka má popis (`description`, např. „Klíče“), hmotnost v kilogramech (`weightKg`) a vzdálenost doručení v kilometrech (`distanceKm`). Přidej konstruktor, gettery a metodu `printInfo()`, která zásilku hezky vypíše.

Všechny zásilky se liší jen daty, takže stačí jedna třída.

## Krok 2: Kurýři (abstraktní třída)

Vytvoř abstraktní třídu **`Courier`**. Každý kurýr má jméno (`name`) a umí:

- `canCarry(Parcel parcel)` – zjistit, jestli zásilku zvládne doručit (**abstraktní**, každý kurýr má jiná omezení),
- `calculatePrice(Parcel parcel)` – spočítat cenu doručení v Kč (**abstraktní**, každý počítá jinak),
- `deliver(Parcel parcel)` – doručit zásilku (**společná** metoda pro všechny). Pokud kurýr zásilku nezvládne, vypíše to. Jinak vypíše, kdo co doručil a za kolik, např. `Dron Bzz doručil zásilku 'Klíče' za 120 Kč.`

Vytvoř tři potomky:

| Kurýr | Omezení | Cena |
|---|---|---|
| **`BikeCourier`** (cyklokurýr) | max. 5 kg a max. 10 km | 10 Kč za každý km |
| **`CarCourier`** (auto) | max. 100 kg, vzdálenost neomezená | 80 Kč základ + 15 Kč za každý km |
| **`DroneCourier`** (dron) | max. 2 kg a max. 15 km | 100 Kč paušál + 5 Kč za každý km |

## Krok 3: Elektrický pohon (interface)

Některé dopravní prostředky jezdí na baterii. Vytvoř interface **`Rechargeable`** s metodami pro zjištění stavu baterie (`getBatteryLevel()`, v procentech) a pro nabití (`recharge()`, nabije na 100 %).

- **`DroneCourier`** a **`BikeCourier`** (elektrokolo) implementují `Rechargeable`, **`CarCourier`** ne.
- Každé doručení vybije baterii (např. dron 2 % za km, elektrokolo 1 % za km).
- Pokud by baterie na doručení nestačila, kurýr zásilku nezvládne (zohledni to v `canCarry`).
- Vymysli ještě jednu třídu, která **není kurýr**, ale dává smysl, aby byla `Rechargeable` (např. terminál pro platbu kartou, který si kurýři berou s sebou).

V `Main` vytvoř pole `Rechargeable[]` a nabij v cyklu všechna zařízení najednou.

## Krok 4: Dispečink (factory)

Vytvoř třídu **`CourierFactory`** se statickou metodou `createFor(Parcel parcel)`, která podle zásilky **sama vybere** vhodného kurýra:

- lehká zásilka na krátkou vzdálenost → nejlevnější vhodná možnost,
- když to nejde → další vhodná možnost,
- zásilka, kterou neuveze nikdo (např. přes 100 kg) → `IllegalArgumentException` s českou zprávou.

Pravidla výběru si navrhni sám, ale musí být rozumná (nesmí např. poslat na 30 km kolo). Zbytek programu nesmí obsahovat `new BikeCourier(...)` apod., kurýry vytváří jen dispečink.

## Krok 5: Služba pracovního dne (service)

Vytvoř třídu **`DeliveryService`**, která řídí celý pracovní den. Sama kurýra nevybírá (to dělá `CourierFactory`), ale stará se o průběh doručování a vede si statistiky.

Service bude mít:

- **atributy se statistikami**: počet doručených zásilek, počet nedoručených zásilek, celková tržba a počet zásilek doručených jednotlivými typy kurýrů,
- `deliver(Parcel parcel)`: pomocí `CourierFactory` získá kurýra, zásilku doručí a aktualizuje statistiky. Zásilku, kterou nejde doručit, ohlásí, ale program kvůli ní nesmí spadnout (`try` / `catch`),
- `deliverAll(List<Parcel> parcels)`: doručí celý seznam zásilek (využij `deliver`),
- `printSummary()`: vypíše souhrn dne (doručeno, nedoručeno, tržba, počty podle typu kurýra).

Pomocné metody, které zvenku nikdo nepotřebuje, udělej `private`.

## Krok 6: Pracovní den

`Main` musí být **krátký**, veškerá logika patří do service.

V `Main`:

1. Vytvoř seznam aspoň šesti různých zásilek (lehké, těžké, blízko, daleko, aspoň jednu, kterou nejde doručit).
2. Vytvoř `DeliveryService` a předej jí seznam zásilek.
3. Vypiš souhrn dne.

## Bonusové úkoly

1. Přidej do `DeliveryService` metodu, která vypíše **nejdražší** doručenou zásilku dne.
2. Přidej **expresní doručení**: zásilka má navíc příznak `express`. Expresní zásilky jsou o 50 % dražší a dispečink pro ně preferuje dron.
3. Přidej nového kurýra (např. `CargoBikeCourier`, nákladní kolo do 40 kg a 15 km). Kolik tříd jsi musel upravit?
4. Factory si pamatuje kurýry, které už vytvořila, a když už má kurýra správného typu, použije ho znovu, místo aby vytvořila nového.
