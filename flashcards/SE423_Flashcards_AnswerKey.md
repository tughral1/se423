# SE423 Midterm Flashcards — Answer Key

20 scenario cards covering Labs 1–6, tagged by CLO. Read the question, commit to an answer, then read the explanation.

## Card 1 — Refactoring  (Lab 01, CLO-2)

A teammate changes calculateAverage(int math, int programming, int database) to calculateAverage(int[] marks) and moves it out of main() into a ResultCalculator class. The program prints exactly the same output as before. In SE423 terms, what did the teammate do?

- **A.** Refactoring: improved the internal structure without changing external behavior
- **B.** Performance optimization, because arrays are faster than three ints
- **C.** Added a new feature, because the method now accepts any number of subjects
- **D.** Debugging, because the hard-coded / 3.0 was a defect

**Answer: A**

**Feynman says:** Refactoring is tidying the kitchen without changing the menu. Same input, same output, better insides. Lab 1's definition: improving the internal structure of existing code without changing its external behavior. Before: Input → Code → Output. After: Input → Improved Code → Same Output.

**One-line test:** Did the output change? No. Did the structure improve? Yes. That is refactoring.

**Why the others are wrong:**
- B Nothing was measured and speed was never the goal. Refactoring targets readability and changeability.

- C Accepting any number of subjects is a side effect of removing hard-coding; no new requirement was implemented and the output is identical.

