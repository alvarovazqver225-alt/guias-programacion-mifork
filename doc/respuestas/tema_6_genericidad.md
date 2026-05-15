<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Genericidad". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: clases y objetos, encapsulación, excepciones, composición, herencia y polimorfismo.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 6. Genericidad

## 1. Empleando `void*` en C o `Object` en Java, pon un ejemplo de una estructura de datos, que empleando un array primitivo, permita alojar cualquier tipo de dato.

### Respuesta

En lenguajes como C o Java, antes de la existencia de mecanismos avanzados de genericidad, la forma tradicional de crear estructuras de datos capaces de almacenar cualquier tipo era recurrir a punteros genéricos (`void*` en C) o a la clase raíz suprema (`Object` en Java). Dado que cualquier puntero puede convertirse a `void*` y cualquier objeto en Java hereda de `Object`, estas referencias actúan como comodines universales.

El código muestra cómo un array de tipo `Object` permite construir un contenedor genérico básico. Al declarar el array interno y los parámetros de los métodos utilizando `Object`, la estructura pierde conocimiento sobre el tipo específico de dato que está albergando. 

De esta manera, una misma instancia de esta clase podría almacenar simultáneamente enteros (autoboxing a `Integer`), cadenas de texto (`String`), o cualquier otro objeto complejo, logrando la ansiada flexibilidad de uso para diferentes escenarios sin necesidad de duplicar el código del contenedor.

```java
// Ejemplo en Java
public class ArrayGenericoBasico {
    private Object[] elementos;
    private int tamaño;

    public ArrayGenericoBasico(int capacidad) {
        this.elementos = new Object[capacidad];
        this.tamaño = 0;
    }

    public void añadir(Object elemento) {
        if (tamaño < elementos.length) {
            elementos[tamaño++] = elemento;
        }
    }

    public Object obtener(int indice) {
        return elementos[indice];
    }
}
```

## 2. Brevemente, ¿Qué significa la **programación genérica**? ¿Es el ejemplo anterior un ejemplo básico de programación genérica? 

### Respuesta

La programación genérica es un paradigma o estilo de programación que se centra en escribir algoritmos y estructuras de datos de manera independiente a los tipos concretos sobre los que van a operar. El objetivo es maximizar la reutilización del código, permitiendo que una misma implementación lógica (como un método de ordenación o una lista enlazada) pueda aplicarse de forma segura y eficiente sobre múltiples tipos de datos diferentes sin necesidad de reescribirla.

El ejemplo anterior, utilizando `void*` en C o `Object` en Java, constituye una forma primitiva y rudimentaria de programación genérica. Ciertamente cumple con el objetivo fundamental de reutilizar la misma estructura de datos para cualquier tipo de elemento.

Sin embargo, en la terminología moderna, cuando se habla de "programación genérica" se suele hacer referencia a lenguajes y mecanismos que logran esta flexibilidad preservando estrictamente la seguridad de tipos (como los *generics* en Java o los *templates* en C++), característica que las soluciones basadas en `void*` u `Object` no poseen en absoluto.

## 3. Indica los problemas respecto al chequeo de tipos, de emplear `void*` o `Object` cuando se crean estructuras de datos genéricas. 

### Respuesta

El problema principal de utilizar enfoques basados en `void*` u `Object` es la pérdida total del chequeo de tipos estático (en tiempo de compilación). Al insertar un elemento en la estructura, el compilador "olvida" el tipo original del dato. 

Esto genera dos inconvenientes graves. En primer lugar, al recuperar el elemento, el contenedor devuelve una referencia genérica. Para poder utilizar las operaciones específicas del objeto (como llamar a métodos propios de un `String`), el programador está forzado a realizar conversiones de tipo explícitas (*casting* en C, *downcasting* en Java), lo cual enguarra el código y añade carga mental.

En segundo lugar, se destruye la seguridad en tiempo de compilación (type safety). Si se crea una lista destinada a contener solo `String`, el compilador no alertará si por error se inserta un número entero, ya que ambos son compatibles con `Object`. El error permanecerá oculto y solo se manifestará en tiempo de ejecución como un fallo catastrófico (como un `ClassCastException` en Java) cuando se intente recuperar el número asumiendo erróneamente que era una cadena.


