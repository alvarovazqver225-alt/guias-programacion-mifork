<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Composición". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación y Excepciones.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# Tema 4.1. Composición


## 1. En C, podemos crear estructuras mayores **componiendo** unas con otras, que suelen describirse como "A tiene-un/tiene-varios B". Pon un ejemplo, empleando `struct`, de una línea de puntos, donde puntos tienen dos coordenadas (`x` e `y`), y la línea esta hecha de dos puntos. Incluye una función para calcular la distancia entre puntos y otra para hallar la longitud de una línea.

### Respuesta

En lenguaje C, la composición se logra anidando estructuras (`struct`). Esto permite crear tipos de datos más complejos a partir de tipos más simples, estableciendo una relación de pertenencia donde una estructura principal contiene a otras como parte de sus atributos.

El siguiente código muestra cómo se define un `Punto` con sus coordenadas y, posteriormente, se compone una `Linea` que alberga internamente dos de estos puntos. De este modo, la línea se construye componiendo elementos más básicos, lo cual facilita la organización de la información y la reutilización del código.

Asimismo, se implementan funciones separadas para operar con estas estructuras. Una se encarga de calcular la distancia matemática entre dos puntos, mientras que otra utiliza la primera para determinar la longitud total de la línea basándose en los dos puntos que la componen.

```c
#include <math.h>

struct Punto {
    double x;
    double y;
};

struct Linea {
    struct Punto p1;
    struct Punto p2;
};

double distancia_puntos(struct Punto a, struct Punto b) {
    return sqrt(pow(b.x - a.x, 2) + pow(b.y - a.y, 2));
}

double longitud_linea(struct Linea l) {
    return distancia_puntos(l.p1, l.p2);
}
```


## 2. Ahora transforma ese ejemplo a orientación a objetos con Java, para tener un primer ejemplo de **composición** en orientación a objetos. Crea una clase `Punto`, y una clase `Linea`. La clase `Punto` debe tener un método para calcular distancia a otro `Punto` y `Linea` debe tener un método para calcular su longitud. Gracias a la ocultación de información, supera a C, garantizando que los puntos sean inmutables, al igual que la línea, que una vez creada, no queremos que se modifique de qué a qué puntos va dicha línea.  

### Respuesta

Al trasladar este diseño a la orientación a objetos con Java, las estructuras se transforman en clases que combinan estado (atributos) y comportamiento (métodos). La composición se expresa declarando atributos cuyo tipo es otra clase, como en el caso de `Linea`, que almacena referencias a objetos de la clase `Punto`.

Gracias a los mecanismos de encapsulación que proporciona Java, se puede garantizar la inmutabilidad de los objetos. Al marcar los atributos como `private final` y proporcionar únicamente métodos de consulta (getters) sin métodos de modificación (setters), se asegura que el estado de un punto o de una línea no cambie una vez inicializado en el constructor.

Esta aproximación ofrece ventajas significativas frente a las estructuras de C. La validación se centraliza en el momento de la creación del objeto, impidiendo que una línea se construya con puntos inválidos (por ejemplo, con valor `null`). Además, las operaciones recaen sobre los propios objetos, promoviendo una arquitectura más cohesiva y robusta.

```java
public class Punto {
    private final double x;
    private final double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() { return x; }
    public double getY() { return y; }

    public double distanciaA(Punto otro) {
        return Math.sqrt(Math.pow(otro.x - this.x, 2) + Math.pow(otro.y - this.y, 2));
    }
}

public class Linea {
    private final Punto origen;
    private final Punto destino;

    public Linea(Punto origen, Punto destino) {
        if (origen == null || destino == null) {
            throw new IllegalArgumentException("Los puntos no pueden ser nulos");
        }
        this.origen = origen;
        this.destino = destino;
    }

    public double longitud() {
        return origen.distanciaA(destino);
    }
}
```