- D / 3.0 produced the correct answer. It was a maintainability smell (Craftsmanship Principle 7, don't hard-code), not a bug.

**Memory hook:** Refactor = same menu, cleaner kitchen.

**Copilot prompt for this situation:**

```text
Refactor this code without changing its external behavior. List each problem (naming, duplication, hard-coding, giant main, mixed responsibilities), the clean-code principle applied, and how it improved readability or maintainability. Keep the console output identical.
```

---

## Card 2 — Encapsulation vs Information hiding  (Lab 02, CLO-1)

FoodItem stores private double price and its constructor throws IllegalArgumentException when price < 0. Later the team changes storage to private int priceInCents but keeps getPrice() returning a double, so no caller changes. Which concept does the second change (storage swap with zero caller impact) demonstrate most precisely?

- **A.** Functional cohesion: the class has one responsibility
- **B.** Encapsulation: the field is private and validated
- **C.** Loose coupling: callers depend on an interface, not a class
- **D.** Information hiding: the internal representation changed behind a stable public method

**Answer: D**

**Feynman says:** Encapsulation is the locked box: private fields, validation at the door. Information hiding goes one step further: people outside don't even know what is in the box, so you can swap the contents and nobody notices. Lab 2 says it literally: "Encapsulation protects the object's state. Information hiding goes one step further."

Today private double price, tomorrow private int priceInCents, and the public interface getPrice() never moves.

**One-line test:** Private and validated? Encapsulation. Can I change the inside without any caller changing? Information hiding.

**Why the others are wrong:**
- A Cohesion is about which responsibilities live together in a class, not about hiding representation.

- B describes the first change (private field + validation), not the storage swap.

- C There is no interface here, only a class with a stable method. Coupling is about dependencies between classes.

**Memory hook:** Encapsulation locks the box. Information hiding makes the box opaque.

**Copilot prompt for this situation:**

```text
Apply encapsulation (private fields, constructor validation with IllegalArgumentException) and information hiding (stable public methods so the internal representation can change later) to this class. Explain where each one was applied and why.
```

---

## Card 3 — Cohesion  (Lab 02, CLO-2)

```java
class FoodSystem {
  void addItem() { ... }
  double calculateTotal() { ... }
  void sendEmail() { ... }
  void saveToDatabase() { ... }
  void printReceipt() { ... }
  void calculateDeliveryDistance() { ... }
}
```
Which kind of cohesion does this class show, and what is the fix?

- **A.** Tight coupling; introduce a PaymentMethod interface
- **B.** Information-hiding violation; make the methods private
- **C.** Functional cohesion; keep it, all methods are about food
- **D.** Coincidental cohesion; split into Order, EmailService, OrderRepository, Receipt, DeliveryCalculator

**Answer: D**

**Feynman says:** Cohesion asks: do the things inside this class belong together? addItem and calculateTotal are both about one order. Email, database, printing, and distance are unrelated jobs that happened to land in the same file, like strangers sharing a bus. That is coincidental cohesion. The fix is one class per job, each with functional cohesion. This is the same smell SRP names from the outside in Lab 3.

**One-line test:** Can I describe the class in one sentence without the word "and"? No → coincidental.

**Why the others are wrong:**
- A Coupling is about dependencies between classes; this smell is inside one class.

- B Making methods private hides them but the unrelated responsibilities are still there.

- C "About food" is a theme, not a responsibility. The lab's own example calls exactly this class coincidental.

**Memory hook:** Functional = a team. Coincidental = strangers on a bus.

**Copilot prompt for this situation:**

```text
Identify coincidental cohesion in this class and refactor it into classes with functional cohesion. For each new class state its single responsibility in one line, and show that main still produces the same output.
```

---

## Card 4 — Coupling and injection  (Lab 02, CLO-2)

```java
class OrderService {
  private CreditCardPayment payment;
  public OrderService() {
    payment = new CreditCardPayment();
  }
  void checkout(Order o) { payment.pay(o.calculateTotal()); }
}
```
Management now wants cash and mobile-wallet payments too. Which change fixes the real problem?

- **A.** Depend on a PaymentMethod interface and inject it through the constructor; OrderService never changes again
- **B.** Make OrderService extend CreditCardPayment to reuse pay()
- **C.** Add if (type.equals("cash")) branches inside checkout()
- **D.** Make CreditCardPayment a Singleton so it is shared

**Answer: A**

**Feynman says:** OrderService is glued to CreditCardPayment because it writes new CreditCardPayment() itself. Glue is tight coupling. Replace the glue with a socket (an interface) and let main plug in whichever payment it likes: new OrderService(new CashPayment()). Lab 2 calls this loose coupling; Lab 3 calls the same move DIP.

**One-line test:** Does the service say new ConcreteThing()? Tightly coupled. Does it receive an interface in its constructor? Loosely coupled.

**Why the others are wrong:**
- B Inheritance for reuse still hard-wires one payment type and makes OrderService "a kind of" payment, which is nonsense.

- C creates an if-else chain that grows with every payment type (the OCP violation of Lab 3).

- D Singleton answers "how many instances?", not "which implementation?".

**Memory hook:** `new` inside a service is glue. Constructor injection is a socket.

**Copilot prompt for this situation:**

```text
Replace tight coupling with loose coupling: introduce an interface for the collaborator and inject it via the constructor. Show main wiring two different implementations without modifying the service, and explain the stable interface.
```

---

## Card 5 — SRP  (Lab 03, CLO-2)

Order has createOrder(), calculateTotal(), saveToDatabase(), sendEmail(), printReceipt(). The lab notes: "Database changes → Order must change. Email service changes → Order must change. Receipt format changes → Order must change." Which SOLID principle is violated?

- **A.** ISP: the class implements too many interfaces
- **B.** SRP: the class has several reasons to change
- **C.** DIP: the class depends on a database
- **D.** OCP: the class cannot be extended

**Answer: B**

**Feynman says:** SRP's real definition is one reason to change. Count the reasons here: database, email, receipt format, order rules. Four reasons, one class. Each reason should live in its own class so a change in one cannot break the others. Lab 3's fix: Order, OrderRepository, EmailService, ReceiptPrinter.

**One-line test:** List the kinds of people who could ask for a change to this class. More than one kind → SRP.

**Why the others are wrong:**
- A No interfaces are involved at all.

- C DIP would need a concrete low-level class instantiated inside; the quoted symptom is "reasons to change".

- D OCP is about adding new behavior without editing existing code. The symptom here is many unrelated behaviors, not blocked extension.

**Memory hook:** SRP = one reason to change, one boss to answer to.

**Copilot prompt for this situation:**

```text
Check SRP: list every reason this class could change, then move each reason into its own class named by its job. Explain where SRP was violated, what changed, and how it improves maintainability.
```

---

## Card 6 — OCP  (Lab 03, CLO-2)

```java
public void pay(String method, double amount) {
  if (method.equals("CARD")) { ... }
  else if (method.equals("PAYPAL")) { ... }
  else if (method.equals("CASH")) { ... }
}
```
Adding JazzCash and EasyPaisa means editing this method again. Which principle is violated and what is the lab's fix?

- **A.** OCP; create a PaymentMethod interface with one class per method and make PaymentService call paymentMethod.pay(amount)
- **B.** LSP; make JazzCashPayment extend CashPayment
- **C.** SRP; move the if-else into a separate PaymentRouter class
- **D.** ISP; split pay() into payCard(), payPayPal(), payCash()

**Answer: A**

**Feynman says:** Open for extension, closed for modification. You should add a payment type by writing a new class, never by reopening a working method. An if-else chain is the opposite: every new type reopens it. Swap the chain for an interface, and "adding" becomes "creating a class". EasyPaisaPayment implements PaymentMethod and PaymentService is untouched.

**One-line test:** To add a new variant, do I edit an existing method? Yes → OCP violation.

**Why the others are wrong:**
- B Payment types are not kinds of each other, and LSP is about substitutability, not extension.

- C Moving the chain to another class just relocates the violation.

- D More methods on one interface makes it fatter (the reverse of ISP) and still needs an edit per type.

**Memory hook:** OCP: add a class, don't reopen a method.

**Copilot prompt for this situation:**

```text
Refactor this if-else chain for OCP: interface + one class per branch, the service depends only on the interface. Prove it by adding a brand-new implementation and showing the service file is unchanged.
```

---

## Card 7 — LSP vs ISP  (Lab 03, CLO-2)

Two snippets from Lab 3.
(1) class Drone extends DeliveryVehicle overrides deliverOrder() to throw new UnsupportedOperationException().
(2) class Kitchen implements RestaurantService must implement deliverFood(), processPayment(), printReceipt(), each throwing UnsupportedOperationException.
Which principles do (1) and (2) violate, respectively?

- **A.** Both LSP
- **B.** (1) LSP, (2) ISP
- **C.** (1) ISP, (2) LSP
- **D.** Both OCP

**Answer: B**

**Feynman says:** Same exception, two different crimes.
(1) A subclass breaks a promise its parent made: "every DeliveryVehicle can deliver". Substitute a Drone where a DeliveryVehicle is expected and the program explodes. That is Liskov. Fix: a narrower Deliverable interface implemented only by classes that really deliver.
(2) Kitchen breaks no promise; it was forced to sign a contract with clauses it never needed. A fat interface. That is Interface Segregation. Fix: split into FoodPreparation, FoodDelivery, PaymentProcessing.

**One-line test:** A child class betraying its parent's contract → LSP. A class forced to implement methods it doesn't need → ISP.

**Why the others are wrong:**
- A Kitchen has no parent whose behavior it breaks; the problem is the interface's size.

- C swaps them.

- D OCP is about extension requiring modification; neither snippet is about adding variants.

**Memory hook:** LSP = broken promise by a child. ISP = forced signature on a fat contract.

**Copilot prompt for this situation:**

```text
For each UnsupportedOperationException in this code decide: LSP violation (subclass breaks the parent contract → replace inheritance with a narrower interface) or ISP violation (fat interface → split into role interfaces). Refactor and explain each decision.
```

---

## Card 8 — DIP  (Lab 03, CLO-2)

```java
class OrderService {
  private MySQLOrderRepository repository;
  public OrderService() {
    repository = new MySQLOrderRepository();
  }
}
```
The team is migrating to MongoDB. Which principle is violated, and what does the refactored constructor look like?

- **A.** ISP; give OrderService two constructors, one per database
- **B.** DIP; public OrderService(OrderRepository repository) with an OrderRepository interface implemented by MySQL and Mongo classes
- **C.** SRP; move the new into a static factory method on OrderService
- **D.** LSP; make MongoOrderRepository extend MySQLOrderRepository

**Answer: B**

**Feynman says:** High-level policy (placing orders) must not depend on low-level detail (MySQL). Both depend on an abstraction, OrderRepository. The "inversion" is that the detail now depends on the interface too. Deliver the dependency through the constructor so main decides which database, not the service. Lab 3 calls the pay-off "the practical benefit of OCP + DIP".

**One-line test:** Does a high-level class name a concrete low-level class? DIP. Does it construct it itself? Even worse.

**Why the others are wrong:**
- A Two constructors double the coupling.

- C A factory on the service still ties it to concrete types and still changes per database.

- D Mongo is not a kind of MySQL; inheritance here is wrong and LSP is unrelated.

**Memory hook:** DIP = plug into the socket (interface), never solder the wire (new).

**Copilot prompt for this situation:**

```text
Apply DIP: introduce an interface for the low-level dependency, make the high-level class take it in its constructor, and show main injecting two different implementations. Note that this also satisfies OCP.
```

---

## Card 9 — Factory Method  (Lab 04, CLO-1)

Ride Booking app: users choose Bike, Car, or Van. "The application should create the appropriate ride object based on the user's selection. The main program should not need to know how each type of ride is created." Which creational pattern?

- **A.** Abstract Factory: one factory per ride producing a family of related products
- **B.** Factory Method: RideFactory.createRide(String type) returns the Ride interface
- **C.** Builder: construct the ride step by step
- **D.** Strategy: swap the ride algorithm at runtime

**Answer: B**

**Feynman says:** new with a brain. main says "give me a ride for 'van'" and gets back something that implements Ride, never seeing new VanRide(). The type-string if-else lives in exactly one place, the factory, and throws IllegalArgumentException for unknown types.

**One-line test:** Does the method RETURN the interface type and then stop caring? Factory Method.

**Why the others are wrong:**
- A Abstract Factory is for families of compatible products (payment + notification together). Here there is one product kind.

- C Builder is for many optional attributes, not for choosing a subtype.

- D Strategy is behavioral: it uses an algorithm. The scenario is about creating an object.

**Memory hook:** Factory Method = vending machine: press a code, get a product, never see the inside.

**Copilot prompt for this situation:**

```text
Implement Factory Method: Product interface, concrete products, a factory with static create(String type) that throws IllegalArgumentException on unknown type, and a main that never writes new of a concrete class. Justify the choice in two sentences and name the rejected alternative.
```

---

## Card 10 — Abstract Factory  (Lab 04, CLO-1)

Online orders must use CreditCardPayment + EmailNotification. Local orders must use CashPayment + SMSNotification. The pair must always match and the app should switch between the two sets as a whole. Which pattern?

- **A.** Abstract Factory: DeliveryServiceFactory with createPayment() and createNotification(), implemented by OnlineOrderFactory and LocalOrderFactory
- **B.** Facade: a placeOrder() that calls both services
- **C.** Builder: build an order with payment and notification set step by step
- **D.** Factory Method: one createPayment(String) and one createNotification(String)

**Answer: A**

**Feynman says:** Matching furniture sets. You don't buy a chair and a table separately and hope they match; you buy the "Online set" or the "Local set" and everything inside is guaranteed compatible. One factory interface, several concrete factories, each producing the whole family. Switching families is one line in main.

**One-line test:** Multiple product KINDS that must be compatible with each other? Abstract Factory. One kind? Factory Method.

**Why the others are wrong:**
- B Facade simplifies calls to subsystems; it doesn't create them.

- C Builder assembles one object with optional parts; it doesn't enforce families.

- D Two independent string factories cannot stop someone pairing CashPayment with EmailNotification.

**Memory hook:** Factory Method = one product. Abstract Factory = matching set.

**Copilot prompt for this situation:**

```text
Implement Abstract Factory: product interfaces, concrete products, an abstract factory interface with one create method per product kind, and one concrete factory per family. Demo switching families by changing one line in main.
```

---

## Card 11 — Builder  (Lab 04, CLO-1)

An order has customer name, product, quantity, delivery address, discount, payment method, special instructions. Most are optional. "The application should allow an order to be constructed step by step without using a constructor with many parameters." Which pattern?

- **A.** Abstract Factory: one factory per order type
- **B.** Builder: static nested Builder with fluent setters and build()
- **C.** Prototype: clone a default order and change fields
- **D.** Singleton: one shared Order object

**Answer: B**

**Feynman says:** A sandwich counter. Say the mandatory bits first (the Builder's constructor takes them), add whatever toppings you want in any order (.setAddress(...).setDiscount(...) each returning this), then say build() and get the finished product. No seven-parameter constructor where you forget which null is which. The product's constructor is private and takes the builder.

**One-line test:** Many optional attributes and readable step-by-step construction? Builder.

**Why the others are wrong:**
- A No families of related products here.

- C Prototype copies an existing configured object; it doesn't help you assemble a brand-new one readably.

- D Orders are many, not one.

**Memory hook:** Builder = sandwich counter: pick, pick, pick, build().

**Copilot prompt for this situation:**

```text
Implement Builder: private product constructor taking the builder, static nested Builder with required fields in its constructor, fluent optional setters returning this, and build(). Demo two orders using different optional fields.
```

---

## Card 12 — Prototype vs Singleton  (Lab 04, CLO-1)

A document app has configured Invoice, Report and Certificate templates. "A user should be able to create a new document by copying an existing configured document and then modifying details such as the customer's name, date, or document number." Which pattern?

- **A.** Factory Method: createDocument("invoice") returning a fresh empty document
- **B.** Singleton: one shared Invoice instance that everyone edits
- **C.** Prototype: templates implement Cloneable; clone() then set the changed fields
- **D.** Builder: rebuild every document from scratch

**Answer: C**

**Feynman says:** Photocopier. The template already has 90% of the setup; copy it and change the name. Creating from scratch is wasteful when a configured original exists. In Java: implements Cloneable, override clone() returning the concrete type, wrap CloneNotSupportedException in a RuntimeException. The original is untouched after you edit the copy.

**One-line test:** Is there an existing configured object I want many near-copies of? Prototype.

**Why the others are wrong:**
- A A factory returns an unconfigured document; the scenario says "copying an existing configured document".

- B Singleton means exactly ONE instance. Editing it would corrupt the template for everyone, which is the "global state" pitfall. Singleton fits the University configuration scenario instead. Its five pitfalls: global state, hard to test, hidden dependencies, thread-safety of if (instance == null), overuse.

- D Rebuilding from scratch throws away the configured template.

**Memory hook:** Prototype = photocopier. Singleton = the one thermostat.

**Copilot prompt for this situation:**

```text
Implement Prototype with Cloneable and a typed clone(). Demo cloning a template, changing two fields, and printing both to prove the original is untouched. If the scenario is shared configuration, implement Singleton instead, make getInstance thread-safe, and list the five pitfalls.
```

---

## Card 13 — Adapter  (Lab 05, CLO-1)

The food app calls PaymentProcessor.pay(double amount). A new provider ships class MobileWallet { void sendMoney(double amount) }. "Instead of changing our existing system or the third-party service, we create ... between them." Which pattern?

- **A.** Proxy: control access to MobileWallet
- **B.** Facade: a simpler interface over the wallet
- **C.** Adapter: MobileWalletAdapter implements PaymentProcessor and calls wallet.sendMoney(amount) inside pay()
- **D.** Decorator: wrap MobileWallet to add pay()

**Answer: C**

**Feynman says:** Plug converter. Your charger (the app) has UK pins; the wall socket (MobileWallet) is European. You don't rewire either; you put a converter between them that has the app's shape on one side and the wallet's on the other. The adapter implements the target, holds the adaptee, and translates one call into the other.

**One-line test:** Right behavior, wrong method name, and you can't change either side? Adapter.

**Why the others are wrong:**
- A Proxy has the same interface as the real object and gates access; no translation.

- B Facade invents a new simple interface over several subsystems. Here there is one class and the target interface already exists.

- D Decorator adds responsibilities to the same interface; it doesn't translate between two different ones.

**Memory hook:** Adapter = plug converter. Same electricity, different pins.

**Copilot prompt for this situation:**

```text
Implement Adapter: target interface, adaptee class left unchanged, adapter implementing the target and holding the adaptee, translating the call. Demo the client using the adapter only through the target interface.
```

---

## Card 14 — Decorator vs Proxy  (Lab 05, CLO-1)

Customers order a Burger and can add Cheese, Extra Sauce, Fries, Drink in any combination. The lab warns against classes like CheeseSauceFriesBurger. Which pattern, and what separates it from Proxy, which looks identical in code?

- **A.** Composite: a burger is a tree of toppings
- **B.** Builder: build the burger with optional toppings
- **C.** Proxy: each topping is a proxy to the burger
- **D.** Decorator: each wrapper implements Food, holds a Food, and adds to getDescription()/getPrice(); Proxy wraps the same way but controls access instead of adding features

**Answer: D**

**Feynman says:** Toppings. Wrap the burger in cheese, wrap that in sauce, wrap that in fries; each layer asks the inner layer for its price and adds its own. Any combination, zero class explosion.

Proxy has the identical wrapper shape but a different job: it's the bouncer. It checks authorization, creates the expensive real service lazily (if (realService == null)), and can say "Access denied." Decorator adds, Proxy gates.

**One-line test:** Does the wrapper add behavior and stack with others? Decorator. Does it decide whether or when the real call happens? Proxy.

**Why the others are wrong:**
- A Composite is for hierarchies of groups and leaves treated uniformly (menus and categories), not for layering extras onto one item.

- B Builder produces a fixed Burger; you couldn't add a topping to an already-built one at runtime, and price composition is lost.

- C Proxies don't add cheese; they control access.

**Memory hook:** Decorator = toppings. Proxy = bouncer. Same coat, different job.

**Copilot prompt for this situation:**

```text
Implement Decorator: component interface, concrete component, abstract decorator holding the component, concrete decorators adding to description and price. Demo a stacked order printing description and total. If the requirement is authorization or lazy creation, implement Proxy with the same interface instead.
```

---

## Card 15 — Facade  (Lab 05, CLO-1)

Placing an order needs Restaurant.prepareFood(), PaymentService.processPayment(), DeliveryService.arrangeDelivery(), NotificationService.sendConfirmation(), and now TrackingService.startTracking(), in that order. The client should call one method. Which pattern?

- **A.** Facade: FoodDeliveryFacade.placeOrder() that owns the subsystems and calls them in sequence
- **B.** Template Method: an abstract placeOrder() with each step overridable
- **C.** Adapter: convert the five interfaces into one
- **D.** Command: one command object per subsystem call

**Answer: A**

**Feynman says:** Hotel concierge. You say "book me dinner and a taxi" and they call the restaurant, the taxi company, and reception. You never learn their phone numbers. The facade doesn't hide the subsystems from existence; it hides them from the client. Adding TrackingService means adding one field and one line inside the facade; the client still calls placeOrder().

**One-line test:** Several subsystems, client wants one simple entry point? Facade.

**Why the others are wrong:**
- B Template Method varies steps across subclasses; here nothing varies, the client just wants simplicity.

- C Adapter translates ONE incompatible interface; it doesn't aggregate five.

- D Command would encapsulate each call as an object for queuing or undo; not the stated need.

**Memory hook:** Facade = concierge: one request, many phone calls you never see.

**Copilot prompt for this situation:**

```text
Implement Facade: subsystem classes unchanged, a facade that instantiates them and exposes one placeOrder() calling them in order. Add TrackingService inside the facade so tracking starts automatically, and show the client code is one line.
```

---

## Card 16 — Composite  (Lab 05, CLO-1)

A menu contains categories (Pakistani, Fast Food, Desserts), each containing items (Biryani, Burger, Cake); categories could contain sub-categories. "Both individual food items and groups of food items can be treated in the same way" via show(). Which pattern?

- **A.** Composite: MenuComponent interface; FoodItem is a leaf; MenuCategory holds a List<MenuComponent> and show() loops over children
- **B.** Decorator: categories decorate items
- **C.** Facade: Menu.showAll()
- **D.** Iterator: RestaurantMenu implements Iterable<MenuItem>

**Answer: A**

**Feynman says:** Folders inside folders. A folder and a file both answer "show yourself"; a folder answers by asking everything inside it to show itself. The client calls show() on the root and the whole tree prints itself, never caring whether a node is a leaf or a branch.

**One-line test:** Tree structure where a group and a single item share one interface? Composite.

**Why the others are wrong:**
- B Decorator wraps one object to add features; it doesn't hold a list of children.

- C Facade simplifies subsystems; it doesn't model a hierarchy.

- D Iterator traverses a flat collection without exposing its structure (Lab 6 uses it for a flat RestaurantMenu). It has no notion of nested groups.

**Memory hook:** Composite = folders in folders. Iterator = a bookmark through one flat list.

**Copilot prompt for this situation:**

```text
Implement Composite: component interface with show(), leaf class, composite class with add() and a loop calling show() on children. Build the exact hierarchy from the task and print it with indentation.
```

---

## Card 17 — Observer  (Lab 06, CLO-1)

When an order's status changes to "Preparing" or "Out for delivery", the Customer, the DeliveryRider, and now the RestaurantManager must all be told; a removed party must stop receiving updates. Which pattern?

- **A.** Command: one NotifyCommand per party
- **B.** Observer: Order keeps a List<OrderObserver>, addObserver/removeObserver, and setStatus() loops calling update(status)
- **C.** Strategy: a NotificationStrategy selected at runtime
- **D.** Mediator: a central class that routes messages

**Answer: B**

**Feynman says:** YouTube subscriptions. The channel (Order) doesn't know your name; it pings everyone on the list when it uploads. Subscribe = addObserver. Unsubscribe = removeObserver. The hole holds a list, not one object. That is the giveaway versus Strategy, which holds exactly one.

**One-line test:** One thing changes and MANY need to know, and the list can change at runtime? Observer.

**Why the others are wrong:**
- A Command encapsulates a request for queuing or undo; it has no subscription list.

- C Strategy holds ONE algorithm chosen by the caller; it doesn't fan out to many.

- D Mediator isn't in the syllabus and centralizes all communication; the scenario is one subject, many listeners.

**Memory hook:** Observer = YouTube subscribe. Many in the hole.

**Copilot prompt for this situation:**

```text
Implement Observer with addObserver, removeObserver and a notify loop. Demo: register three observers, change status, remove one, change status again, and show the removed observer prints nothing.
```

---

## Card 18 — State vs Strategy  (Lab 06, CLO-1)

```java
class PlacedState implements OrderState {
  public void next(OrderContext order) {
    order.setState(new PreparingState());
  }
}
```
versus
```java
service.setStrategy(new ExpressDelivery());
```
Both have an interface, several implementations, and a context with a field. What single fact tells you the first is State and the second is Strategy?

- **A.** In State the plugged-in object replaces itself (states know each other and call setState); in Strategy the outside caller swaps it and strategies never reference each other
- **B.** State uses an abstract class, Strategy uses an interface
- **C.** Strategy methods return a value, State methods are void
- **D.** State has more than two implementations

**Answer: A**

**Feynman says:** Traffic light vs GPS. Press "next" on green and the light decides it becomes yellow; nobody outside chooses. That is State: the mood picks the next mood. The GPS never changes its own route preference; you pick fastest or shortest from outside. That is Strategy. Identical code shape, different "who swaps it". The fingerprint is order.setState(new ...) written inside a state class.

**One-line test:** Who calls setState/setStrategy? A state class itself → State. The caller or main → Strategy.

**Why the others are wrong:**
- B Both can use either; the lab uses interfaces for both.

- C The lab's State getStatus() returns a String; return types don't define patterns.

- D The count is irrelevant.

**Memory hook:** State = traffic light (changes itself). Strategy = GPS (you change it).

**Copilot prompt for this situation:**

```text
Implement State where each state class performs its own transition via context.setState(...) and the terminal state refuses with a message. For the practice task, insert ReadyForPickupState by changing only PreparingState.next() and adding the new class.
```

---

## Card 19 — Command vs Template Method  (Lab 06, CLO-1)

Restaurant staff can Accept, Cancel, and now Prepare orders. Each operation must be an object the RestaurantController executes without containing any order-specific logic, so operations can later be queued or undone. Separately: every payment runs validateOrder → makePayment → generateReceipt with only makePayment varying. Which two patterns, respectively?

- **A.** Strategy for the operations; Command for the payment sequence
- **B.** Template Method for both
- **C.** Command for the operations; Template Method for the payment sequence
- **D.** Command for both

**Answer: C**

**Feynman says:** Order ticket vs boarding procedure. A ticket (Command) carries the whole request, including which kitchen (the receiver, passed in the constructor) it's for; the rail (invoker) just runs tickets and can hold, log, or tear them up. The boarding procedure (Template Method) is a fixed sequence in a final method where one step is a blank the subclass fills via protected abstract makePayment().

**One-line test:** Queue/log/undo and a dumb invoker → Command. Fixed sequence with one varying step via inheritance → Template Method.

**Why the others are wrong:**
- A Strategy is several WAYS of doing ONE job; Accept and Cancel are different jobs. And Command isn't a sequence skeleton.

- B Template Method can't give you queueable request objects.

- D Command doesn't enforce a fixed order of steps.

**Memory hook:** Command = order ticket on the rail. Template = recipe with one blank.

**Copilot prompt for this situation:**

```text
Implement Command with a receiver, concrete commands holding the receiver, and an invoker with executeCommand(Command). Add PrepareOrderCommand without touching the controller. Separately implement Template Method with a final processPayment() calling private fixed steps and a protected abstract makePayment().
```

---

## Card 20 — Factory vs Strategy  (Lab 04 + 06, CLO-1)

```java
Delivery d = DeliveryFactory.createDelivery("drone");
d.deliver();
```
versus
```java
service.setStrategy(new ExpressDelivery());
service.getFee(5);
```
Both have an interface and several implementations chosen at runtime. Which statement correctly separates Factory Method from Strategy?

- **A.** They are the same pattern under two names
- **B.** Factory is behavioral, Strategy is creational
- **C.** Factory RETURNS the object and is done (creation, called once); Strategy is HELD by a context and CALLED to produce a result (behavior, called every time). A factory often creates the strategy a context then uses
- **D.** Strategy always uses a static method; Factory never does

**Answer: C**

**Feynman says:** Birth vs behaviour. The factory is the maternity ward: it hands you a Delivery and never sees it again. The strategy is the employee: the service keeps it on staff and asks it to compute a fee every time an order arrives.

Code tell: a factory method's return type is the interface. A strategy shows up as a field or parameter in the class that uses it, and its method returns an answer. They're a team: DeliveryStrategyFactory.create("express") picks the brain, DeliveryService uses the brain.

**One-line test:** Does the method return the interface? Factory. Does a field hold the interface and call it for a result? Strategy.

**Why the others are wrong:**
- A Same code shape, opposite intent and opposite category.

- B Reversed. Factory Method is creational; Strategy is behavioral.

- D Static-ness is incidental; the lab's factory happens to be static, but that isn't the definition.

**Memory hook:** Factory = birth. Strategy = behaviour. Factory picks the brain, Strategy uses the brain.

**Copilot prompt for this situation:**

```text
When both appear in a scenario, implement a Factory that returns the Strategy from a type string, and a context that holds and uses the Strategy. State explicitly which class creates and which class uses, and which pattern category each belongs to.
```

---