## 4. Vamos entonces con mecanismos de mejora de la programación genérica ¿Qué son los **parámetros de tipo**? 

### Respuesta

Los parámetros de tipo son el mecanismo moderno diseñado para superar las limitaciones de las colecciones basadas en `Object` o `void*`. Permiten definir clases, interfaces o métodos dejando como incógnita ("parametrizado") uno o varios de los tipos de datos que van a utilizar en su interior. Estos parámetros suelen representarse convencionalmente con letras mayúsculas encerradas entre corchetes angulares, como `<T>` (Type), `<E>` (Element) o `<K, V>` (Key, Value).

La idea subyacente es que el tipo exacto se pospone hasta el momento en que se instancia la clase o se invoca el método. Cuando el programador crea un objeto de una clase parametrizada, proporciona un "argumento de tipo" real (por ejemplo, `String`), sustituyendo de manera virtual todas las apariciones del parámetro `<T>` dentro de esa clase por el tipo concreto proporcionado.

Este enfoque devuelve el control al compilador. Al conocer de antemano el tipo específico que albergará la estructura, el compilador puede verificar que todas las inserciones sean correctas, bloqueando datos incompatibles en tiempo de compilación, y garantiza que las extracciones se realicen con el tipo preciso, eliminando por completo la necesidad de conversiones explícitas (downcasting) y asegurando la integridad del programa.


## 5. En Java existe "generics", en C++ existen "templates". Pon un ejemplo de uso de programación genérica en ambos, instanciando una lista o vector dinámico que solo admite `String`. Introduce valores, y luego haz un recorrido de ellos mostrando cómo cada elemento es del tipo concreto con seguridad.

### Respuesta

La programación genérica moderna se plasma mediante los "templates" en C++ y los "generics" en Java. Ambos mecanismos, aunque internamente diferentes, ofrecen una sintaxis muy similar a nivel de usuario, permitiendo declarar estructuras fuertemente tipadas en tiempo de compilación y evitar el *downcasting*.

```java
// Ejemplo en Java usando Generics
import java.util.ArrayList;
import java.util.List;

public class EjemploJava {
    public static void main(String[] args) {
        // Se instancia con el parámetro de tipo String
        List<String> lista = new ArrayList<>();
        
        lista.add("Hola");
        lista.add("Mundo");
        // lista.add(42); // Error de compilación inmediato
        
        for (String texto : lista) {
            // Se recupera como String directamente, sin downcasting
            System.out.println(texto.toUpperCase());
        }
    }
}
```

```cpp
// Ejemplo en C++ usando Templates
#include <iostream>
#include <vector>
#include <string>

int main() {
    // Se instancia el vector indicando el tipo std::string
    std::vector<std::string> lista;
    
    lista.push_back("Hola");
    lista.push_back("Mundo");
    // lista.push_back(42); // Error de compilación inmediato
    
    for (const std::string& texto : lista) {
        // Operamos con el tipo exacto de forma segura
        std::cout << texto << std::endl;
    }
    return 0;
}
```


## 6. Sobre el funcionamiento de la programación genérica. ¿Qué hace el compilador cuando se instancia una clase que tiene parámetros de tipo? ¿Hace lo mismo C++ y Java? ¿Qué es el "type erasure" de Java y la "instanciación de plantillas" de C++?

### Respuesta

Aunque C++ y Java logran el mismo nivel de seguridad de tipos para el programador, lo hacen empleando enfoques de compilación diametralmente opuestos en el procesamiento interno de los parámetros de tipo.

En C++, el mecanismo se denomina "instanciación de plantillas" (template instantiation). Cuando el compilador encuentra, por ejemplo, un `vector<string>` y un `vector<int>`, genera en el código binario final dos clases completamente independientes y separadas, creando una copia a medida del código de la plantilla sustituyendo la `<T>` por el tipo real. Esto favorece la ejecución optimizada (sin sobrecarga en tiempo de ejecución), pero puede incrementar notablemente el tamaño del archivo ejecutable (fenómeno conocido como "code bloat").