## 3. ¿Qué significa la **multiplicidad** en la composición? En el ejemplo anterior, ¿cuál es la multiplicidad entre `Linea` y `Punto`? Indícalo expresando la multiplicidad en ambas direcciones, de `Linea` a `Punto` y de `Punto` a `Linea`.

### Respuesta

La multiplicidad en la composición indica la cantidad de instancias de una clase que están relacionadas o contenidas dentro de otra en un momento dado. Define los límites numéricos (mínimos y máximos) permitidos en la relación entre los diferentes componentes del modelo, determinando de forma precisa cuántos elementos de un tipo pueden o deben asociarse con un elemento de otro tipo.

En el ejemplo anterior de la línea y el punto, la relación posee una multiplicidad bidireccional clara. Desde la perspectiva de la clase `Linea` hacia `Punto`, la multiplicidad es de 1 a 2, puesto que una sola línea siempre está formada por exactamente dos puntos (origen y destino).

Por el contrario, desde la perspectiva de la clase `Punto` hacia la `Linea`, la multiplicidad suele definirse como de 1 a 0..* (cero a muchos). Esto significa que un punto específico, de forma independiente, podría no pertenecer a ninguna línea, o bien podría estar referenciado e incluido en múltiples líneas diferentes de manera simultánea en el sistema.


## 4. ¿Qué significa composición **fuerte** y composición **débil**? ¿Qué consecuencia implica en relación al ciclo de vida de los objetos? Indica a cuál solemos referirnos como **"asociación o agregación"** y a cuál como **"composición"** propiamente.

### Respuesta

La composición fuerte hace referencia a una relación donde el elemento contenido depende existencialmente del contenedor. En este escenario, si el contenedor es destruido, los objetos que alberga también lo serán de forma irremediable, dado que su existencia carece de sentido fuera de dicho contenedor.

En contraposición, la composición débil establece un vínculo más laxo en el que el elemento contenido puede existir de forma independiente. Si el contenedor principal desaparece, las partes que lo componen pueden seguir vivas y ser referenciadas por otras partes del sistema, ya que su ciclo de vida no está estrictamente ligado al del contenedor que las agrupaba momentáneamente.

En el vocabulario del diseño orientado a objetos, a la composición débil se la suele denominar "asociación" o "agregación", reflejando que los elementos se juntan pero mantienen su independencia vital. Por otro lado, la composición fuerte es la que recibe el nombre estricto de "composición", implicando una pertenencia exclusiva y una responsabilidad directa sobre la creación y destrucción de sus partes.


## 5. Cuando una clase usa a otra al recibirla o devolverla como parámetro en algún método, al hacer `new` dentro de un método, o al usarlas como variables locales, ¿hablamos de composición o de **"dependencia"**?

### Respuesta

Cuando una clase utiliza a otra de manera transitoria, como al recibirla por parámetro en un método, devolverla, instanciarla localmente o usarla como variable temporal, se habla de una relación de "dependencia" en lugar de composición o asociación.

Esta relación implica que los cambios en la definición de la clase utilizada podrían afectar directamente a la clase que la usa, generando un acoplamiento a nivel de comportamiento. Sin embargo, no existe un almacenamiento prolongado ni una relación de posesión entre ellas, puesto que no se declaran como atributos a nivel de la clase contenedora.

Las dependencias representan el nivel más débil de acoplamiento entre objetos. A diferencia de la composición, donde el objeto forma parte de la estructura interna durante gran parte o todo su ciclo de vida, la dependencia es efímera y se limita estrictamente a la duración de la invocación del método o bloque de código correspondiente.


## 6. En el ejemplo anterior de línea y punto, programa la relación entre `Linea` y `Punto` de dos formas. Una **como composición fuerte**, donde el ciclo de vida de los puntos está ligado al de Linea y otra **como composición débil**, donde no.

### Respuesta

