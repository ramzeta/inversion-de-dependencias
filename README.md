# Principio de Inversión de Dependencias (Dependency Inversion Principle - DIP)

## 📌 ¿Qué es el DIP?
El Principio de Inversión de Dependencias (Dependency Inversion Principle) es la "D" de SOLID. Su objetivo es reducir el acoplamiento entre módulos de alto nivel (lógica de negocio) y módulos de bajo nivel (implementaciones concretas), haciendo que ambos dependan de abstracciones (interfaces o clases abstractas).

### 🔑 Reglas fundamentales
1. Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.
2. Las abstracciones no deben depender de detalles. Los detalles deben depender de abstracciones.

## ❌ Ejemplo SIN aplicar DIP (alto acoplamiento)
```java
class LightBulb {
    public void turnOn() { System.out.println("LightBulb is ON"); }
    public void turnOff() { System.out.println("LightBulb is OFF"); }
}

class Switch {
    private LightBulb bulb;
    public Switch(LightBulb bulb) { this.bulb = bulb; }
    public void operate(boolean on) {
        if(on) bulb.turnOn(); else bulb.turnOff();
    }
}

public class Main {
    public static void main(String[] args) {
        LightBulb bulb = new LightBulb();
        Switch lightSwitch = new Switch(bulb);
        lightSwitch.operate(true);
    }
}
```
**Problemas:**
- `Switch` depende directamente de la implementación concreta `LightBulb`.
- Dificulta cambiar a otra fuente de luz (p.ej., `Fan`) o hacer pruebas unitarias.

## ✅ Ejemplo APLICANDO DIP (inyección manual)
### Paso 1: Definir abstracción
```java
public interface Switchable {
    void turnOn();
    void turnOff();
}
```
### Paso 2: Implementar abstracción en la clase concreta
```java
public class LightBulb implements Switchable {
    @Override public void turnOn() { System.out.println("LightBulb is ON"); }
    @Override public void turnOff() { System.out.println("LightBulb is OFF"); }
}
```
### Paso 3: Dependencia por abstracción (constructor injection)
```java
public class ElectricPowerSwitch {
    private final Switchable device;

    public ElectricPowerSwitch(Switchable device) {
        this.device = device;
    }

    public void operate(boolean on) {
        if(on) device.turnOn(); else device.turnOff();
    }
}

public class Main {
    public static void main(String[] args) {
        Switchable bulb = new LightBulb();
        ElectricPowerSwitch powerSwitch = new ElectricPowerSwitch(bulb);
        powerSwitch.operate(true);
    }
}
```
**Ventajas:**
- `ElectricPowerSwitch` solo conoce la interfaz `Switchable`, no la implementación concreta.
- Facilita cambiar la implementación, extensibilidad y testing.

## 🚀 Usando Spring Framework para DIP automático
Spring aplica DIP a través de Inyección de Dependencias (DI) y contenedores IoC.

```java
import org.springframework.stereotype.Component;
import org.springframework.beans.factory.annotation.Autowired;

@Component
public class LightBulb implements Switchable {
    @Override public void turnOn() { System.out.println("LightBulb ON via Spring"); }
    @Override public void turnOff() { System.out.println("LightBulb OFF via Spring"); }
}

@Component
public class ElectricPowerSwitch {
    private final Switchable device;

    @Autowired
    public ElectricPowerSwitch(Switchable device) {
        this.device = device;
    }

    public void operate(boolean on) {
        if(on) device.turnOn(); else device.turnOff();
    }
}
```
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ApplicationContext;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(Application.class, args);
        ElectricPowerSwitch powerSwitch = context.getBean(ElectricPowerSwitch.class);
        powerSwitch.operate(true);
    }
}
```

## ✅ Pruebas unitarias con DIP
```java
import org.junit.jupiter.api.Test;
import static org.mockito.Mockito.*;

public class ElectricPowerSwitchTest {
    @Test
    void testOperateOn() {
        Switchable mockDevice = mock(Switchable.class);
        ElectricPowerSwitch powerSwitch = new ElectricPowerSwitch(mockDevice);

        powerSwitch.operate(true);
        verify(mockDevice).turnOn();
    }
}
```

## 📝 Resumen y Buenas Prácticas
- Siempre programa contra interfaces/abstracciones, no implementaciones.
- Prefiere constructor injection sobre setter injection.
- Usa frameworks DI (Spring) para manejar el ciclo de vida y dependencias.
- Aplica DIP para lograr código más mantenible, extensible y testeable.

¡Felicidades! Ahora dominas el Principio de Inversión de Dependencias en Java de forma simple y práctica.