En cambio, Java utiliza una técnica denominada "borrado de tipos" (type erasure). El compilador emplea la información genérica (`<T>`) única y exclusivamente para verificar la coherencia de los tipos durante la compilación. Una vez validado el código, borra todas las referencias genéricas y compila la clase como si internamente utilizara `Object`. Para las operaciones de extracción, el compilador inyecta de forma invisible los *castings* automáticos necesarios. En consecuencia, en tiempo de ejecución (bytecode) solo existe una única clase en memoria, independientemente de cuántos tipos distintos se hayan instanciado, ahorrando memoria y garantizando compatibilidad con versiones antiguas del lenguaje.


## 7. Vamos a crear una nueva clase con parámetros de tipo. Define en Java una clase `Par`, que permite alojar dos valores de tipos diferentes. Incluye un constructor y un getter para cada tipo. Pon un ejemplo de uso de ese `Par`, por ejemplo para especificar el tipo de retorno de una función que devuelve en un `Par` la media y desviación típica de un array de `double`. 

### Respuesta

La creación de clases con múltiples parámetros de tipo es extremadamente útil para agrupar datos dispares sin tener que crear una clase específica para cada combinación concebible en el sistema.

El siguiente código muestra cómo se define un contenedor `Par` que acepta dos tipos genéricos, nombrados arbitrariamente como `T` y `U`. Gracias a esto, el objeto instanciado es capaz de almacenar y retornar elementos fuertemente tipados sin recurrir en ningún momento a la clase base `Object`.

```java
// Clase genérica con dos parámetros de tipo
public class Par<T, U> {
    private final T primero;
    private final U segundo;

    public Par(T primero, U segundo) {
        this.primero = primero;
        this.segundo = segundo;
    }

    public T getPrimero() { return primero; }
    public U getSegundo() { return segundo; }
}

public class AnalisisDatos {
    // Uso del Par para devolver dos tipos distintos de forma tipada
    public static Par<Double, Double> calcularEstadisticas(double[] datos) {
        double media = 5.5; // (Simulando cálculo de media)
        double desviacion = 1.2; // (Simulando cálculo de desviación)
        
        return new Par<>(media, desviacion);
    }
    
    public static void main(String[] args) {
        Par<Double, Double> resultados = calcularEstadisticas(new double[]{1, 2, 3});
        System.out.println("Media: " + resultados.getPrimero());
        System.out.println("Desviación: " + resultados.getSegundo());
    }
}
```


## 8. En Java, se pueden declarar parámetros de tipo también a nivel de método, no solo a nivel de clase. Pon un ejemplo con un método genérico `seleccionaUno`, que pasados dos objetos del mismo tipo, te devuelva aleatoriamente uno de ellos. Muestra la diferencia de definirlo con dos `Object`, a definirlo con dos parámetros de tipo, en terminos de (i) evitar downcasting y (ii) forzar que ambos objetos sean del mismo tipo. 

### Respuesta

Los métodos genéricos permiten introducir parametrización de tipos a nivel individual sin necesidad de que la clase contenedora entera sea genérica. Se definen indicando el parámetro de tipo `<T>` justo antes del tipo de retorno en la firma del método.

Si el método `seleccionaUno` se definiera aceptando dos parámetros `Object`, el compilador permitiría pasar dos objetos de naturalezas completamente incompatibles (ej. un `String` y un `Integer`). Al retornar la selección, el resultado sería siempre un `Object`, forzando al usuario a realizar un peligroso *downcasting* manual, con la consecuente incertidumbre sobre el tipo real devuelto.

Por el contrario, al definirlo como un método genérico utilizando `<T>`, el compilador examina ambos argumentos al invocar la función e infiere el tipo común más cercano. Esto fuerza de manera automática que ambos objetos pertenezcan a ramas de herencia compatibles y garantiza que el tipo de retorno coincida exactamente con el de los parámetros enviados, eliminando el *casting* por completo.

