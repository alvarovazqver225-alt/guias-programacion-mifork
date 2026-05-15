<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Polimorfismo". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones, Composición y Herencia.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 5. Polimorfismo

## 1. Brevemente, ¿qué es el **"polimorfismo"** y para qué sirve en programación orientada a objetos? ¿qué es la **"sobreescritura"** de métodos?

### Respuesta

El polimorfismo es un concepto central en la programación orientada a objetos que significa "múltiples formas". Permite tratar objetos de diferentes clases derivadas de una misma superclase como si fueran del mismo tipo base, pero con la característica clave de que cada objeto responderá a una misma invocación de método de la manera específica que corresponde a su tipo real.

Su principal utilidad es lograr código altamente abstracto, flexible y extensible. Gracias al polimorfismo, es posible escribir funciones o estructuras de control que operen de forma genérica sobre un supertipo, sin necesidad de conocer en tiempo de compilación qué variante exacta de objeto se está manejando. Cuando el sistema crezca y se añadan nuevos subtipos, el código genérico seguirá funcionando sin requerir modificaciones.

La "sobreescritura" (overriding) de métodos es el mecanismo fundamental que hace posible el polimorfismo. Consiste en que una clase hija redefine un método que ya había sido heredado de su clase padre, manteniendo exactamente la misma firma (mismo nombre, mismos parámetros y tipo de retorno). De esta manera, la subclase anula el comportamiento por defecto de su ancestro e inyecta su lógica particular y especializada.


## 2. ¿En qué consiste la **"ligadura dinámica"** o **"enlace tardío"**? ¿qué relación tiene con el polimorfismo? ¿hay que indicarlos explícitamente al programar o depende esto del lenguaje? Compara C++ y Java. Indicalo después también para Python.

### Respuesta

La ligadura dinámica (o enlace tardío) es el mecanismo interno que resuelve a qué bloque de código exacto debe saltar la ejecución cuando se invoca un método polimórfico. A diferencia de la ligadura estática, donde el compilador decide la llamada basándose en el tipo de la variable de referencia, en la ligadura dinámica la decisión se pospone hasta el tiempo de ejecución (runtime), basándose en el tipo real del objeto que se encuentra en memoria.

El comportamiento varía según el lenguaje. En Java, la ligadura dinámica es el comportamiento predeterminado y automático para todos los métodos de instancia (no estáticos). No es necesario utilizar ninguna palabra clave especial; por defecto, cualquier método puede ser sobreescrito y su resolución se realizará en tiempo de ejecución.

En C++, por razones de eficiencia y filosofía del lenguaje, el comportamiento por defecto es la ligadura estática. Para habilitar el polimorfismo y forzar el enlace tardío, el programador debe indicar explícitamente que un método es susceptible de ello, marcándolo con la palabra clave `virtual` en la clase base. Por último, en Python, al ser un lenguaje de tipado dinámico o "duck typing", toda resolución de métodos se realiza siempre en tiempo de ejecución de manera inherente, sin necesidad de declarar herencias estrictas ni palabras clave.


## 3. Pon un ejemplo sencillo en Java, de un `Soldado`, con un método `saluda`, con dos subclases: `Zapador` y `Artillero`, donde `Zapador` sobreescribe el método `saludar`, sustituyendo por completo su comportamiento. Ilustra el funcionamiento del polimorfismo creando un array de `Soldados` de dos tipos y luego recorriéndolo empleando referencias de tipo `Soldado` y llamando a `saludar`.

### Respuesta

El código anterior ilustra el mecanismo de polimorfismo mediante herencia. La clase base `Soldado` define un comportamiento genérico que las clases hijas `Zapador` y `Artillero` deciden sobreescribir completamente, aportando su propia implementación del saludo y alterando así la conducta heredada.

En el programa principal, la matriz se declara del tipo genérico `Soldado`, lo que permite almacenar objetos de cualquier subtipo. Al iterar el array y ejecutar el método `saludar()`, la ligadura dinámica asegura que no se llame siempre a la versión genérica; en su lugar, se invoca automáticamente el código específico correspondiente al tipo real de cada objeto en ese instante.

```java
public class Soldado {
    public void saludar() {
        System.out.println("Se presenta el soldado.");
    }
}

public class Zapador extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Aquí un zapador, listo para despejar el área.");
    }
}

public class Artillero extends Soldado {
    @Override
    public void saludar() {
        System.out.println("Artillero preparado con explosivos pesados.");
    }
}

public class DemostracionPolimorfismo {
    public static void main(String[] args) {
        Soldado[] peloton = new Soldado[3];
        peloton[0] = new Soldado();
        peloton[1] = new Zapador();
        peloton[2] = new Artillero();

        // Recorrido empleando la referencia genérica del supertipo
        for (Soldado s : peloton) {
            // Se ejecuta la versión específica de cada subclase (Polimorfismo)
            s.saludar(); 
        }
    }
}
```


