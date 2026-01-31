<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Clases y Objetos". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: ninguno.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->

# TEMA 1. Clases y objetos

## 1. ¿Cuáles son las cuatro características básicas de la programación orientada a objetos? Describe brevemente cada una

La programación orientada a objetos (POO) se fundamenta en cuatro pilares que permiten gestionar la complejidad del software. El primero es la abstracción, que consiste en identificar las características esenciales de un objeto descartando los detalles no relevantes para el problema actual. El segundo es el encapsulamiento, que agrupa datos y comportamientos en una unidad (clase), ocultando el estado interno del objeto y permitiendo el acceso solo a través de una interfaz definida.

Los otros dos pilares son la herencia y el polimorfismo. La herencia permite crear nuevas clases a partir de otras existentes, facilitando la reutilización de código y la jerarquización de conceptos. Por su parte, el polimorfismo permite que diferentes objetos respondan de manera distinta a un mismo mensaje o llamada a un método, lo que aporta una gran flexibilidad al diseño del sistema.

mindmap
  root((POO))
    Abstraccion
    Encapsulamiento
    Herencia
    Polimorfismo


## 2. Cita cuatro lenguajes populares que permitan la programación orientada a objetos

Existen múltiples lenguajes que adoptan este paradigma, cada uno con matices en su implementación. Java es, probablemente, el referente más puro en el ámbito académico y empresarial por su diseño "todo es una clase". C++, que ya conoces en su faceta estructurada, es un lenguaje multiparadigma que permite POO manteniendo un control de bajo nivel.

Otros dos lenguajes de enorme relevancia son Python, muy valorado por su sintaxis limpia y su uso en ciencia de datos, y C#, desarrollado por Microsoft, que comparte muchas similitudes con Java y es fundamental en el desarrollo de aplicaciones para Windows y videojuegos con Unity.

## 3. Los paradigmas anteriores a la POO, ¿Qué es la **programación estructurada**? y, todavía mejor, ¿Qué es la **programación modular**?

La programación estructurada es un paradigma basado en el uso de estructuras de control (secuencias, selecciones como if/else y bucles como while/for) y subrutinas o funciones. Su objetivo principal es mejorar la claridad y el tiempo de desarrollo mediante la división del programa en bloques lógicos, evitando el uso de saltos incondicionales (como el goto) que complican el seguimiento del flujo.

La programación modular da un paso más allá al organizar el código en módulos o unidades separadas e independientes. Cada módulo agrupa funciones y datos relacionados, lo que facilita la legibilidad y permite que diferentes programadores trabajen en distintas partes de un sistema simultáneamente sin interferir entre sí. En C, esto se suele gestionar mediante el uso de archivos .h y .c.

graph LR
    A[Programa Principal] --> B[Modulo A]
    A --> C[Modulo B]
    B --> B1[Función 1]
    B --> B2[Función 2]
    C --> C1[Función 3]

## 4. ¿Qué tres elementos definen a un objeto en programación orientada a objetos?

Un objeto se define por su estado, su comportamiento y su identidad. El estado está compuesto por los datos o atributos que el objeto almacena en un momento dado (por ejemplo, las coordenadas x e y de un punto). El comportamiento se define mediante los métodos o funciones que el objeto puede ejecutar, los cuales suelen operar sobre su propio estado.

La identidad es lo que permite distinguir a un objeto de otro, incluso si ambos tienen el mismo estado (los mismos valores en sus atributos). En lenguajes como Java, la identidad está ligada a la dirección de memoria donde reside el objeto, permitiendo que el sistema reconozca que son instancias distintas.

## 5. ¿Qué es una clase? ¿Es lo mismo que un objeto? ¿Qué es una instancia? ¿Todos los lenguajes orientados a objetos manejan el concepto de clase?

Una clase es el molde, plano o plantilla que define las características y acciones comunes a un grupo de elementos. No es lo mismo que un objeto; la clase es la definición abstracta (el código), mientras que el objeto es la entidad concreta que existe en memoria durante la ejecución. El término instancia es prácticamente sinónimo de objeto, refiriéndose al acto de "instanciar" o crear un ejemplar basado en la clase.

No todos los lenguajes orientados a objetos utilizan clases. Aunque Java, C++ y C# son "basados en clases", existen lenguajes como JavaScript (en sus versiones originales) que utilizan un modelo "basado en prototipos", donde los objetos se crean clonando otros objetos existentes en lugar de seguir un plano estático.