Para ilustrar la diferencia, a continuación se presentan dos formas de programar la relación entre una línea y sus puntos. En la **composición débil** (agregación), la línea recibe los puntos ya creados desde el exterior. El ciclo de vida de los puntos no está controlado por la línea, por lo que si la línea deja de usarse, los puntos pueden seguir existiendo en otras partes del programa.

```java
// Composición débil (Agregación)
public class LineaDebil {
    private final Punto origen;
    private final Punto destino;

    public LineaDebil(Punto origen, Punto destino) {
        this.origen = origen;
        this.destino = destino;
    }
}
```

Por el contrario, en la **composición fuerte**, la línea es responsable exclusiva de crear los puntos en su propio interior. No se reciben puntos preexistentes, sino sus coordenadas, de manera que la línea instancia sus propios puntos privados. Si la línea desaparece, la única referencia a esos puntos se pierde con ella.

```java
// Composición fuerte
public class LineaFuerte {
    private final Punto origen;
    private final Punto destino;

    public LineaFuerte(double x1, double y1, double x2, double y2) {
        this.origen = new Punto(x1, y1);
        this.destino = new Punto(x2, y2);
    }
}
```


## 7. En Java, en la composición fuerte, ¿cuando el contenedor destruye los objetos? No se observa que `Linea` destruya los `Punto` explícitamente, ¿Por qué?

### Respuesta

En lenguajes como C o C++, el programador debe encargarse explícitamente de liberar la memoria de los objetos contenidos cuando el contenedor es destruido, para evitar fugas de memoria. Sin embargo, en Java, la destrucción de objetos no se gestiona de manera manual, lo cual explica por qué la clase `Linea` no incluye código explícito para eliminar sus puntos.

Esta tarea recae sobre el Recolector de Basura (Garbage Collector), un mecanismo de la máquina virtual de Java. El Recolector detecta automáticamente cuándo un objeto ha dejado de ser accesible o referenciado desde cualquier otra parte activa del programa.

En el caso de una composición fuerte, cuando la instancia de `Linea` deja de tener referencias útiles y es eliminada de la memoria, sus atributos privados internos, como los objetos `Punto`, también pierden automáticamente sus últimas referencias. En ese instante, el Recolector de Basura asume el control y se encarga de liberar la memoria ocupada por dichos puntos de forma transparente.


## 8. Pon un ejemplo de composicion débil entre un departamento que tiene varios profesores. Implementa dos composiciones a la vez: entre el departamento y todos sus profesores y entre el departamento y su director, que es un profesor del departamento. Siempre debe haber un director en el departamento desde el inicio. Lanza excepciones si se viola la invariante. Emplea arrays primitivos de Java, estilo `Profesor[]`, con máximo 50, pero no rompas la encapsulación, no desveles que estás empleando un array, permite añadir un `Profesor` al final de la lista, y eliminar un profesor dada su posición. Da acceso a los profesores con un método para saber cuántos hay y otro para obtener un profesor por posición. El director se puede cambiar por otro profesor del departamento. Sin embargo, ten en cuenta esta invariante de clase: el director debe formar siempre parte de la lista de profesores, es decir, ten cuidado al cambiar el director o al eliminar un profesor.

### Respuesta

El ejemplo muestra un `Departamento` que gestiona dos composiciones débiles: una multiplicidad hacia muchos `Profesor` y una relación directa hacia un único `Profesor` que actúa como director. Al requerirse el director en el constructor, se garantiza la invariante de que siempre exista uno y que forme parte de la lista interna.

Mediante la encapsulación, se esconde la utilización de un array estático de Java y la gestión del contador `numProfesores`. Los métodos garantizan la integridad de los datos lanzando excepciones tanto al intentar sobrepasar el límite como al querer eliminar al profesor que actualmente ejerce de director.

Se puede observar cómo las validaciones resultan fundamentales para mantener el estado coherente. El método `setDirector` debe cerciorarse de que el nuevo candidato pertenece a la lista de profesores, mientras que el borrado debe bloquear la expulsión del director activo, protegiendo así la invariante del diseño en todo momento.

