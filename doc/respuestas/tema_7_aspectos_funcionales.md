<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Aspectos funcionales". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia, polimorfismo y genericidad.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 7. Aspectos funcionales

## 1. ¿Qué es un puntero a una función? Pon un ejemplo de código en C, donde se define una función y que reciba una cadena de caracteres como parámetro y devuelva la cadena en mayúsculas. Crea un puntero en una variable local a dicha función llamado `aMayusculas` e invócala con el puntero.

### Respuesta

Un puntero a función es una variable que, en lugar de almacenar un valor de datos (como un entero o un carácter), almacena la dirección de memoria donde reside el código ejecutable de una función. Este mecanismo permite pasar funciones como parámetros a otras funciones, guardarlas en estructuras de datos o invocarlas indirectamente en tiempo de ejecución de manera dinámica.

En C, los punteros a funciones constituyen la base para implementar técnicas avanzadas como *callbacks* o tablas de despacho. Para declarar un puntero a función, es indispensable que la firma de la variable puntero (tipos de retorno y parámetros) coincida con exactitud con la firma de la función a la que apuntará.

```c
#include <stdio.h>
#include <ctype.h>
#include <string.h>

// Definición de la función real
void convertirAMayusculas(char* cadena) {
    for (int i = 0; i < strlen(cadena); i++) {
        cadena[i] = toupper(cadena[i]);
    }
}

int main() {
    char texto[] = "hola mundo";

    // Puntero a función (retorna void, recibe char*)
    void (*aMayusculas)(char*) = convertirAMayusculas;

    // Invocación a través del puntero
    aMayusculas(texto);

    printf("Texto convertido: %s\n", texto);
    return 0;
}
```


## 2. ¿Qué es una **función lambda** en un lenguaje de programación? Pon un ejemplo similar al anterior en Javascript y otro en Java con funciones lambda. Usa una variable local `aMayusculas` para apuntar a la función lambda. Por simplicidad, en Java, emplea `Function<String, String>` para el tipo de la referencia a la función lambda.

### Respuesta

Aunque superficialmente las funciones lambda y los punteros a funciones en C parecen resolver el mismo problema (pasar comportamientos de un lado a otro), difieren fundamentalmente por el concepto de los cierres léxicos (*closures*).

Un puntero a función en C es estrictamente un apuntador crudo a un bloque de instrucciones en memoria. Cuando el programa "salta" a esa dirección, dicho código carece de cualquier contexto o memoria del entorno exterior donde se generó el puntero; todo el estado que requiera la función para operar debe ser pasado obligatoriamente de forma manual a través de sus argumentos.

En profundo contraste, una función lambda (acompañada de su closure) no es únicamente una referencia de código. Conceptualmente, es un paquete empaquetado que contiene tanto el bloque de código a ejecutar, como una copia oculta del estado capturado y referenciado (las variables locales atrapadas del entorno). Esta simbiosis de código y estado encapsulado proporciona una enorme abstracción frente al enfoque primitivo de los lenguajes de bajo nivel.

Una función lambda (o función anónima) es una porción de código ejecutable que se define de manera compacta, usualmente sin asociarle un nombre o identificador explícito, directamente en el lugar donde se va a utilizar o asignar. Su propósito principal es facilitar la escritura de fragmentos de comportamiento cortos y autocontenidos, permitiendo tratar el código como un dato más que puede ser almacenado, devuelto o pasado como argumento.

A diferencia de las funciones tradicionales que requieren definiciones formales separadas, las funciones lambda se declaran *in situ* y utilizan una sintaxis reducida. Esto favorece enormemente la legibilidad cuando se escriben algoritmos que requieren transformaciones rápidas, filtrados o cualquier modelo de *callback*.

```javascript
// Ejemplo en JavaScript
let texto = "hola mundo js";

// Definición de la función lambda (arrow function)
const aMayusculas = (cadena) => cadena.toUpperCase();

console.log(aMayusculas(texto));
```

```java
// Ejemplo en Java
import java.util.function.Function;

public class Main {
    public static void main(String[] args) {
        String texto = "hola mundo java";

        // Definición de la función lambda en Java
        Function<String, String> aMayusculas = (cadena) -> cadena.toUpperCase();

        System.out.println(aMayusculas.apply(texto));
    }
}
```