graph TD
    Clase[Clase: Molde de Galletas] -- instanciación --> O1(Objeto A: Galleta 1)
    Clase -- instanciación --> O2(Objeto B: Galleta 2)


## 6. ¿Dónde se almacenan en memoria los objetos? ¿Es igual en todos los lenguajes? ¿Qué es la **recolección de basura**? 

En Java, los objetos se almacenan siempre en una región de memoria llamada Heap (montículo). A diferencia de C, donde puedes elegir crear una estructura en el stack o en el heap (usando malloc), en Java el programador solo manipula referencias (punteros gestionados) que apuntan a la memoria dinámica. Esta gestión varía entre lenguajes: C++ permite mayor control manual, mientras que Java prioriza la seguridad.

La recolección de basura (Garbage Collection) es un proceso automático de la Máquina Virtual de Java (JVM) que identifica y destruye los objetos que ya no tienen ninguna referencia apuntándoles. Esto libera al programador de la tarea de liberar memoria manualmente (el free de C), evitando errores comunes como las fugas de memoria (memory leaks).

graph LR
    Stack[Stack: Variables locales/Referencias] -->|apunta a| Heap[Heap: Objetos Reales]
    Heap --> GC{Garbage Collector}
    GC -->|Elimina| X[Objetos sin referencia]


## 7. ¿Qué es un método? ¿Qué es la **sobrecarga de métodos**? 

Un método es un bloque de código dentro de una clase que define un comportamiento o acción que los objetos de esa clase pueden realizar. Es el equivalente a una función en C, pero con la particularidad de que siempre pertenece a una clase y tiene acceso a los atributos del objeto que lo invoca. Se compone de un modificador de acceso, un tipo de retorno, un nombre y una lista de parámetros.

La sobrecarga de métodos es la capacidad de definir en una misma clase varios métodos con el mismo nombre, pero con diferentes "firmas" (distinto número o tipo de parámetros). Esto permite realizar acciones similares con diferentes tipos de entrada sin necesidad de inventar nombres distintos, mejorando la legibilidad del código. El compilador decide qué método ejecutar basándose en los argumentos proporcionados en la llamada.

## 8. Ejemplo mínimo de clase en Java, que se llame Punto, con dos atributos, x e y, con un método que se llame `calculaDistanciaAOrigen`, que calcule la distancia a la posición 0,0. Por sencillez, los atributos deben tener visibilidad por defecto. Crea además un ejemplo de uso con una instancia y uso del método

Este es un ejemplo de cómo se define una clase con sus atributos y un método de cálculo. Al no especificar public o private en los atributos, estos tienen visibilidad por defecto (paquete).

public class Punto {
    // Atributos con visibilidad por defecto
    double x;
    double y;

    // Método para calcular la distancia al origen (0,0)
    public double calculaDistanciaAOrigen() {
        return Math.sqrt(x * x + y * y);
    }

    // Ejemplo de uso (normalmente iría en otra clase o en el main)
    public static void main(String[] args) {
        Punto p1 = new Punto(); // Instancia
        p1.x = 3.0;
        p1.y = 4.0;
        
        double distancia = p1.calculaDistanciaAOrigen();
        System.out.println("La distancia al origen es: " + distancia);
    }
}

classDiagram
    class Punto {
        double x
        double y
        +calculaDistanciaAOrigen() double
    }


## 9. ¿Cuál es el punto de entrada en un programa en Java? ¿Qué es `static` y para qué vale? ¿Sólo se emplea para ese método `main`? ¿Para qué se combina con `final`?

El punto de entrada de cualquier aplicación Java es el método public static void main(String[] args). La palabra clave static indica que el método (o atributo) pertenece a la clase en sí y no a una instancia específica. Esto permite que la Máquina Virtual de Java (JVM) ejecute el main sin necesidad de crear primero un objeto de esa clase, lo cual sería imposible si el programa aún no ha empezado.

static no se limita al main; se usa para definir constantes de clase o métodos de utilidad que no dependen del estado de un objeto (como Math.sqrt). Cuando se combina con final, se crean constantes inmutables de clase (ej. public static final double PI = 3.14159;). Esto asegura que el valor sea compartido por todas las instancias y que no pueda ser modificado una vez asignado.

## 10. Intenta ejecutar un poco de Java de forma básica, con los comandos `javac` y `java`. ¿Cómo podemos compilar el programa y ejecutarlo desde linea de comandos? ¿Java es compilado? ¿Qué es la **máquina virtual**? ¿Qué es el *byte-code* y los ficheros `.class`?