```java
public class Profesor {
    private final String nombre;

    public Profesor(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }
}

public class Departamento {
    private final Profesor[] profesores;
    private int numProfesores;
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) {
            throw new IllegalArgumentException("El director no puede ser nulo.");
        }
        this.profesores = new Profesor[50];
        this.numProfesores = 0;
        this.director = directorInicial;
        addProfesor(directorInicial);
    }

    public void addProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("El profesor no puede ser nulo.");
        if (numProfesores >= profesores.length) {
            throw new IllegalStateException("Capacidad máxima de profesores alcanzada.");
        }
        profesores[numProfesores++] = p;
    }

    public void removeProfesor(int pos) {
        if (pos < 0 || pos >= numProfesores) {
            throw new IndexOutOfBoundsException("Posición no válida.");
        }
        if (profesores[pos] == director) {
            throw new IllegalStateException("No se puede eliminar al director del departamento.");
        }
        for (int i = pos; i < numProfesores - 1; i++) {
            profesores[i] = profesores[i + 1];
        }
        profesores[--numProfesores] = null;
    }

    public void setDirector(Profesor nuevoDirector) {
        boolean pertenece = false;
        for (int i = 0; i < numProfesores; i++) {
            if (profesores[i] == nuevoDirector) {
                pertenece = true;
                break;
            }
        }
        if (!pertenece) {
            throw new IllegalArgumentException("El nuevo director debe pertenecer al departamento.");
        }
        this.director = nuevoDirector;
    }

    public int getCantidadProfesores() {
        return numProfesores;
    }

    public Profesor getProfesor(int pos) {
        if (pos < 0 || pos >= numProfesores) {
            throw new IndexOutOfBoundsException("Posición no válida.");
        }
        return profesores[pos];
    }
}
```


## 9. En Java, existen también `List`, cambia y muestra cómo sería el código anterior empleando `List` en vez de arrays primitivos. ¿Qué parte del código original te has ahorrado? Además, fíjate en el método `getProfesor(int pos)`: si en su lugar existiera un método que devolviera todos los profesores a la vez, ¿qué problema tendría devolver directamente la lista interna? ¿Cómo lo resolverías?

### Respuesta

Al utilizar la interfaz `List` e instanciar un `ArrayList`, se simplifica notablemente el código. Se ahorra por completo la gestión del contador manual, los desplazamientos de elementos al eliminar desde una posición intermedia y la limitación rígida del tamaño máximo de cincuenta elementos. 

Respecto a devolver directamente la lista interna desde un método para dar acceso global a los profesores, esto supone una ruptura grave de la encapsulación. Si se retorna la referencia a la lista original, el código externo podría añadir o eliminar profesores de ella eludiendo todas las validaciones definidas, lo que rompería las invariantes y pondría en riesgo la estabilidad del objeto.

Para resolver este problema, la práctica recomendada consiste en proteger la estructura interna. Se puede devolver una copia defensiva de la lista (creando una nueva lista con los mismos elementos) o emplear mecanismos como `Collections.unmodifiableList(profesores)`, que retorna una vista de solo lectura que lanza una excepción si se intenta modificar.

```java
import java.util.ArrayList;
import java.util.List;

public class Departamento {
    private final List<Profesor> profesores;
    private Profesor director;

    public Departamento(Profesor directorInicial) {
        if (directorInicial == null) throw new IllegalArgumentException("El director no puede ser nulo.");
        this.profesores = new ArrayList<>();
        this.director = directorInicial;
        addProfesor(directorInicial);
    }

    public void addProfesor(Profesor p) {
        if (p == null) throw new IllegalArgumentException("El profesor no puede ser nulo.");
        profesores.add(p);
    }

    public void removeProfesor(int pos) {
        if (pos < 0 || pos >= profesores.size()) throw new IndexOutOfBoundsException("Posición no válida.");
        if (profesores.get(pos) == director) {
            throw new IllegalStateException("No se puede eliminar al director del departamento.");
        }
        profesores.remove(pos);
    }

    public void setDirector(Profesor nuevoDirector) {
        if (!profesores.contains(nuevoDirector)) {
            throw new IllegalArgumentException("El nuevo director debe pertenecer al departamento.");
        }
        this.director = nuevoDirector;
    }

    public int getCantidadProfesores() {
        return profesores.size();
    }

    public Profesor getProfesor(int pos) {
        return profesores.get(pos);
    }
}
```