## 4. Si sobreescribo un método, ¿puedo invocar el método base para trabajar a partir de su resultado? Haz que zapador cambie ligeramente la forma de saludar, que salude de forma normal, tal cual hace el soldado base, pero que además añada un "ZAPADOR A SUS ORDENES" ¿qué palabra clave del lenguaje has usado para invocar al método de la clase base?

### Respuesta

Al sobreescribir un método, no es estrictamente necesario descartar toda la lógica programada en la clase padre. Se puede invocar el método base para aprovechar su funcionamiento original y, a continuación o de forma previa, añadir instrucciones nuevas para ampliar o refinar dicho comportamiento.

Para lograr esto, se emplea la palabra clave `super` seguida del nombre del método, de forma análoga a como se utiliza `this` para referenciar a la instancia actual. Esta construcción, `super.saludar()`, ordena al compilador que ignore la sobreescritura actual y ejecute directamente el bloque de código definido en la superclase superior inmediata.

Es una práctica extremadamente común en orientación a objetos, ya que evita la duplicación de código. Permite que la subclase se encargue exclusivamente de gestionar sus nuevos atributos o lógicas, delegando a la superclase la gestión de la parte común que ya estaba correctamente implementada.

```java
public class Zapador extends Soldado {
    @Override
    public void saludar() {
        super.saludar(); // Invoca el comportamiento base
        System.out.println("ZAPADOR A SUS ORDENES"); // Añade el comportamiento específico
    }
}
```


## 5. Al sobreescribir un método en Java, ¿qué restricciones existen sobre los tipos de los parámetros y el tipo de retorno? ¿Qué diferencia hay entre sobreescritura (*overriding*) y sobrecarga (*overloading*)? ¿Para qué sirve la anotación `@Override` y por qué es recomendable usarla siempre?

### Respuesta

Al sobreescribir un método (overriding), la firma debe mantenerse idéntica: el nombre y los tipos de los parámetros deben ser exactamente los mismos. Respecto al tipo de retorno, Java permite la "covarianza", lo que significa que el método sobreescrito puede devolver un subtipo del retorno original, pero nunca un tipo incompatible. Además, la visibilidad del método en la subclase no puede ser más restrictiva que en la superclase.

La sobreescritura (overriding) no debe confundirse con la sobrecarga (overloading). La sobrecarga ocurre cuando en una misma clase se declaran múltiples métodos con el mismo nombre pero con diferentes parámetros (distinto tipo o cantidad). La sobrecarga es un mecanismo de ligadura estática en tiempo de compilación y no está relacionada con el polimorfismo de herencia, mientras que la sobreescritura implica alterar un comportamiento heredado.

La anotación `@Override` se coloca justo encima de la declaración del método sobreescrito. Aunque no es estrictamente obligatoria, su uso es altamente recomendable porque instruye al compilador para que verifique formalmente que realmente se está sobreescribiendo un método de la superclase. Si se comete un error tipográfico en el nombre del método o en los parámetros, el compilador lanzará un error de inmediato, evitando defectos lógicos difíciles de detectar.


## 6. Entonces, cuando se estudia Java, ¿se emplea el polimorfismo desde el principio? Por ejemplo, sobreescribiendo `toString` o sobreescribiendo `equals`, ¿ya estoy usando polimorfismo?

### Respuesta

Efectivamente, en el ecosistema de Java, los desarrolladores utilizan el polimorfismo y la sobreescritura desde las fases más tempranas del aprendizaje, a menudo sin ser plenamente conscientes de ello. Dado que absolutamente todas las clases creadas en Java heredan implícitamente de la superclase universal `java.lang.Object`, ya se encuentran inmersas en una jerarquía de herencia.

Cuando un programador implementa métodos como `toString()` o `equals(Object obj)` en una clase propia, lo que está haciendo realmente es sobreescribir las versiones genéricas y vacías que proporciona la clase `Object`. Esto constituye un uso genuino de polimorfismo y enlace tardío.

Gracias a esto, herramientas internas del lenguaje o librerías de terceros (como las colecciones, el comando `System.out.println()` o los depuradores) pueden tratar a cualquier objeto creado por el usuario como una instancia genérica de `Object`. Al invocar dichos métodos, el polimorfismo garantiza que se ejecutará la representación en formato de texto o la lógica de comparación específica y correcta definida por el programador.


## 7. ¿Qué es una **"clase abstracta"**? ¿Qué es un **"método abstracto"**? ¿Puedo crear instancias de una clase abstracta? Pongamos un ejemplo en Java: Redefinamos `Soldado`, hagamos que, además del método `saluda` que ya tenía, tenga un método `atacar`, que sea abstracto y que cada tipo de soldado haga su acción cuando se le pida atacar. ¿Donde debemos poner `abstract`?