## 3. ¿Qué es el **paradigma funcional**? ¿Por qué a algunos lenguajes orientados a objetos como Java 8, se les llama multi-paradigma? ¿Qué quiere decir que las funciones son "ciudadanos de primera clase"?

### Respuesta

El paradigma funcional es un estilo de programación declarativo que trata la computación como la evaluación de funciones matemáticas puras, evitando estrictamente el cambio de estado (mutabilidad) y los efectos secundarios. En lugar de ejecutar instrucciones paso a paso que modifican variables (como en la programación imperativa), el flujo se basa en aplicar funciones a entradas inmutables para obtener nuevos valores resultantes, componiendo funciones pequeñas para resolver problemas complejos.

Cuando lenguajes tradicionalmente imperativos y orientados a objetos, como Java (a partir de su versión 8) o C++, incorporan conceptos del mundo funcional (como lambdas o flujos de datos puros), pasan a denominarse lenguajes "multi-paradigma". Esto significa que ofrecen al desarrollador la libertad de combinarlos: modelar el estado del sistema usando clases y objetos (POO), pero manipular los comportamientos, colecciones y procesos asíncronos mediante técnicas puramente funcionales.

Decir que las funciones son "ciudadanos de primera clase" (first-class citizens) significa que el lenguaje otorga a las funciones el mismo rango y privilegios que a las variables o valores primitivos convencionales. Una función puede ser guardada en una variable, pasada como argumento a otra función, retornada como resultado, e incluso construida dinámicamente en tiempo de ejecución. Este principio es la piedra angular que permite la existencia de funciones de orden superior y las lambdas.


## 4. Explica la sintaxis básica de una función lambda en Java.

### Respuesta

En Java, la sintaxis básica de una función lambda se divide en tres componentes esenciales: la lista de parámetros, el operador "flecha" (`->`) y el cuerpo de la expresión. Su estructura general es `(parametros) -> expresion` o `(parametros) -> { bloque de codigo; }`.

Los parámetros se especifican entre paréntesis, separados por comas. El compilador de Java posee "inferencia de tipos", lo que significa que a menudo es innecesario declarar el tipo explícito de los parámetros; el compilador deduce el tipo basándose en el contexto. Además, si la función lambda acepta un único parámetro y su tipo es inferido, se pueden omitir los paréntesis que lo envuelven.

En cuanto al cuerpo, si la lambda se limita a una única línea de código y evalúa una sola expresión, no requiere llaves (`{}`) ni la palabra reservada `return`, ya que el resultado se devuelve de manera implícita. Por el contrario, si la lógica requiere múltiples líneas, asignaciones o cálculos complejos, es imperativo encerrar el cuerpo entre llaves y, si debe devolver un valor, utilizar un `return` explícito.


## 5. Ahora recibamos una función como parámetro a un método y la llamaremos desde dentro. Amplia los ejemplos anteriores de Java y JavaScript con un método llamado `transformar`, que reciba un `String` como parámetro y luego una función transformadora como lo es `aMayúsculas` y la invoque desde dentro.

### Respuesta

Cuando un método está diseñado para recibir una función como parámetro, se habla de una función de "orden superior" (Higher-Order Function). Este mecanismo fomenta la creación de funciones esqueleto que ejecutan lógicas comunes —como preparar el entorno— pero que delegan el detalle específico del procesamiento al comportamiento (la función lambda) que se inyecta desde el exterior.

```javascript
// Ejemplo ampliado en JavaScript
function transformar(cadena, funcionTransformadora) {
    // Invocación a la función inyectada
    return funcionTransformadora(cadena);
}

const aMayusculas = (cad) => cad.toUpperCase();
console.log(transformar("hola js", aMayusculas));
```

```java
// Ejemplo ampliado en Java
import java.util.function.Function;

public class Main {
    // Método que recibe el dato y el comportamiento (Function)
    public static String transformar(String cadena, Function<String, String> funcionTransformadora) {
        // Se invoca el método apply() de la interfaz funcional
        return funcionTransformadora.apply(cadena);
    }

    public static void main(String[] args) {
        Function<String, String> aMayusculas = (cad) -> cad.toUpperCase();
        System.out.println(transformar("hola java", aMayusculas));
    }
}
```