```java
public class Sorteo {
    // Alternativa con Object: Insegura y requiere downcasting posterior
    public Object seleccionaUnoObject(Object a, Object b) {
        return Math.random() > 0.5 ? a : b;
    }

    // Alternativa con Generics: Forzada la compatibilidad y retorno seguro
    public <T> T seleccionaUno(T a, T b) {
        return Math.random() > 0.5 ? a : b;
    }
    
    public void prueba() {
        // Uso seguro y tipado directo sin cast
        String ganador = seleccionaUno("Candidato A", "Candidato B");
        
        // Error de compilación si se intenta mezclar tipos incompatibles
        // Integer imposible = seleccionaUno(42, "Texto"); 
    }
}
```


## 9. ¿Se pueden establecer restricciones en los parámetros de tipo? Por ejemplo, si quiero definir un tipo genérico `<T>`, ¿puedo decir que tenga que ser, al menos, un número para poder tratarlo como tal? Pon un ejemplo en Java de un `Punto` con dos coordenadas, metodos `getX`, `getY`, y una función `calcularDistanciaA` otro `Punto`. Permite que esas coordenadas sean cualquier tipo de número. Pon dos soluciones: una simplemente creando coordenadas de tipo `Number` y otra añadiendo generics para reforzar el chequeo de tipos y saber exactamente con qué tipo de número trabaja el `Punto`. En este caso y respecto al "type erasure", ¿cuál es el tipo final tras la compilación?

### Respuesta

La genericidad permite establecer límites (bounded type parameters) para restringir la familia de tipos aceptados. Mediante la palabra clave `extends` aplicada al parámetro de tipo, se obliga a que el tipo concreto proporcionado herede necesariamente de una determinada clase superior o implemente una interfaz específica (por ejemplo, `<T extends Number>`).

A continuación se presentan dos soluciones. La primera renuncia a la genericidad apoyándose en la herencia convencional: utiliza la superclase `Number` como tipo de las coordenadas. La segunda emplea genericidad restringida, lo que garantiza que las coordenadas del punto pertenezcan a un tipo numérico específico conocido en el momento de instanciar la clase (como `Punto<Double>`).

Respecto al borrado de tipos ("type erasure"), en la solución con *generics* el tipo final resultante en el bytecode, tras la compilación, dejará de ser la representación universal genérica (Object). Al haber especificado el límite `extends Number`, el compilador sustituirá internamente todas las ocurrencias de `T` directamente por la clase tope de la restricción, es decir, la clase abstracta `Number`.

```java
// Solución 1: Sin generics, usando herencia básica
public class PuntoTradicional {
    private final Number x, y;

    public PuntoTradicional(Number x, Number y) {
        this.x = x; this.y = y;
    }
    public Number getX() { return x; }
    public Number getY() { return y; }
}

// Solución 2: Con generics restringidos
public class PuntoGenerico<T extends Number> {
    private final T x, y;

    public PuntoGenerico(T x, T y) {
        this.x = x; this.y = y;
    }
    public T getX() { return x; }
    public T getY() { return y; }
}
```


## 10. Sobre las soluciones anteriores. Si bien ambas permiten trabajar con distintos tipos de número sin duplicar la clase `Punto`, reflexiona sobre el refuerzo del chequeo de tipos con generics. ¿Permiten ambas crear un punto con una coordenada de tipo entero y la otra coordenada de tipo real? ¿Qué tipo devuelve el `getX` con la solucion sin generics y qué tipo devuelve el que tiene la solución con generics?

### Respuesta

Ambas soluciones introducen limitaciones y ventajas dispares. En la primera aproximación (sin generics, basada en `Number`), al declararse los atributos genéricamente como `Number`, es perfectamente legal construir el punto enviando un `Integer` para la coordenada X y un `Double` para la coordenada Y, abriendo la puerta a puntos de tipo mixto. En esta versión, al invocar el método `getX`, el sistema retornará de forma inflexible un objeto del tipo base `Number`, forzando conversiones posteriores si se precisan cálculos precisos.