### Respuesta

Una clase abstracta es una clase incompleta que sirve exclusivamente como molde o base para que otras clases hereden de ella. Su característica principal es que no permite la creación de instancias u objetos de forma directa (no se puede hacer un `new` de una clase abstracta). Solo se pueden instanciar sus subclases "concretas".

Un método abstracto es aquel que define una firma (nombre, parámetros y retorno) pero carece de implementación o cuerpo (no tiene llaves `{}`). Delegando la obligación de programar la lógica interna a las subclases. Sirve para establecer un contrato estricto: obliga a que toda clase hija proporcione su propia versión de dicha funcionalidad. Si una clase posee al menos un método abstracto, debe declararse obligatoriamente como clase abstracta.

La palabra clave `abstract` se sitúa en la declaración de la clase, justo antes de `class`, y en la declaración del método, antes del tipo de retorno. En el ejemplo del soldado, se añade `atacar()` como abstracto en la base, forzando a que tanto zapadores como artilleros implementen cómo atacan en la realidad.

```java
// Se marca la clase como abstracta para que no pueda ser instanciada directamente
public abstract class Soldado {
    public void saluda() {
        System.out.println("Se presenta el soldado.");
    }
    
    // Método abstracto: define un contrato pero carece de cuerpo
    public abstract void atacar();
}

public class Zapador extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Colocando carga explosiva C4.");
    }
}

public class Artillero extends Soldado {
    @Override
    public void atacar() {
        System.out.println("Disparando proyectil de artillería.");
    }
}
```


## 8. ¿Qué efecto tiene la palabra clave `final` sobre métodos y clases en Java? ¿Cómo se relaciona con el polimorfismo? ¿Conoces algún ejemplo de clase `final` en la propia API estándar de Java?

### Respuesta

La palabra clave `final` en Java se utiliza para imponer restricciones inmutables sobre el diseño arquitectónico de clases y métodos, actuando de forma opuesta y restrictiva frente al polimorfismo y la herencia. 

Cuando se aplica `final` a la declaración de un método, se está prohibiendo tajantemente que cualquier subclase pueda sobreescribirlo. Si se aplica `final` a nivel de clase, se prohíbe por completo que existan clases hijas; la clase no puede ser heredada. Esto corta de raíz cualquier posibilidad de utilizar ligadura dinámica para esos métodos o de extender el comportamiento del objeto.

Este mecanismo es crucial por motivos de seguridad, rendimiento y coherencia de diseño. Un ejemplo clásico en la API estándar de Java es la clase `String`. Al estar declarada como `public final class String`, ningún programador puede crear una subclase de `String` para alterar maliciosamente su comportamiento interno o violar su naturaleza estrictamente inmutable en el lenguaje.


## 9. En Java, qué son las **"interfaces"**? ¿Son como clases abstractas? ¿Una clase puede implementar más de una interfaz?

### Respuesta

En Java, las interfaces son estructuras que actúan como contratos puramente abstractos. Históricamente, una interfaz solo podía contener definiciones de métodos sin cuerpo (métodos abstractos) y constantes estáticas, sin estado o atributos de instancia. Su propósito es definir un conjunto de acciones o comportamientos públicos que una clase promete saber realizar, disociando qué debe hacer de cómo debe hacerlo.

Aunque guardan similitudes con las clases abstractas (dado que ninguna de las dos se puede instanciar y ambas exigen a las subclases implementar funcionalidades), las interfaces carecen por completo de constructores y estado interno ligado al objeto. Recientemente (a partir de Java 8), las interfaces permiten proveer implementaciones por defecto mediante métodos `default`, pero su filosofía esencial de ser meros contratos de capacidad persiste.

A diferencia de la herencia de clases tradicional, donde Java impone una restricción de herencia simple (solo se puede usar un `extends`), una misma clase puede implementar una cantidad ilimitada de interfaces separándolas por comas (usando `implements`). Esto permite solventar de manera limpia los problemas derivados de la falta de herencia múltiple en el lenguaje, dotando al objeto de múltiples facetas de comportamiento.


## 10. Vamos a poner un ejemplo nuevo con polimorfismo. Queremos implementar una clase `Punto`, con un método `calcularDistanciaA`, que permite calcular la distancia a otro `Punto`. Sin embargo, como queremos trabajar con puntos 2D y 3D, haz que ese método sea abstracto y haya dos implementaciones de ese cálculo de distancia. Emplea `instanceof` y *downcasting* para verificar que se recibe un punto compatible y poder calcular correctamente la distancia siempre entre puntos del mismo subtipo. Aprovecha este diseño para crear ahora una clase `Linea`, que acepta `Punto`, sin saber de qué tipo es, y es capaz de dar su longitud independientemente de las dimensiones de sus puntos (las cuales desconoce).

