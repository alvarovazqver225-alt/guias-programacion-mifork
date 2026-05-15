<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Herencia". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación, Excepciones y Composición.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.2. Herencia

## 1. En orientación a objetos, ¿qué es la **herencia** y su relación con "A es-un B"?. Explica las dos implicaciones principales: (1) **compatibilidad de tipos** y (2) **herencia de estado y comportamiento**. Pon un ejemplo en Java muy sencillo, donde un `Soldado` tiene un `nombre` (privado) y un método `saludar()` que muestra su nombre. Hay dos subtipos: un `Artillero`, que es capaz de disparar cohetes y un `Zapador` que pone minas, ambos heredan el atributo nombre y la capacidad de saludar. Además, y de forma específica, el artillero tiene un número de cohetes y el zapador un número de minas, accesibles mediante "getters" específicos. Respecto a la compatibilidad de tipos, aprovechémosla: crea un array de `Soldado`, mete varios de distinto tipo (son todos compatibles con `Soldado`). Recórrela y que todos te saluden.

### Respuesta

La herencia es un mecanismo de la programación orientada a objetos que permite crear nuevas clases (subclases o clases hijas) a partir de clases existentes (superclases o clases padre). Establece una relación jerárquica del tipo "A es-un B" (por ejemplo, un "Artillero es-un Soldado"), denotando que la subclase es una especialización o un subtipo más concreto de la superclase.

La primera implicación principal es la herencia de estado y comportamiento. La subclase absorbe automáticamente los atributos y métodos definidos en la superclase, evitando reescribir código y permitiendo a la nueva clase centrarse únicamente en añadir los datos o las funcionalidades que la hacen específica, promoviendo así la reutilización.

La segunda implicación, aún más potente, es la compatibilidad de tipos. Al establecerse que una subclase "es un" tipo de su superclase, los objetos de las subclases pueden utilizarse en cualquier lugar donde se espere un objeto de la clase base. Esto permite agrupar objetos heterogéneos bajo un mismo tipo común y tratarlos de forma genérica.

```java
public class Soldado {
    private String nombre;
    
    public Soldado(String nombre) { this.nombre = nombre; }
    
    public void saludar() {
        System.out.println("Se presenta el soldado " + nombre);
    }
}

public class Artillero extends Soldado {
    private int cohetes;
    
    public Artillero(String nombre, int cohetes) {
        super(nombre);
        this.cohetes = cohetes;
    }
    public int getCohetes() { return cohetes; }
    public void disparar() { System.out.println("Disparando cohete!"); }
}

public class Zapador extends Soldado {
    private int minas;
    
    public Zapador(String nombre, int minas) {
        super(nombre);
        this.minas = minas;
    }
    public int getMinas() { return minas; }
    public void plantarMina() { System.out.println("Mina colocada!"); }
}

public class Cuartel {
    public static void main(String[] args) {
        Soldado[] tropa = new Soldado[3];
        tropa[0] = new Soldado("Perez");
        tropa[1] = new Artillero("Gomez", 5);
        tropa[2] = new Zapador("Ruiz", 10);
        
        for (Soldado s : tropa) {
            s.saludar(); // Todos saben saludar gracias a la herencia
        }
    }
}
```


## 2. Al crear los soldados concretos, ¿cuántos constructores se ejecutan y en qué orden? ¿Qué significa `super` dentro de un constructor? Si la clase base no tiene visible el constructor sin parámetros, ¿debo llamar a `super` siempre? 

### Respuesta

Al instanciar un objeto de una subclase, se ejecutan en cadena los constructores de toda la jerarquía de herencia subyacente. El orden de ejecución transcurre desde la clase base más genérica hacia abajo, finalizando con el constructor de la propia subclase que se está instanciando. Esto garantiza que la parte heredada del objeto se inicialice correctamente antes de inicializar los atributos específicos de la subclase.

La palabra reservada `super`, cuando se utiliza seguida de paréntesis dentro de un constructor, sirve para invocar explícitamente a un constructor de la superclase directa. Su llamada debe ser obligatoriamente la primera instrucción dentro del cuerpo del constructor de la subclase.

Si la clase base no dispone de un constructor vacío o sin parámetros (ya sea porque no se definió o no es visible), es estrictamente obligatorio invocar a `super(...)` pasando los argumentos adecuados correspondientes al constructor que sí esté disponible. Si hubiera un constructor por defecto en la clase base, el compilador introduciría una llamada implícita a `super()` automáticamente, pero en su ausencia, se exige la invocación explícita para asegurar la integridad de la inicialización.