Por su parte, la solución fundamentada en genericidad (`PuntoGenerico<T extends Number>`) refuerza el chequeo de tipos y anula esta ambigüedad. Al instanciar un objeto como `PuntoGenerico<Double>`, el parámetro `T` se sella como `Double` para ambas coordenadas. En consecuencia, el compilador rechazará frontalmente cualquier intento de suministrar simultáneamente un entero y un real, blindando la uniformidad interna del objeto.

Asimismo, la principal ventaja del modelo genérico es la retención absoluta del tipo. Al invocar el método `getX()` sobre el `PuntoGenerico<Double>`, el valor retornado no será un `Number` abstracto, sino nítida y directamente un `Double`, evitando el desgaste operativo y los riesgos de errores asociados a un posterior casting manual.


## 11. Hagamos un ejemplo avanzado. El siguiente código, con interfaz `Punto`, que define un método `calcularDistanciaA(Punto p)`, junto con las implementaciones `Punto2D` y `Punto3D`. Añade generics para asegurarnos que la sobreescritura del método calcular distancia a otro `Punto` siempre es sobre un `Punto` del mismo tipo, evitando `instanceof` y el downcasting.
```java
public interface Punto { 
    public double distanciaA(Punto p); 
} 

public class Punto2D implements Punto { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto p) { 
        if (p instanceof Punto2D) { 
            Punto2D p2d = (Punto2D) p; 
            return Math.sqrt(Math.pow(x - p2d.x, 2) 
                    + Math.pow(y - p2d.y, 2)); 
        } else { 
            throw new RuntimeException("p debe ser Punto 2D"); 
        } 
    } 
} 
public class Punto3D implements Punto { 
    // Igual que Punto2D, pero con tres coordenadas
    ...
} 
```

### Respuesta

Este diseño arquitectónico, conocido en Java formalmente como un patrón de "tipo genérico autorreferenciado" (Self-Referencing Generic Type), permite forzar una sincronización absoluta entre la clase que implementa la interfaz y el parámetro del método abstracto. 

Al incluir el parámetro `<T extends Punto<T>>` en la interfaz base, se está obligando estructuralmente a que las subclases se inserten a sí mismas como argumento genérico al declarar la implementación (`implements Punto<Punto2D>`). Esto fuerza la redefinición estricta de la firma del método en la hija.

Gracias a este modelo, la clase `Punto2D` declara el parámetro receptor del método `distanciaA` directamente como `Punto2D` en lugar de un `Punto` genérico. El compilador pasa a asumir el blindaje, rechazando en compilación la introducción de un `Punto3D` y eliminando por consiguiente la obligación de incluir costosas ramificaciones lógicas (`instanceof`), proveyendo un entorno excepcionalmente limpio de `downcasting`.

```java
// La interfaz requiere recibir el mismo tipo que se implementa
public interface Punto<T extends Punto<T>> { 
    public double distanciaA(T p); 
} 

public class Punto2D implements Punto<Punto2D> { 
     private final double x, y; 
     public Punto2D(double x, double y) { 
        this.x = x; this.y = y; 
    } 

    @Override 
    public double distanciaA(Punto2D p) { 
        // Ya no es necesario 'instanceof' ni 'downcasting'.
        // El compilador garantiza que 'p' es de tipo Punto2D.
        return Math.sqrt(Math.pow(x - p.x, 2) + Math.pow(y - p.y, 2)); 
    } 
} 

public class Punto3D implements Punto<Punto3D> { 
    private final double x, y, z;
    public Punto3D(double x, double y, double z) {
        this.x = x; this.y = y; this.z = z;
    }

    @Override 
    public double distanciaA(Punto3D p) { 
        // Comprobación segura y directa
        return Math.sqrt(Math.pow(x - p.x, 2) + 
                         Math.pow(y - p.y, 2) + 
                         Math.pow(z - p.z, 2)); 
    } 
}
```


## 12. Dado que `String` es subtipo de `Object`, ¿significa eso que `List<String>` es subtipo de `List<Object>`? ¿Y que `String[]` es subtipo de `Object[]`? Razona por qué la respuesta es diferente en cada caso y qué problema en tiempo de ejecución puede aparecer con los arrays. A partir de estos ejemplos, define qué significa que un tipo genérico sea **covariante**, **contravariante** o **invariante** respecto a su parámetro de tipo.