## 6. Ahora, invoca `transformar`, con una nueva función lambda directamente en la llamada a `transformar`, por ejemplo, una función lambda que invierta la cadena. Define la función de inversión justo cuando la estás pasando como parámetro.

### Respuesta

Una de las mayores virtudes de las funciones lambda es la capacidad de definir comportamiento transitorio y de un solo uso en el momento exacto en el que es requerido. Esto evita saturar el diseño de la aplicación con la creación de múltiples métodos, clases o variables con nombres artificiales que solo se van a llamar una única vez en el sistema.

En el siguiente ejemplo, la invocación al método `transformar` prescinde de variables intermedias. Durante la misma llamada al método, en el espacio reservado para el parámetro de la función transformadora, se inyecta "al vuelo" una expresión lambda encargada de invertir los caracteres de la cadena suministrada.

```java
public class Main {
    public static String transformar(String cadena, java.util.function.Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(cadena);
    }

    public static void main(String[] args) {
        // Definición de la función anónima (lambda) directamente en los parámetros
        String invertida = transformar("roma", (cadena) -> {
            return new StringBuilder(cadena).reverse().toString();
        });
        
        System.out.println("Cadena invertida: " + invertida); // Mostrará: amor
    }
}
```


## 7. ¿Qué se entiende por cierre o "closure" en el contexto de las funciones lambda? Pon un ejemplo en Java de cómo una función lambda es capaz de acceder a una variable local en el contexto donde fue definida. Modifica el ejemplo anterior, creando otra función lambda para transformar una cadena, pero que lo que haga es concatenar a la cadena de entrada otra cadena que está en una variable local definida fuera de la función lambda.

### Respuesta

Un cierre (closure) es un mecanismo del lenguaje que permite a una función lambda "capturar" y recordar el estado léxico del entorno en el que fue declarada, incluso después de que el ámbito o bloque de código original haya terminado su ejecución. Esto significa que la función lambda tiene acceso y puede manipular o utilizar variables locales que residen en el contexto exterior que la rodea.

En el caso particular de Java, existen restricciones de diseño para este mecanismo. Para asegurar el determinismo y la seguridad en programación concurrente, cualquier variable local que una lambda pretenda capturar desde su contexto englobante debe ser explícitamente declarada como `final`, o bien comportarse como tal (lo que se conoce como "efectivamente final"). Si el programador intentase reasignar un nuevo valor a dicha variable externa después de definir la lambda, el compilador emitiría un error de sintaxis inmediatamente.

```java
import java.util.function.Function;

public class Main {
    public static String transformar(String cadena, Function<String, String> funcionTransformadora) {
        return funcionTransformadora.apply(cadena);
    }

    public static void main(String[] args) {
        // Variable local en el ámbito exterior a la lambda
        String sufijoEfectivamenteFinal = " (procesado)";

        // La lambda hace una 'closure' capturando la variable externa 'sufijoEfectivamenteFinal'
        Function<String, String> concatenarSufijo = (cadena) -> cadena + sufijoEfectivamenteFinal;

        String resultado = transformar("Archivo", concatenarSufijo);
        System.out.println(resultado); // Muestra: Archivo (procesado)
    }
}
```


## 8. Reflexiona: ¿en qué se diferencia entonces una función lambda de los punteros a funciones que hay en C?

### Respuesta

Aunque superficialmente las funciones lambda y los punteros a funciones en C parecen resolver el mismo problema (pasar comportamientos de un lado a otro), difieren fundamentalmente por el concepto de los cierres léxicos (*closures*).

Un puntero a función en C es estrictamente un apuntador crudo a un bloque de instrucciones en memoria. Cuando el programa "salta" a esa dirección, dicho código carece de cualquier contexto o memoria del entorno exterior donde se generó el puntero; todo el estado que requiera la función para operar debe ser pasado obligatoriamente de forma manual a través de sus argumentos.

En profundo contraste, una función lambda (acompañada de su closure) no es únicamente una referencia de código. Conceptualmente, es un paquete empaquetado que contiene tanto el bloque de código a ejecutar, como una copia oculta del estado capturado y referenciado (las variables locales atrapadas del entorno). Esta simbiosis de código y estado encapsulado proporciona una enorme abstracción frente al enfoque primitivo de los lenguajes de bajo nivel.