## 3. Respecto a los objetos de subclases en memoria, los atributos privados de la superclase, ¿forman parte de una instancia de la subclase en memoria? En caso afirmativo ¿implica que se puedan usar desde el código de la subclase? Explícalo con el ejemplo de `Soldado` y alguna de sus subclases.

### Respuesta

Cuando se instancia un objeto de una subclase, el espacio de memoria reservado para dicho objeto incluye efectivamente todos los atributos de la superclase, independientemente del nivel de visibilidad que tengan asignados. La información de la clase base forma parte indivisible del objeto concreto que se acaba de crear en la memoria RAM.

Sin embargo, que el espacio y el atributo existan físicamente en el objeto no implica que se puedan usar o acceder libremente desde el código de la subclase. Los atributos definidos como `private` en la superclase mantienen su encapsulación y están restringidos exclusivamente al código fuente de la clase que los declaró.

Por ejemplo, un objeto en memoria de tipo `Artillero` contiene internamente el atributo `nombre` heredado de la clase `Soldado`. No obstante, si el programador escribe en la clase `Artillero` código que intente leer o modificar `this.nombre` directamente, el compilador emitirá un error indicando que el acceso es privado. La única vía para interactuar con ese atributo heredado es a través de métodos visibles, como constructores, "getters" o "setters" proporcionados explícitamente por `Soldado`.

## 4. ¿Qué implica en términos de **extensibilidad** de código el hecho de que sean compatibles a nivel de tipos? Ilustra esto añadiendo un nuevo tipo de `Soldado` y demostrando que el código para pedir el saludo a todos los soldados no se modifica.

### Respuesta

La compatibilidad de tipos que provee la herencia es uno de los pilares para lograr sistemas altamente extensibles. Permite escribir bloques de código genéricos enfocados en el supertipo, de manera que dichos bloques puedan seguir funcionando sin modificaciones incluso cuando en el futuro se agreguen nuevos subtipos al sistema.

Esto se traduce en que la aplicación está "abierta a la extensión, pero cerrada a la modificación" (Principio Abierto-Cerrado). Si una porción del código espera manejar objetos del tipo base, puede admitir de forma transparente cualquier nueva variante que implemente la misma interfaz o herede de la misma superclase, reduciendo enormemente los costes de mantenimiento y de ampliación.

Para ilustrar este principio, se puede agregar una nueva clase `Francotirador` que hereda de `Soldado`. El bucle que iteraba el array llamando a `saludar()` permanecerá inalterado y, sin embargo, procesará al nuevo francotirador correctamente, ya que está garantizado que cualquier derivado de `Soldado` poseerá este comportamiento.

```java
public class Francotirador extends Soldado {
    public Francotirador(String nombre) {
        super(nombre);
    }
}

// El código original sigue funcionando exactamente igual sin modificarse
public static void main(String[] args) {
    Soldado[] tropa = {
        new Artillero("Gomez", 5),
        new Francotirador("Vargas") // El nuevo tipo se procesa transparentemente
    };
    
    for (Soldado s : tropa) {
        s.saludar(); 
    }
}
```


## 5. En Java, cuando trabajo con referencias y herencia. ¿Puedo tener una referencia del supertipo que apunte a objetos reales de un subtipo? ¿Puedo invocar con la referencia del supertipo a métodos públicos del subtipo? ¿En qué consiste el **"upcasting"** y el **"downcasting"**? ¿Qué es el `instanceof`? Pon un ejemplo de recorrido de un array de `Soldado`, comprobando que, si el objeto real es un `Artillero`, solicite el número de cohetes que tiene y los imprima.

### Respuesta

En lenguajes con tipos estáticos como Java, es totalmente legal tener una referencia del supertipo apuntando a un objeto real de un subtipo en memoria (lo cual se conoce como "upcasting"). Sin embargo, el compilador únicamente permitirá invocar, a través de esa referencia, aquellos métodos que estén definidos formalmente en la interfaz del supertipo, impidiendo llamar a los métodos específicos de la subclase aunque el objeto real los posea.

Para poder usar los métodos específicos, se requiere realizar un "downcasting", que es la conversión explícita de la referencia genérica a una referencia del tipo más concreto. Dado que esta operación puede fallar en tiempo de ejecución si el objeto no es verdaderamente del subtipo indicado, se suele emplear previamente el operador `instanceof`. Este operador evalúa de manera segura si un objeto en memoria pertenece a una clase concreta o a cualquiera de sus derivados, devolviendo un valor booleano.