## 10. Al igual que ocurre con las excepciones en Java, que pueden encerrar causas (que son excepciones), de forma recursiva, suponen un tipo especial de composiciones, denominadas composiciones recursivas. Pon un ejemplo en Java de una `Persona`, que sea inmutable, y que tiene una madre, que es otra `Persona`. Haz un main con un ejemplo de uso con una familia de personas, desde el nieto hasta la abuela. Enumera algún otro ejemplo clásico de composiciones recursivas.

### Respuesta

Una composición recursiva ocurre cuando una clase declara como atributo una o más instancias de su mismo tipo. Al igual que las excepciones pueden anidar otras excepciones internamente para representar una cadena de causas, las composiciones recursivas permiten construir estructuras jerárquicas encadenando objetos idénticos en su definición.

El código ilustra una cadena inmutable donde cada `Persona` almacena una referencia a su `madre`, que a su vez es otra `Persona`. Al ejecutar el ejemplo en la función principal, se puede navegar esta relación ascendente de manera recursiva, partiendo desde el eslabón más bajo hasta alcanzar el nodo raíz (en el que la referencia interna es nula).

Aparte del anidamiento de excepciones, otros ejemplos clásicos de composiciones recursivas en el desarrollo de software incluyen las estructuras de directorios en un sistema de archivos (donde un directorio puede contener más directorios), los nodos de una lista enlazada o los componentes de interfaces gráficas.

```java
public class Persona {
    private final String nombre;
    private final Persona madre;

    public Persona(String nombre, Persona madre) {
        this.nombre = nombre;
        this.madre = madre;
    }

    public String getNombre() { return nombre; }
    public Persona getMadre() { return madre; }

    public static void main(String[] args) {
        Persona bisabuela = new Persona("Carmen", null);
        Persona abuela = new Persona("Maria", bisabuela);
        Persona madre = new Persona("Laura", abuela);
        Persona nieto = new Persona("Hugo", madre);

        System.out.println("Nieto: " + nieto.getNombre());
        System.out.println("Madre: " + nieto.getMadre().getNombre());
        System.out.println("Abuela: " + nieto.getMadre().getMadre().getNombre());
    }
}
```

## 11. ¿Qué son las relaciones de composición "bidireccionales"? ¿Qué habría que hacer para implementar este tipo de relación en el ejemplo de `Profesor` y `Departamento`?

### Respuesta

Las relaciones de composición bidireccionales ocurren cuando ambos extremos de una asociación mantienen referencias el uno hacia el otro de manera simultánea. Es decir, el objeto contenedor conoce a sus componentes internos, y a la vez, cada componente conoce y guarda una referencia directa al contenedor específico al cual pertenece.

En el caso del ejemplo entre `Profesor` y `Departamento`, establecer una relación bidireccional implicaría que el `Departamento` continuaría albergando la lista de profesores, pero la clase `Profesor` recibiría y almacenaría como atributo privado una referencia a su `Departamento` asignado.

Implementar esto correctamente requiere una especial precaución durante las modificaciones para evitar inconsistencias. Cuando se añade un profesor al departamento, el método de inserción no solo debe agregarlo a la lista interna, sino también invocar al profesor para asignarle el departamento. Esta doble actualización debe gestionarse cuidadosamente para asegurar que ambos lados del vínculo apunten a la realidad correcta.