## 9. Devolvamos ahora funciones. Creemos ahora una función que sea capaz de crear funciones "descuento". Una función "descuento", decrementa un porcentaje pasado como parámetro. Por simplicidad, usa `Function<Double, Double>` para su tipo. La función `crearDescuento(porcentaje)`, recibe solo el porcentaje de descuento a aplicar y devuelve la función de descuento. Prueba a crear dos descuentos distintos y aplicarlos a una cantidad. Explica la closure en la función descuento.

### Respuesta

Devolver funciones permite construir "fábricas de comportamientos". En lugar de tener un único método para calcular un descuento, se puede diseñar un generador que, recibiendo un parámetro configurador, devuelve una función especializada lista para usarse. Esta capacidad es central en programación funcional para técnicas como *Currying* o aplicación parcial.

```java
import java.util.function.Function;

public class Main {
    // Método que actúa como fábrica de funciones lambda
    public static Function<Double, Double> crearDescuento(double porcentaje) {
        // Retorna una lambda que captura el parámetro 'porcentaje'
        return (precioOriginal) -> precioOriginal - (precioOriginal * (porcentaje / 100.0));
    }

    public static void main(String[] args) {
        // Se crean dos funciones distintas encapsulando diferentes cierres (closures)
        Function<Double, Double> descuentoBlackFriday = crearDescuento(50.0);
        Function<Double, Double> descuentoSocio = crearDescuento(10.0);

        double precioBase = 120.0;
        System.out.println("Black Friday: " + descuentoBlackFriday.apply(precioBase)); // 60.0
        System.out.println("Socio: " + descuentoSocio.apply(precioBase)); // 108.0
    }
}
```

El mecanismo de *closure* (cierre léxico) aquí es determinante y fascinante. Cuando el método `crearDescuento` se invoca y finaliza, su parámetro local `porcentaje` teóricamente debería ser destruido en la pila (stack) de ejecución y desaparecer. Sin embargo, dado que la función lambda retornada hace uso interno de dicha variable, Java aplica un cierre léxico. Empaqueta el estado de ese parámetro `porcentaje` de forma inmutable y lo adhiere de por vida a la instancia funcional devuelta, garantizando que cuando la lambda sea ejecutada horas o contextos después, siga conservando acceso impecable al porcentaje con el que nació.


## 10. En Java, que es un lenguaje con comprobación estática de tipos, donde los tipos se declaran, toda función lambda tiene un tipo, que se conoce como **interfaz funcional**. ¿Qué es una **interfaz funcional**? ¿Qué requisitos tiene?

### Respuesta

En el ecosistema fuertemente tipado de Java, no existe un "tipo función" puro de bajo nivel. Por consiguiente, para que el compilador sea capaz de gestionar, alojar y resolver el tipado estricto de las funciones lambda introducidas en Java 8, se ideó el concepto de "Interfaz Funcional".

Una interfaz funcional es, por definición absoluta, una interfaz convencional de Java que contiene un único, y solo un, método abstracto (SAM, por sus siglas en inglés: Single Abstract Method). Los métodos con la palabra reservada `default`, estáticos o métodos sobreescritos pertenecientes a `Object` no interfieren; mientras que la interfaz obligue formalmente a implementar exclusivamente un contrato abstracto, será reconocida y tratada por el compilador como interfaz funcional.

El funcionamiento interno consiste en que cuando el compilador detecta la definición de una expresión lambda, infiere su firma y elabora internamente una instancia anónima o adaptación estructural compatible con el único método de la interfaz funcional objetivo. La anotación opcional `@FunctionalInterface` sirve para advertir y blindar la interfaz: si en el futuro otro desarrollador intentase añadir indebidamente un segundo método abstracto, el código no compilará, protegiendo su estatus.


## 11. Creemos una interfaz funcional a mano. Por ejemplo, define la interfaz funcional del ejemplo que transforma la cadena en otra. Llámale `Transformador`, que define una función que convierte una cadena de texto (`String`) en otra (`String`).

### Respuesta

Crear una interfaz funcional personalizada permite bautizar contratos semánticos explícitos orientados netamente al dominio del problema, resultando habitualmente en un código más legible para desarrolladores ajenos a las librerías preestablecidas del lenguaje.