### Respuesta

Aunque la herencia convencional dictamine que `String` es un subtipo válido de `Object`, en el sistema de tipos genéricos de Java, `List<String>` no es en absoluto un subtipo de `List<Object>`. Los tipos genéricos en Java son por defecto estrictamente **invariantes**. Esto se diseñó así por seguridad: si se permitiera tratar una `List<String>` como una `List<Object>`, alguien podría insertar un número entero en la lista a través de la referencia superior genérica, corrompiendo silenciosamente la colección original que solo esperaba cadenas.

A diferencia de las colecciones genéricas, los arrays en Java sí mantienen una herencia paralela al tipo que contienen. Por consiguiente, `String[]` sí es un subtipo legítimo de `Object[]`. Este comportamiento se denomina **covarianza**. Esta laxitud histórica genera problemas de seguridad en tiempo de ejecución: si una referencia de tipo `Object[]` apunta a un arreglo real de cadenas y se intenta almacenar en él un número, el compilador lo permitirá, pero la máquina virtual producirá una interrupción inmediata de la ejecución mediante una `ArrayStoreException`.

Estos dos ejemplos permiten entender el concepto de varianza de los tipos paramétricos. Un tipo es invariante si rechaza cualquier tipo de jerarquía ajena a la identidad estricta (`List<String>` solo acepta `List<String>`). Es covariante si la estructura contenedora preserva la dirección de la herencia de sus componentes (el array de `String` es subtipo del de `Object`). En último lugar, sería contravariante si invirtiera intencionalmente dicha dirección (donde el contenedor del supertipo operara como subtipo del contenedor derivado).


## 13. Java permite recuperar covarianza y contravarianza en tipos genéricos de forma controlada mediante **wildcards**. ¿Qué es un wildcard (`?`)? Muestra la diferencia entre `List<? extends T>` y `List<? super T>`, indicando en qué casos se usa cada uno. Pon dos ejemplos: (i) un método que reciba una lista de números y calcule su suma, usando `? extends`; (ii) un método que reciba una lista y le añada varios números enteros, usando `? super`.

### Respuesta

Un wildcard o "comodín" es un parámetro de tipo especial en Java que se representa con un símbolo de interrogación (`?`) e indica un "tipo desconocido". Se emplea específicamente para proveer flexibilidad y rescatar funcionalmente la covarianza y la contravarianza que los tipos invariantes prohíben por defecto.

El comodín con límite superior (`? extends T`) establece covarianza: la colección aceptará tipos concretos de la clase `T` o de cualquiera de sus herederas. Esto la transforma en una estructura de "solo lectura" segura, ideal para metodologías extractivas, porque aunque se ignora el tipo exacto, se garantiza que como mínimo posee los rasgos básicos de `T`. Inversamente, el comodín de límite inferior (`? super T`) induce contravarianza: exige clases que sean ancestros de `T`. Esta modalidad abre un canal de "solo escritura" infalible, pues al demandar una colección de supertipos genéricos, se asegura poder insertar un elemento de subtipo `T` sin quebrantar la lógica.

```java
import java.util.List;

public class UtilidadesWildcards {
    
    // (i) Uso de ? extends (Covarianza - Lectura)
    // Acepta List<Integer>, List<Double>, etc.
    public double sumarNumeros(List<? extends Number> lista) {
        double suma = 0.0;
        for (Number num : lista) {
            suma += num.doubleValue(); // Es seguro leer como Number
        }
        // lista.add(3); // ERROR: Imposible escribir porque se ignora el tipo exacto
        return suma;
    }

    // (ii) Uso de ? super (Contravarianza - Escritura)
    // Acepta List<Integer>, List<Number>, o List<Object>
    public void añadirEnteros(List<? super Integer> lista) {
        lista.add(10); // Es seguro añadir un Integer a sus supertipos
        lista.add(20);
        // Integer valor = lista.get(0); // ERROR: Inseguro leer sin cast manual
    }
}
```
