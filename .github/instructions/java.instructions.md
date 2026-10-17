---
applyTo: "**/*.java"
---

# Java rules for SE423 lab code

- One file per task unless told otherwise. Exactly one `public class Main` with `public static void main(String[] args)`. All other classes and interfaces are package-private in the same file. No `package` declaration.
- Imports only from `java.util` (`ArrayList`, `LinkedList`, `List`, `Iterator`, `Objects`). No third-party libraries.
- Fields are `private` (and `final` when never reassigned). Validate constructor arguments with `IllegalArgumentException` and a clear message.
- Depend on interfaces; inject collaborators through the constructor. Never instantiate a concrete repository, payment, or notification class inside a service.
- No `if/else` or `switch` on a type string outside a Factory `create` method.
- Name classes by role and lab convention: `XxxStrategy`/`StandardXxx`, `XxxObserver`, `XxxCommand`, `XxxState`, `XxxFactory`, `XxxAdapter`, `XxxDecorator`, `XxxFacade`, `XxxProxy`, `XxxRepository`, `XxxService`, `Builder` (static nested).
- Mark pattern roles with one-line comments: `// Product`, `// ConcreteProduct`, `// Creator`, `// Subject`, `// Observer`, `// Receiver`, `// Command`, `// Invoker`, `// Context`, `// State`, `// Component`, `// Leaf`, `// Composite`, `// Target`, `// Adaptee`, `// Adapter`, `// RealSubject`, `// Proxy`.
- `System.out.println` only for demo output; keep messages identical in style to the labs ("Order accepted.", "Payment processed by card.", "Delivery tracking started.").
- Template Method: `public final void template()` calling `private` fixed steps and `protected abstract` varying steps.
- Keep methods under ~15 lines and classes focused on one responsibility.