Para el ejemplo propuesto, la interfaz demanda un solo contrato abstracto enfocado a recibir texto y devolver texto. A partir de ese momento, la interfaz puede utilizarse libremente como tipo para variables que alberguen lambdas.

```java
@FunctionalInterface
public interface Transformador {
    // Único método abstracto de la interfaz funcional (SAM)
    String transformar(String entrada);
}

public class Main {
    public static void main(String[] args) {
        // La lambda cumple con el contrato del método 'transformar'
        Transformador aMayusculas = (cad) -> cad.toUpperCase();
        
        System.out.println(aMayusculas.transformar("ejemplo manual"));
    }
}
```


## 12. Ahora hagamos la interfaz funcional algo más genérica y empleando generics, para que permita definir un `Transformador` de un tipo en otro. Pon un ejemplo de un transformador que redondea un `Double` en un `Integer`.

### Respuesta

Al inyectar "Generics" (parametrización de tipos) en la definición de una interfaz funcional, su utilidad sufre una expansión monumental. Se suprime el rígido acoplamiento a un tipo de dato duro (como el `String`) y se instaura una plantilla genérica utilizable para infinidad de conversiones lógicas dispares, evitando polucionar la base de código con decenas de interfaces distintas.

Mediante la definición de dos tipos genéricos, digamos `<T>` para simbolizar la Entrada (Type) y `<R>` para estipular el Retorno (Return), el contrato abstracto logra abarcar cualquier metamorfosis.

```java
@FunctionalInterface
public interface TransformadorGenerico<T, R> {
    // La firma acepta un tipo T indeterminado y debe retornar un tipo R
    R transformar(T entrada);
}

public class Main {
    public static void main(String[] args) {
        // Definición de un Transformador de Double a Integer
        TransformadorGenerico<Double, Integer> redondeador = (numero) -> (int) Math.round(numero);
        
        Integer resultado = redondeador.transformar(5.7);
        System.out.println("Redondeo: " + resultado); // Imprime 6
    }
}
```


## 13. `Transformador`, en su versión genérica, parece muy útil y reutilizable, hasta el punto de que es igual a una interfaz funcional que ya hay, que es `Function<T, R>`. Muestra las interfaces funcionales predefinidas que hay en Java.

### Respuesta

Para estandarizar el desarrollo y evitar la proliferación y reinvención constante de interfaces funcionales genéricas estructuralmente idénticas (como el `TransformadorGenerico`), Java 8 proveyó un extenso y curado catálogo de interfaces estándar dentro del paquete `java.util.function`.

Estas interfaces representan familias de operaciones abstractas. Se distinguen principalmente por las firmas de sus métodos de entrada y retorno. Las más emblemáticas son:
- `Function<T, R>`: Representa una función tradicional de transformación que recibe un argumento `T` y devuelve un resultado `R`. Su método es `apply()`.
- `Consumer<T>`: Representa una operación que acepta un argumento `T` pero no devuelve ningún resultado (efecto secundario puro, ideal para imprimir o mutar registros). Su método es `accept()`.
- `Supplier<T>`: Representa un proveedor que no recibe ningún argumento, pero genera o proporciona un valor del tipo `T`. Su método es `get()`.
- `Predicate<T>`: Representa una operación booleana que evalúa y filtra una condición sobre el argumento `T`, devolviendo un primitivo `boolean`. Su método es `test()`.


## 14. Vamos a ver ejemplos expresivos de funcional en Java. Estudiemos el `List.forEach`, como versión funcional del bucle `for`. Emplea el `forEach` para recorrer una lista de `Integer` y que muestre un mensaje si el entero es positivo.

### Respuesta

El método `forEach` incluido en las colecciones de Java es la materialización de lo que se conoce como "iteración interna". En un bucle `for` tradicional, es el programador quien dicta y controla externamente y de forma manual el progreso de los índices y las comprobaciones sobre el vector.

Con iteración interna, la estructura de datos se responsabiliza en exclusiva de navegar internamente a través de sus propios elementos. El programador simplemente se limita a suministrarle una operación `Consumer` (una función lambda) que especifica "qué acción" desea que se le ejecute e inflija individualmente a cada uno de los elementos iterados.