### Respuesta

Este diseño ilustra de manera elegante el poder del polimorfismo. La clase abstracta `Punto` establece el contrato general sobre cómo deben interactuar los puntos entre sí, forzando a cada versión matemática concreta (2D y 3D) a proveer su propia fórmula espacial particular. La comprobación con `instanceof` actúa como una barrera de seguridad necesaria antes de forzar el tipo de la variable (downcasting).

El mayor beneficiario de este polimorfismo es la clase `Linea`. Al estar diseñada exclusivamente contra el tipo abstracto `Punto`, desconoce por completo las lógicas bidimensionales o tridimensionales. La invocación `origen.calcularDistanciaA(destino)` dispara el enlace tardío y la máquina virtual delega el cálculo exacto a la versión pertinente.

Si en un escenario posterior el programa necesitase integrar soporte para puntos en un plano temporal (4D), bastaría con programar un nuevo `Punto4D`. La clase contenedora `Linea` seguiría compilando y computando longitudes tetradimensionales con total precisión, sin que un programador deba alterar ni una sola coma de su código interno.

```java
// Clase base abstracta
public abstract class Punto {
    // Método polimórfico abstracto
    public abstract double calcularDistanciaA(Punto otro);
}

public class Punto2D extends Punto {
    private final double x, y;
    public Punto2D(double x, double y) { this.x = x; this.y = y; }
    
    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto2D)) {
            throw new IllegalArgumentException("Incompatible, se espera Punto2D");
        }
        Punto2D p2 = (Punto2D) otro; // Downcasting seguro
        return Math.sqrt(Math.pow(p2.x - this.x, 2) + Math.pow(p2.y - this.y, 2));
    }
}

public class Punto3D extends Punto {
    private final double x, y, z;
    public Punto3D(double x, double y, double z) { this.x = x; this.y = y; this.z = z; }
    
    @Override
    public double calcularDistanciaA(Punto otro) {
        if (!(otro instanceof Punto3D)) {
            throw new IllegalArgumentException("Incompatible, se espera Punto3D");
        }
        Punto3D p3 = (Punto3D) otro; // Downcasting seguro
        return Math.sqrt(Math.pow(p3.x - this.x, 2) + 
                         Math.pow(p3.y - this.y, 2) + 
                         Math.pow(p3.z - this.z, 2));
    }
}

// Clase Linea totalmente agnóstica de las dimensiones del Punto
public class Linea {
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        this.origen = origen;
        this.destino = destino;
    }

    public double longitud() {
        // Enlace tardío: se llamará a la fórmula 2D o 3D correspondientemente
        return origen.calcularDistanciaA(destino);
    }
}
```


## 11. ¿Qué es la **"herencia de interfaces"** en Java? ¿Existe **"herencia múltiple de interfaces"**? Pon un ejemplo de una interfaz `Fichero` que tenga un método para leer su contenido en forma de `String` y luego dicha interfaz sea extendida por otra que sea `FicheroEscribible` que permita enviar contenido e incluso eliminar el fichero.

### Respuesta

La herencia de interfaces en Java es un mecanismo que permite que una interfaz amplíe a otra interfaz utilizando la palabra clave `extends`. Al igual que con las clases, la interfaz hija hereda automáticamente todos los contratos (métodos abstractos) declarados en la interfaz padre, añadiendo sobre ellos sus nuevas firmas de comportamiento más especializadas o ricas.

Una peculiaridad fundamental en Java es que, mientras las clases están atadas a la herencia simple, las interfaces sí soportan la herencia múltiple completa. Una sola interfaz puede extender de múltiples interfaces simultáneamente (por ejemplo: `interface FicheroAvanzado extends Fichero, Cifrable, Comprimible`). Esto es posible porque, al carecer de estado interno y de implementaciones por defecto en su concepción original, se anulan los peligros clásicos como el "Problema del Diamante".

En el ejemplo propuesto, la interfaz base `Fichero` define la capacidad esencial de lectura. La segunda interfaz, `FicheroEscribible`, hereda de ella, lo que implica que cualquier clase que decida implementar `FicheroEscribible` estará obligada por contrato a desarrollar tanto los métodos de escritura y eliminación propios de la subinterfaz, como el método de lectura original de la interfaz madre.

```java
// Interfaz base
public interface Fichero {
    String leerContenido();
}

// Subinterfaz que amplía el contrato heredando de Fichero
public interface FicheroEscribible extends Fichero {
    void escribirContenido(String texto);
    void eliminarFichero();
}
```