Para compilar un programa en Java desde la línea de comandos, se utiliza el comando javac seguido del nombre del archivo (ej. javac Punto.java). Este proceso no genera un ejecutable binario directo para el procesador como ocurre en C, sino que produce un archivo .class que contiene byte-code. Para ejecutarlo, se utiliza el comando java seguido del nombre de la clase (ej. java Punto), lo que inicia la Máquina Virtual de Java (JVM).

Java se considera un lenguaje de "compilación intermedia". El byte-code es un lenguaje de bajo nivel diseñado para ser ejecutado por la JVM, no por el hardware directamente. Esta arquitectura es la que permite la portabilidad ("Escribe una vez, ejecuta en cualquier lugar"), ya que el mismo archivo .class puede ejecutarse en cualquier sistema operativo siempre que tenga instalada su propia versión de la JVM.


## 11. En el código anterior de la clase `Punto` ¿Qué es `new`? ¿Qué es un **constructor**? Pon un ejemplo de constructor en una clase `Empleado` que tenga DNI, nombre y apellidos

En Java, la palabra clave new es el operador encargado de solicitar memoria en el heap para un nuevo objeto e invocar a su constructor. Sin el new, solo tendríamos una referencia "vacía" (similar a un puntero a NULL en C). El constructor es un método especial que se ejecuta automáticamente al crear la instancia; no tiene tipo de retorno y se llama exactamente igual que la clase.

Su función principal es inicializar el estado del objeto (sus atributos) para asegurar que el objeto sea válido desde el momento de su creación. Si no definimos ningún constructor, Java proporciona uno por defecto sin parámetros, pero es una buena práctica definir los nuestros para obligar a que los datos esenciales (como el DNI en un Empleado) se asignen desde el principio.

public class Empleado {
    String dni;
    String nombre;
    String apellidos;

    // Constructor de la clase Empleado
    public Empleado(String dni, String nombre, String apellidos) {
        this.dni = dni;
        this.nombre = nombre;
        this.apellidos = apellidos;
    }
}

// Ejemplo de creación:
// Empleado emp = new Empleado("12345678X", "Álvaro", "García");

classDiagram
    class Empleado {
        String dni
        String nombre
        String apellidos
        +Empleado(dni, nombre, apellidos)
    }


## 12. ¿Qué es la referencia `this`? ¿Se llama igual en todos los lenguajes? Pon un ejemplo del uso de `this` en la clase `Punto`

La palabra clave this es una referencia que todo objeto tiene hacia sí mismo. Se utiliza principalmente dentro de los métodos o constructores para evitar la ambigüedad cuando un parámetro tiene el mismo nombre que un atributo de la clase. Al usar this.atributo, le indicamos al compilador que nos referimos a la variable de instancia y no al parámetro local del método.

Este concepto no se llama igual en todos los lenguajes: mientras que en Java, C++ y C# se utiliza this, en otros como Python se emplea obligatoriamente self, y en Visual Basic se usa Me. Aunque el nombre cambie, la función es la misma: permitir que el código del objeto acceda a sus propios miembros de forma inequívoca.

public class Punto {
    double x; // Atributo de la clase
    double y;

    public void setUbicacion(double x, double y) {
        // 'this.x' es el atributo, 'x' es el parámetro
        this.x = x; 
        this.y = y;
    }
}

## 13. Añade ahora otro nuevo método que se llame `distanciaA`, que reciba un `Punto` como parámetro y calcule la distancia entre `this` y el punto proporcionado

Para calcular la distancia entre dos puntos ($P_1$ y $P_2$), aplicamos la fórmula de la distancia euclídea: $d = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}$. En este caso, el punto $P_1$ será el objeto que ejecuta el método (this) y el punto $P_2$ será el objeto pasado como parámetro.Este método demuestra cómo los objetos pueden interactuar entre sí. Aunque el parámetro sea otro objeto de la clase Punto, tenemos acceso a sus atributos x e y para realizar el cálculo.

public double distanciaA(Punto otroPunto) {
    double dx = this.x - otroPunto.x;
    double dy = this.y - otroPunto.y;
    return Math.sqrt(dx * dx + dy * dy);
}

graph LR
    A((this: Punto)) -- calcula distancia a --> B((otroPunto: Punto))
    style A fill:#e1f5fe
    style B fill:#e8f5e8