```java
import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<Integer> numeros = Arrays.asList(-3, 10, -5, 20, 0);

        // Bucle funcional forEach donde se transfiere el comportamiento (lambda)
        numeros.forEach((numero) -> {
            if (numero > 0) {
                System.out.println("Número positivo: " + numero);
            }
        });
    }
}
```

## 15. Repasando el tema de genericidad, fíjate en la firma de `forEach`, ¿por qué se usa `Consumer<? super T>` y no `Consumer<T>`? Explica qué significa **PECS**, y explícalo para el caso de mejorar el ejemplo del método `transformar` la hora de definir el tipo de la función transformadora.

### Respuesta

El acrónimo PECS (Producer Extends, Consumer Super) es la regla mnemotécnica o principio arquitectónico ideado en Java para resolver cuándo usar la varianza genérica (comodines) apropiada. Establece que cuando un objeto se dedica a "producir" o proporcionar datos al sistema, debe declararse con límites superiores (`? extends T`, que provee covarianza para lectura segura); y cuando un objeto se encarga de "consumir" o tragarse datos que el sistema le envía, debe definirse con límites inferiores (`? super T`, proveyendo contravarianza para escritura segura).

El método `forEach` exige explícitamente un `Consumer<? super T>` debido a este último factor. Si poseemos una lista de números de tipo `List<Integer>`, el método `forEach` va a suministrarle elementos `Integer` a la función que lo recorra (la función "consume" esos enteros). Gracias al comodín `? super`, no estamos obligados a pasarle restrictivamente una función estrictamente `Consumer<Integer>`, sino que somos libres de pasarle también un `Consumer<Number>` genérico o un `Consumer<Object>`, ya que ambos son antecesores seguros capaces de ingerir enteros sin violar las normas de herencia.

```java
import java.util.function.Function;

public class Utilidades {
    // Aplicando el principio PECS a nuestro método transformar:
    // El 'transformador' CONSUME el String (entrada) -> ? super String
    // El 'transformador' PRODUCE el String de salida -> ? extends String
    public static String transformarMejorado(String cadena, 
            Function<? super String, ? extends String> funcionTransformadora) {
            
        return funcionTransformadora.apply(cadena);
    }
}
```

## 16. Referencias a métodos. Podemos obtener una referencia a métodos de objetos o clases. Pon un ejemplo en JavaScript y en Java, de una clase `Persona` con un método `saludar`. En el código principal, crea una `Persona` con un nombre, y obtén una referencia a su método `saludar` en una variable local. Invoca `saludar` con esa referencia a su método `saludar`.

### Respuesta

Las referencias a métodos (Method References) son un edulcorante o azúcar sintáctico de alto nivel que permite nombrar directamente, y tratar como funciones u objetos puros, a métodos que ya han sido previamente programados y declarados con nombre. 

Cuando la única acción interna de una función lambda anónima es hacer de simple envoltorio para la invocación a un método existente y delegarle los parámetros, se considera una redundancia superflua. Las referencias a métodos simplifican y embellecen la sintaxis, reemplazando la declaración lambda por un puntero conceptual al método pertinente.

```javascript
// Ejemplo en JavaScript
class PersonaJS {
    constructor(nombre) { this.nombre = nombre; }
    saludar() { console.log(`Hola, soy ${this.nombre}`); }
}

const pJS = new PersonaJS("Carlos");
// JS requiere forzar el contexto 'this' con bind, o usar lambdas, para no perder referencia
const referenciaSaludarJS = pJS.saludar.bind(pJS); 

referenciaSaludarJS(); // Ejecuta el saludo apuntado
```

```java
// Ejemplo en Java
public class PersonaJava {
    private String nombre;
    public PersonaJava(String nombre) { this.nombre = nombre; }
    public void saludar() { System.out.println("Hola, soy " + this.nombre); }

    public static void main(String[] args) {
        PersonaJava pJava = new PersonaJava("Marta");

        // Referencia directa al método de instancia de un objeto específico
        Runnable referenciaSaludarJava = pJava::saludar;

        // Invocación a través de la interfaz funcional (sin lambda manual)
        referenciaSaludarJava.run();
    }
}
```