```java
public static void revisarArmamento(Soldado[] tropa) {
    for (Soldado s : tropa) {
        // Upcasting implícito al tratar a todos como 'Soldado'
        s.saludar(); 

        // Comprobación de tipo segura antes del downcasting
        if (s instanceof Artillero) {
            // Downcasting explícito a 'Artillero'
            Artillero a = (Artillero) s;
            System.out.println("Posee " + a.getCohetes() + " cohetes listos.");
        }
    }
}
```


## 6. Respecto a la ocultación de información y herencia, ¿qué significa acceso **"protegido"** de métodos y/o atributos? ¿Cómo se implementa en Java? Pon un ejemplo de uso de en la clase `Soldado` para que su nombre sea protegido y pueda usarse en el método de poner bombas del `Zapador`.

### Respuesta

El nivel de acceso protegido es un grado intermedio de visibilidad diseñado específicamente para escenarios de herencia. Cuando un atributo o método se declara como protegido, queda oculto para las clases ajenas del exterior, pero se hace visible y accesible directamente desde el código fuente de cualquier subclase que herede de dicha clase, independientemente del paquete donde resida la subclase.

En Java, este nivel se implementa utilizando la palabra reservada `protected` antes de la declaración del atributo o método. Cabe destacar que, debido a particularidades del diseño de Java, los miembros protegidos también resultan visibles para cualquier otra clase que se encuentre dentro del mismo paquete (visibilidad a nivel de paquete).

Si se aplica este concepto al ejemplo anterior declarando el nombre del soldado como `protected String nombre`, las subclases como `Zapador` ganarán permiso para acceder al estado interno del padre directamente. De esta forma, el zapador podrá utilizar `this.nombre` dentro de sus métodos específicos sin requerir el uso de un método contenedor "getter".

```java
public class Soldado {
    // Visibilidad protegida, accesible para clases hijas
    protected String nombre;
    
    public Soldado(String nombre) { this.nombre = nombre; }
}

public class Zapador extends Soldado {
    public Zapador(String nombre) {
        super(nombre);
    }
    
    public void ponerBomba() {
        // Acceso directo al atributo heredado gracias a protected
        System.out.println("El zapador " + this.nombre + " ha plantado un explosivo.");
    }
}
```


## 7. En los lenguajes orientados a objetos ¿hay una **clase base** para todos los objetos? ¿Ocurre en todos los lenguajes? ¿Qué ocurre en Java?

### Respuesta

En la mayoría de los lenguajes de programación puramente orientados a objetos modernos, existe una clase base o clase raíz suprema de la cual heredan, de forma directa o indirecta, todas las demás clases del sistema. Esta aproximación garantiza que absolutamente cualquier objeto disponga de un comportamiento mínimo común o características base para su manipulación general.

Sin embargo, esto no ocurre de forma universal en todos los lenguajes que soportan orientación a objetos. Por ejemplo, en C++ no existe una clase raíz predefinida impuesta por el lenguaje. El programador puede crear múltiples jerarquías inconexas que no comparten ningún ancestro en común a nivel sintáctico del lenguaje.

En el caso particular de Java, sí existe esta raíz suprema y recibe el nombre de la clase `Object`. Si al programar una clase no se especifica explícitamente ninguna superclase usando la palabra `extends`, el compilador hace que la clase herede de forma automática de `java.lang.Object`. Gracias a esto, todos los objetos en Java garantizan disponer de métodos básicos como `.toString()`, `.equals()` o `.hashCode()`.


## 8. ¿Qué es la **"herencia múltiple"**? ¿Existe en Java herencia múltiple?

### Respuesta

La herencia múltiple es una característica presente en algunos lenguajes orientados a objetos que permite que una sola clase herede estados y comportamientos de dos o más superclases de manera simultánea. Es decir, una clase hija podría tener más de un padre directo, asimilando así los atributos y métodos de varias líneas jerárquicas distintas.

El principal problema asociado a la herencia múltiple es la complejidad semántica y estructural que puede introducir, destacando el clásico "Problema del Diamante". Este conflicto ocurre cuando una clase hereda de dos superclases, y ambas a su vez comparten un ancestro común, generando ambigüedades sobre qué versión de un método repetido debe predominar si ambas ramas intermedias lo sobrescribieron.