## 14. El paso del `Punto` como parámetro a un método, es **por copia** o **por referencia**, es decir, si se cambia el valor de algún atributo del punto pasado como parámetro, dichos cambios afectan al objeto fuera del método? ¿Qué ocurre si en vez de un `Punto`, se recibiese un entero (`int`) y dicho entero se modificase dentro de la función? 

En Java, todo se pasa por valor, pero hay que entender qué es exactamente lo que se copia. Cuando pasamos un objeto (como un Punto), lo que se copia es el valor de la referencia (la dirección de memoria). Por tanto, si dentro del método cambias un atributo del punto (ej. p.x = 10;), el cambio sí afecta al objeto fuera del método porque ambos apuntan al mismo sitio en el heap. Sin embargo, si dentro del método intentas reasignar el parámetro entero (ej. p = new Punto();), eso no afectará a la variable original fuera del método.

Con los tipos primitivos como int, el comportamiento es distinto. Se pasa una copia del valor numérico. Si modificas ese int dentro del método, el cambio es local a la función y no afecta en absoluto a la variable original fuera de ella. En resumen: los objetos se comportan de forma "similar" a pasar un puntero en C, mientras que los primitivos se comportan como un paso por valor estándar.


## 15. ¿Qué es el método `toString()` en Java? ¿Existe en otros lenguajes? Pon un ejemplo de `toString()` en la clase `Punto` en Java

El método toString() es un método especial heredado de la clase base Object (de la que heredan todas las clases en Java). Su propósito es devolver una representación en formato de texto (String) de un objeto. Si no lo sobrescribes, Java imprimirá algo poco útil como Punto@1a2b3c. Es muy común en otros lenguajes; por ejemplo, en Python se utiliza el método especial __str__ para el mismo fin.

Implementar toString() es fundamental para la depuración, ya que permite ver el estado del objeto de forma clara con un simple System.out.println(p);. Java invoca este método automáticamente siempre que se intenta concatenar un objeto con una cadena de texto.

@Override
public String toString() {
    return "Punto(x=" + this.x + ", y=" + this.y + ")";
}

// Uso:
// Punto p = new Punto(3, 4);
// System.out.println(p); // Imprime: Punto(x=3.0, y=4.0)


## 16. Reflexiona: ¿una clase es como un `struct` en C? ¿Qué le falta al `struct` para ser como una clase y las variables de ese tipo ser instancias?


Una clase se puede ver como una evolución del struct de C, pero con diferencias fundamentales. Un struct solo puede contener datos (variables). Para que un struct fuera como una clase, le faltaría la capacidad de contener comportamiento (métodos) dentro de sí mismo y mecanismos de control de acceso (como private o public) para proteger sus datos.

Además, en C las variables de tipo struct pueden vivir en el stack (pila), mientras que en Java las instancias de una clase (objetos) siempre viven en el heap y nosotros solo manejamos referencias a ellos. La clase es el "plano" completo que une datos y lógica, mientras que el struct es solo un contenedor de datos.

graph TD
    A[struct en C] --> B{¿Qué le falta?}
    B --> C[Métodos / Funciones propias]
    B --> D[Control de acceso / Encapsulamiento]
    B --> E[Gestión automática de memoria]
    C & D & E --> F[Clase en Java]


## 17. Quitemos un poco de magia a todo esto: ¿Como se podría “emular”, con `struct` en C, la clase `Punto`, con su función para calcular la distancia al origen? ¿Qué ha pasado con `this`?

Para emular la clase Punto en C, se definiría un struct con los campos x e y. Dado que en C los struct no pueden contener funciones, la función calculaDistanciaAOrigen debe definirse fuera. Para que la función sepa sobre qué datos operar, es obligatorio pasarle un puntero al struct como primer argumento. En este escenario, el puntero que pasamos manualmente (normalmente llamado self o p) hace exactamente la misma función que el this implícito en Java.

Lo que ha pasado con this es que en Java el compilador "inyecta" automáticamente esa referencia en todos los métodos de instancia. En C, la gestión es manual y externa: no hay encapsulamiento real, ya que cualquier parte del programa podría modificar los campos del struct directamente si tiene acceso al puntero, a menos que se usen técnicas avanzadas de tipos opacos.

// Emulación en C
typedef struct {
    double x;
    double y;
} Punto;

double calculaDistanciaAOrigen(Punto* this) {
    return sqrt(this->x * this->x + this->y * this->y);
}

// Uso en C:
// Punto p1 = {3.0, 4.0};
// double d = calculaDistanciaAOrigen(&p1);