## 17. ¿Qué tipos de referencias a método se pueden hacer en Java? Pon un ejemplo de referencia a método estático, a constructor, a método de instancia de una instancia concreta y a método de instancia sobre cualquier instancia.

### Respuesta

En el lenguaje Java existen estrictamente cuatro formas categóricas de crear y direccionar referencias a métodos empleando el operador cuádruple punto (`::`), las cuales difieren de acuerdo al tipo y ubicación estructural del método apuntado y de dónde o cómo el compilador inyectará los argumentos de manera implícita.

1. **Referencia a un método estático (`Clase::metodoEstatico`)**: Sirve como alias directo a la función de clase. Por ejemplo, `Math::max` equivale a la lambda `(a, b) -> Math.max(a, b)`.
2. **Referencia a un constructor (`Clase::new`)**: Actúa como un atajo a una factoría. Permite que interfaces como un `Supplier` generen instancias vacías directamente, como `ArrayList::new` que reemplaza a `() -> new ArrayList<>()`.
3. **Referencia a un método de instancia de un objeto particular (`instancia::metodo`)**: Captura o hace *bind* sobre un objeto previamente creado. Si `p` es un objeto `Persona`, `p::saludar` anula los argumentos y engloba automáticamente el contexto de `p`, similar a `() -> p.saludar()`.
4. **Referencia a un método de instancia sobre cualquier objeto (`Clase::metodo`)**: El primer argumento introducido por la interfaz funcional proveerá dinámicamente el objeto sobre el que se invoca el método. Por ejemplo, `String::toUpperCase` se mapeará sobre una lambda del tipo `(cadena) -> cadena.toUpperCase()`.


## 18. Otro ejemplo expresivo. Ordena una lista de `Persona`, cada persona tiene un nombre y una edad (de tipo entero). Ordena la lista de `Persona` con `Collections.sort`, pasándole como comparador una expresión lambda que compare la edad de ambas personas y si tienen la misma edad, se ordene por orden alfabético del nombre. Crea dos versiones: Una con la función de comparación hecha manualmente, y otra empleando `Comparator`.

### Respuesta

El ordenamiento de colecciones compuestas por objetos de dominio demanda lógicas de comparación profundas y prioridades específicas que la interfaz o colección no puede inferir mágicamente. La interfaz de librería `Comparator` estipula el mecanismo estandarizado para inyectar dichas reglas mediante funciones (cuyo contrato exige retornar valores negativos, positivos o nulos relativos al orden posicional que dictamina de dos objetos).

Aunque es perfectamente legal confeccionar un bloque de código masivo interno dentro de una expresión lambda para gobernar un ordenamiento multinivel (edad y luego nombre), se trata de una aproximación verbosa. La elegancia de las interfaces funcionales modernas radica en los métodos *default* auxiliares, los cuales transforman el problema de la ordenación compleja en un ensamblaje composicional de referencias a métodos, produciendo código declarativo de lectura asombrosamente humana.

```java
import java.util.*;

class Persona {
    private String nombre;
    private int edad;

    public Persona(String nombre, int edad) { this.nombre = nombre; this.edad = edad; }
    public String getNombre() { return nombre; }
    public int getEdad() { return edad; }
    @Override public String toString() { return nombre + "(" + edad + ")"; }
}

public class Main {
    public static void main(String[] args) {
        List<Persona> personas = Arrays.asList(
            new Persona("Luis", 30), new Persona("Ana", 25), new Persona("Carlos", 25)
        );

        // Versión 1: Lógica manual verbosa empleando funciones lambda en bruto
        List<Persona> lista1 = new ArrayList<>(personas);
        Collections.sort(lista1, (p1, p2) -> {
            int comparacionEdad = Integer.compare(p1.getEdad(), p2.getEdad());
            if (comparacionEdad == 0) {
                return p1.getNombre().compareTo(p2.getNombre());
            }
            return comparacionEdad;
        });

        // Versión 2: Lógica declarativa elegante encadenando métodos del Comparator
        List<Persona> lista2 = new ArrayList<>(personas);
        Collections.sort(lista2, Comparator.comparingInt(Persona::getEdad)
                                           .thenComparing(Persona::getNombre));

        System.out.println(lista2); // [Ana(25), Carlos(25), Luis(30)]
    }
}
```