Debido a estas complicaciones, Java fue diseñado expresamente sin soporte para la herencia múltiple de clases. En Java, una clase solo puede heredar (usando `extends`) de una única superclase directa, fomentando jerarquías lineales y más simples. A cambio, provee las interfaces (mediante la palabra `implements`) para simular la herencia múltiple de tipos y contratos, aunque tradicionalmente sin herencia de estado.


## 9. Las excepciones en los lenguajes orientados a objetos son objetos. Por tanto, se pueden crear excepciones personalizadas. Pon un ejemplo en Java de una excepción personalizada (`UsuarioNoEncontradoException`), que sea *no controlada* y que además este compuesto con un `Usuario`, para saber qué `Usuario` dio el problema. Permite además que se pueda incluir la causa, es decir, sobrecarga el constructor para tener una versión que permita añadir la causa subyacente. 

### Respuesta

Las excepciones personalizadas permiten crear tipos de error que expresan de forma semántica y precisa la causa del problema en el dominio del negocio. Al extender de la clase `RuntimeException`, se crea automáticamente una excepción "no controlada" (unchecked), es decir, que el compilador de Java no obliga a capturar mediante bloques `try-catch` ni a declarar explícitamente con `throws` en la cabecera de los métodos.

En este diseño, la excepción utiliza la composición para almacenar información adicional de contexto que resulte útil durante la depuración. En lugar de limitarse a guardar un simple mensaje de texto, la excepción custodia el objeto concreto (`Usuario`) que originó el estado anómalo, facilitando inspeccionar el estado del sistema en el punto del error.

Además, se provee sobrecarga de constructores para aceptar opcionalmente un objeto `Throwable` previo, conocido como la "causa raíz". Esta práctica es vital en la arquitectura de excepciones, ya que permite propagar el contexto funcional (el usuario no fue encontrado) sin destruir la traza original técnica (por ejemplo, un problema previo de conexión de red), encadenando unas excepciones con otras.

```java
// Clase Usuario básica de ejemplo
public class Usuario {
    private String username;
    public Usuario(String username) { this.username = username; }
    public String getUsername() { return username; }
}

// Excepción no controlada (hereda de RuntimeException) compuesta con Usuario
public class UsuarioNoEncontradoException extends RuntimeException {
    private final Usuario usuarioProblematico;

    // Constructor con mensaje y usuario
    public UsuarioNoEncontradoException(String mensaje, Usuario usuario) {
        super(mensaje);
        this.usuarioProblematico = usuario;
    }

    // Constructor sobrecargado para incluir la causa raíz (encadenamiento)
    public UsuarioNoEncontradoException(String mensaje, Usuario usuario, Throwable causa) {
        super(mensaje, causa);
        this.usuarioProblematico = usuario;
    }

    public Usuario getUsuarioProblematico() {
        return usuarioProblematico;
    }
}
```


## 10. Herencia vs. Composición. Se dice que no se debe emplear herencia simplemente por reutilizar código, es decir, que si quiero reutilizar código simplemente, no debo pensar en herencia como primera opción ¿por qué?

### Respuesta

Emplear herencia exclusivamente como mecanismo para reutilizar bloques de código preexistentes es un antipatrón de diseño. Cuando se hereda, se fuerza semánticamente que la nueva clase es un subtipo lógico de la clase padre. Si esto no refleja fielmente la realidad del dominio (una verdadera relación "es-un"), el diseño se vuelve frágil y engañoso.

Forzar herencias antinaturales lleva a que la subclase exponga métodos heredados que no tienen sentido o que resultan perjudiciales en su nuevo contexto. Por ejemplo, si se desea crear una clase `Pila` (Stack) y se hereda de `Lista` (List) simplemente para reusar las funcionalidades de inserción, la Pila terminará exponiendo métodos para insertar o borrar en posiciones intermedias, lo cual viola radicalmente la esencia y el comportamiento esperado de una Pila estructural.

A largo plazo, las jerarquías de herencia mal concebidas originan rigidez arquitectónica. Un cambio interno en la clase padre se propaga obligatoriamente a todas las clases hijas, pudiendo quebrar su funcionamiento de forma imprevista si no existía realmente una subordinación lógica sólida entre ellas.


## 11. Herencia vs. Composición. Se dice que se debe *"favorecer la composición frente a la herencia"*, ¿por qué?

### Respuesta

El principio de "favorecer la composición frente a la herencia" promueve ensamblar objetos complejos combinando instancias de objetos más pequeños e independientes en lugar de construir profundos y rígidos árboles de herencia. Esta estrategia conduce a sistemas con un grado de acoplamiento significativamente menor, facilitando el mantenimiento y la evolución del código a lo largo del tiempo.

Con la composición, el comportamiento se delega a los objetos contenidos durante la ejecución, lo que proporciona una enorme flexibilidad y dinamismo. Resulta sumamente sencillo intercambiar o sustituir una de las partes de un objeto en tiempo real para modificar su conducta sin necesidad de reprogramar el sistema. Por el contrario, la relación de herencia queda soldada estáticamente durante el proceso de compilación y es invariable.

Además, la composición reduce los problemas clásicos de propagación de cambios en las jerarquías profundas. Un objeto puede valerse de otro mediante la invocación controlada de sus métodos públicos, sin depender intrínsecamente de su estructura interna. Esta independencia garantiza que modificaciones en el componente contenido tendrán un impacto delimitado y previsible sobre el componente contenedor.


## 12. Herencia vs. Composición. Se dice que la *"herencia rompe la encapsulación"*, ¿a qué se refiere esto?

### Respuesta

Se afirma que la herencia viola o compromete la encapsulación porque la clase hija pasa a depender de los detalles internos de implementación de la clase padre, en lugar de depender únicamente de su interfaz pública. Cuando se crea una subclase, esta asume suposiciones implícitas sobre cómo los métodos heredados están programados en su interior, un grado de conocimiento que no ocurre en una relación normal de uso entre clases separadas.

Un problema recurrente se da cuando una superclase implementa métodos que internamente se invocan entre sí. Si una subclase sobrescribe uno de esos métodos pensando de forma aislada sin conocer la dinámica interna del padre, podría desencadenar efectos colaterales graves, dobles contabilizaciones o llamadas infinitas, destruyendo por completo el estado del objeto de forma involuntaria.

Debido a esto, las clases diseñadas para ser heredadas requieren una documentación exhaustiva sobre sus flujos internos, su invocación cruzada de métodos y los límites permitidos para la sobrescritura. En esencia, la barrera protectora de la caja negra de la encapsulación se degrada, ya que la superclase y la subclase acaban conformando un componente fuertemente acoplado en sus lógicas de ejecución.


## 13. Pongamos un ejemplo de dos alternativas para lo mismo. Tenemos un `Estudiante` y un `Trabajador`, ambos tienen datos en común: el DNI y el nombre. Modelemos esto de dos formas: uno por herencia, con una superclase `Persona`, y otro con composición, con una clase `DatosPersonales`. Se debe recibir una instancia de `DatosPersonales` en el constructor de la clase `Estudiante` y `Trabajador`.

### Respuesta

A continuación se ilustran las dos alternativas de diseño. En el enfoque basado en la herencia, se abstraen los atributos compartidos hacia una clase base abstracta `Persona`. Las clases `Estudiante` y `Trabajador` extienden esta clase, adquiriendo automáticamente la estructura de la persona y especializándose con sus propios atributos.

```java
// Alternativa 1: Herencia
public class Persona {
    private final String dni;
    private final String nombre;
    
    public Persona(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

public class Estudiante extends Persona {
    private String matricula;
    
    public Estudiante(String dni, String nombre, String matricula) {
        super(dni, nombre);
        this.matricula = matricula;
    }
}

public class Trabajador extends Persona {
    private String departamento;
    
    public Trabajador(String dni, String nombre, String departamento) {
        super(dni, nombre);
        this.departamento = departamento;
    }
}
```

En contraste, el enfoque mediante composición aísla los datos comunes en un componente o módulo independiente llamado `DatosPersonales`. `Estudiante` y `Trabajador` no comparten herencia; en cambio, albergan internamente una instancia de esta clase. Este modelo resulta más flexible si se anticipa que un individuo pudiera operar simultáneamente como estudiante y trabajador o si la estructura de los datos personales necesitara ser sustituida en tiempo de ejecución.

```java
// Alternativa 2: Composición
public class DatosPersonales {
    private final String dni;
    private final String nombre;
    
    public DatosPersonales(String dni, String nombre) {
        this.dni = dni;
        this.nombre = nombre;
    }
}

public class EstudianteComp {
    private final DatosPersonales personaInfo;
    private String matricula;
    
    public EstudianteComp(DatosPersonales info, String matricula) {
        this.personaInfo = info;
        this.matricula = matricula;
    }
}

public class TrabajadorComp {
    private final DatosPersonales personaInfo;
    private String departamento;
    
    public TrabajadorComp(DatosPersonales info, String departamento) {
        this.personaInfo = info;
        this.departamento = departamento;
    }
}
```
