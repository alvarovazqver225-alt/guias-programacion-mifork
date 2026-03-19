<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Encapsulación". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 2. Encapsulación

## 1. En Programación Orientada a Objetos (POO), ¿Qué buscan la **encapsulación** y **la ocultación** de información? Enumera brevemente algunas ventajas de la ocultación de información.

La encapsulación en Programación Orientada a Objetos es el mecanismo que agrupa los datos (atributos) y los métodos (comportamientos) que operan sobre esos datos en una única unidad lógica o "cápsula", que en Java denominamos clase. Por otro lado, la ocultación de información es el principio de diseño que restringe el acceso directo a los detalles internos de esta unidad desde el exterior. Juntos, buscan proteger el estado interno del objeto, garantizando que este solo pueda ser modificado o consultado a través de una interfaz controlada y bien definida, independizando el "qué hace" el objeto del "cómo lo hace".

Las ventajas principales de la ocultación de información radican en la robustez y la mantenibilidad del código. En primer lugar, previene modificaciones accidentales o indebidas del estado del objeto, lo que garantiza la integridad de los datos y el cumplimiento de las reglas de negocio (conocidas como invariantes). En segundo lugar, reduce drásticamente el acoplamiento entre las distintas partes de un programa, ya que los componentes externos no dependen de la implementación interna de una clase.

Esto último facilita enormemente la evolución del software: si en el futuro se necesita cambiar la forma en que se almacenan los datos internamente (por ejemplo, pasar de usar dos variables separadas a un array), el código cliente que utiliza dicho objeto no se verá afectado en absoluto, siempre y cuando la firma de los métodos públicos permanezca inalterada.

graph TD
    subgraph Capsula [Clase Encapsulada]
        A[Atributos Privados<br/>Estado Oculto]
        B[Métodos Públicos<br/>Interfaz Externa]
        B -->|Controla el acceso y modifica| A
    end
    C[Código Cliente<br/>Resto del Programa] -->|Interactúa a través de| B
    C -.->|Acceso Denegado ❌| A
    
    style A fill:#ffcdd2,stroke:#c62828
    style B fill:#c8e6c9,stroke:#2e7d32


## 2. ¿Qué se entiende por la **interfaz pública** de un objeto o clase en POO? Describe brevemente cómo se relaciona con la ocultación de información.

La interfaz pública de una clase está constituida por el conjunto de métodos (y, ocasionalmente, constantes) que se exponen y se hacen accesibles para el resto del programa. En otras palabras, define el "contrato" de la clase: establece de forma clara qué operaciones puede realizar un objeto de ese tipo y cómo otros componentes externos pueden interactuar con él, especificando los datos que recibe y los que devuelve.

Este concepto está íntimamente ligado a la ocultación de información. Al diseñar una interfaz pública bien delimitada, se obliga a que cualquier interacción con el estado del objeto (sus datos) se canalice exclusivamente a través de dichos métodos. Los detalles de implementación y los atributos se mantienen ocultos en el ámbito privado de la clase, actuando la interfaz como una barrera protectora que valida y gestiona todas las peticiones externas.

Gracias a esta separación entre lo que es público y lo que está oculto, se logra que el "qué hace" el objeto sea visible, pero el "cómo lo hace" permanezca invisible. Esto garantiza que la lógica interna pueda modificarse, optimizarse o corregirse en el futuro sin romper el código que depende de la clase, siempre y cuando las firmas de los métodos de la interfaz pública (nombre, parámetros y tipo de retorno) se mantengan inalteradas.

A modo de ejemplo en Java, observa cómo se separan visual y lógicamente ambos conceptos:

public class CuentaBancaria {
    // 1. Ocultación de información: el estado interno es estrictamente privado.
    // Ningún código externo puede modificar 'saldo' directamente.
    private double saldo;

    // 2. Interfaz pública: los métodos accesibles desde el exterior.
    // Forman el "contrato" de uso de la clase CuentaBancaria.
    public void depositar(double cantidad) {
        if (cantidad > 0) { // La interfaz protege el estado interno
            this.saldo += cantidad;
        }
    }

    public double consultarSaldo() {
        return this.saldo;
    }
}


classDiagram
    class CuentaBancaria {
        -double saldo
        +depositar(double cantidad) void
        +consultarSaldo() double
    }
    note for CuentaBancaria "El símbolo '-' indica estado oculto (private).\nEl símbolo '+' representa la interfaz pública (public)."

## 3. Brevemente: ¿Por qué hay que ser conscientes y diseñar con cuidado la **interfaz pública** de una clase? ¿Es fácil cambiarla?

Hay que diseñar la interfaz pública con sumo cuidado porque representa el contrato definitivo entre la clase y el resto del sistema. Una vez que establecemos estos métodos como públicos, otros componentes (las clases cliente) comenzarán a invocarlos y a depender directamente de sus firmas para funcionar correctamente. Un diseño precipitado podría exponer detalles internos innecesarios, creando métodos confusos que rompan la encapsulación y comprometan la seguridad de los datos que la clase debe gestionar.

Cambiar la interfaz pública no es fácil en absoluto una vez que la clase está siendo utilizada por otras partes del programa. Si modificamos la firma de un método público (ya sea su nombre, el tipo de dato que retorna o el número y tipo de sus parámetros), provocaremos errores de compilación inmediatos en todas y cada una de las líneas de código externo que hacían llamadas a dicho método. Esto obliga al programador a rastrear y actualizar manualmente todas las dependencias afectadas, un proceso costoso y propenso a introducir nuevos errores en sistemas medianos o grandes.

Esta rigidez contrasta radicalmente con los elementos privados de la clase, cuya implementación podemos alterar en cualquier momento sin afectar al exterior. Por este motivo, la regla de oro en el diseño Orientado a Objetos dicta que la interfaz pública debe ser lo más minimalista, clara y estable posible desde el primer momento, garantizando que el "contrato" perdure en el tiempo frente a los inevitables cambios internos.

A modo de ejemplo, observa cómo un simple cambio en la interfaz rompería el código dependiente:

public class Calculadora {
    // Diseño inicial de la interfaz pública
    public int sumar(int a, int b) { 
        return a + b; 
    }

    /* Si en el futuro intentamos cambiar la firma para soportar decimales:
     public double sumar(double a, double b) { ... }
     
     Romperíamos automáticamente el código de otros programadores que dependían 
     del retorno entero original:
     
     int resultado = miCalculadora.sumar(5, 10); // ¡Error de compilación en el cliente!
    */
}

graph TD
    A[Código Cliente<br/>Otras clases] -->|Dependencia estricta| B[Interfaz Pública<br/>Métodos public]
    B -->|Protege y accede a| C[Implementación Interna<br/>Atributos private]
    
    style A fill:#fff3e0,stroke:#ef6c00
    style B fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px
    style C fill:#ffcdd2,stroke:#c62828,stroke-dasharray: 5 5
    
    note1[¡Si cambia B, se rompe A!]
    A -.-> note1


## 4. ¿Qué son las **invariantes de clase** y por qué la ocultación de información nos ayuda?

Las invariantes de clase son un conjunto de reglas, condiciones lógicas o restricciones que deben cumplirse siempre para que el estado de un objeto se considere válido y coherente a lo largo de toda su existencia. Estas condiciones se deben garantizar al momento de instanciar el objeto (a través de su constructor) y deben mantenerse verdaderas antes y después de la ejecución de cualquier método que pertenezca a su interfaz pública. Por ejemplo, en una clase CuentaBancaria, una invariante podría ser que el número de cuenta debe tener exactamente 20 dígitos; en una clase Fraccion, la invariante dictaría que el denominador jamás puede ser cero.

La ocultación de información es el mecanismo técnico clave que permite garantizar que estas invariantes no se rompan accidental o malintencionadamente. Al declarar los atributos que forman el estado del objeto como privados (private), se impide radicalmente que cualquier fragmento de código externo modifique las variables de forma directa y asigne valores que corrompan las reglas lógicas establecidas.

Gracias a esta ocultación, la clase obliga al código externo a comunicarse a través de sus métodos públicos. Estos métodos actúan como "guardianes" o filtros: antes de modificar el estado interno oculto, comprueban los parámetros recibidos. Si los datos cumplen con la invariante, la modificación se realiza; si la violan, el método puede rechazar la operación (por ejemplo, lanzando un error), asegurando así que el objeto mantenga siempre su propia integridad.

A continuación, se ilustra este concepto con un ejemplo en Java:

public class Fraccion {
    private int numerador;
    // Invariante de clase: el denominador NUNCA puede ser 0
    private int denominador; 

    public Fraccion(int numerador, int denominador) {
        if (denominador == 0) { // El constructor valida la invariante inicial
            throw new IllegalArgumentException("El denominador no puede ser 0");
        }
        this.numerador = numerador;
        this.denominador = denominador;
    }

    // La interfaz pública actúa como guardián de la invariante
    public void setDenominador(int nuevoDenominador) {
        if (nuevoDenominador == 0) {
            // Se rechaza la operación para no romper la regla lógica
            throw new IllegalArgumentException("Violación de invariante: denominador 0");
        }
        this.denominador = nuevoDenominador;
    }
}

stateDiagram-v2
    [*] --> EstadoValido : Constructor valida datos
    
    EstadoValido --> EstadoValido : Método público con datos correctos
    EstadoValido --> EstadoInvalido : Intento de asignación directa
    
    note right of EstadoInvalido
        Operación BLOQUEADA
        gracias al modificador
        'private'
    end note


## 5. Pon un ejemplo de una clase `Punto` en `Java`, con dos coordenadas, `x` e `y`, de tipo `double`, con un método `calcularDistanciaAOrigen`, y que haga uso de la ocultación de información. ¿Cuál es la interfaz pública de la clase `Punto`? ¿Qué significa `public` y `private`?

A continuación, se presenta el diseño de la clase Punto aplicando rigurosamente los principios de encapsulación. Las coordenadas se protegen para que no puedan ser alteradas de forma incontrolada desde el exterior, mientras que se proporciona un comportamiento específico para calcular la distancia al origen de coordenadas $(0,0)$ utilizando el teorema de Pitágoras (calculando la hipotenusa mediante la fórmula $\sqrt{x^2 + y^2}$).

public class Punto {
    // Ocultación de información: el estado interno es inaccesible desde el exterior
    private double x;
    private double y;

    // Parte de la interfaz pública: el constructor para inicializar el estado
    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Parte de la interfaz pública: comportamiento expuesto al resto del programa
    public double calcularDistanciaAOrigen() {
        // Se accede a 'x' e 'y' internamente sin problemas
        return Math.sqrt((this.x * this.x) + (this.y * this.y));
    }
}

En este ejemplo de implementación, la interfaz pública de la clase Punto está compuesta exclusivamente por su constructor Punto(double x, double y) y el método calcularDistanciaAOrigen(). Estos son los únicos puntos de entrada que el resto de las clases del programa pueden invocar para interactuar con un objeto instanciado de tipo Punto. Todo lo demás permanece invisible para el usuario de la clase.

Los modificadores de acceso determinan esta visibilidad: el modificador private significa que los atributos x e y solo pueden ser leídos o modificados desde las propias líneas de código contenidas dentro de las llaves {} de la clase Punto (logrando así la ocultación de información). Por el contrario, el modificador public indica que el elemento es accesible sin restricciones desde cualquier otra clase del proyecto, definiendo el contrato o los servicios que nuestro objeto ofrece.

classDiagram
    class Punto {
        -double x
        -double y
        +Punto(double x, double y)
        +calcularDistanciaAOrigen() double
    }
    note for Punto "La interfaz pública está formada por\nlos elementos marcados con '+'"


## 6. En Java, ¿A quiénes se pueden aplicar los modificadores `public` o `private`?

En el lenguaje Java, los modificadores de control de acceso public y private se aplican principalmente a los miembros de una clase. Esto abarca los atributos (las variables que guardan el estado), los métodos (los comportamientos) y los constructores. Al aplicar estos modificadores a los miembros, el programador define con precisión quirúrgica qué partes formarán la interfaz pública del objeto y qué partes quedarán relegadas a la implementación interna oculta, materializando así el principio de encapsulación.

Además de a los miembros, estos modificadores también se pueden aplicar a la declaración de las clases e interfaces en sí mismas, aunque existe una restricción técnica fundamental. Una clase de "nivel superior" (aquella que declaras directamente en su propio archivo .java) puede ser public para ser importada y utilizada desde cualquier paquete del proyecto, pero nunca puede ser declarada como private. El uso del modificador private en la definición de clases está reservado exclusivamente para las "clases anidadas" (clases que se programan dentro de otras clases), cuyo único propósito es servir como una estructura de apoyo puramente interna.

A continuación, se muestra un ejemplo esquemático en código ilustrando todos los elementos que pueden recibir estos modificadores:

// 1. Modificador public aplicado a una clase de nivel superior
public class Coche {
    
    // 2. Modificador private aplicado a atributos (estado interno protegido)
    private String matricula;
    private double velocidadActual;

    // 3. Modificador public aplicado a un constructor
    public Coche(String matricula) {
        this.matricula = matricula;
        this.velocidadActual = 0.0; // Invariante inicial
    }

    // 4. Modificador public aplicado a un método (parte de la interfaz pública)
    public void acelerar(double incremento) {
        if (incremento > 0) {
            this.velocidadActual += incremento;
            registrarTelemetria(); // Llama a un método auxiliar oculto
        }
    }

    // 5. Modificador private aplicado a un método (uso puramente interno)
    private void registrarTelemetria() {
        // Esta lógica interna no puede ser invocada desde fuera de la clase Coche
        System.out.println("Telemetría: " + this.velocidadActual);
    }
    
    // 6. Modificador private aplicado a una clase anidada (uso exclusivo de Coche)
    private class MotorInterno {
        // Lógica del motor...
    }
}

graph TD
    subgraph Elementos de Java
        A[Clases de Nivel Superior]
        B[Atributos]
        C[Constructores]
        D[Métodos]
        E[Clases Anidadas]
    end
    
    F((public)) --> A
    F --> B
    F --> C
    F --> D
    F --> E
    
    G((private)) -.->|Acción No Permitida ❌| A
    G --> B
    G --> C
    G --> D
    G --> E
    
    style F fill:#c8e6c9,stroke:#2e7d32
    style G fill:#ffcdd2,stroke:#c62828


## 7. En POO, la visibilidad puede ser pública o privada, pero ¿existen más tipos de visibilidad? ¿Qué ocurre en Java? ¿Y en otros lenguajes?

En la Programación Orientada a Objetos, más allá de la visibilidad pública y privada, existen otros niveles diseñados para ofrecer un control más granular sobre la encapsulación, especialmente cuando entran en juego la herencia y la agrupación de clases. En Java, concretamente, existen cuatro niveles de acceso. Además de public (accesible desde cualquier parte) y private (accesible solo desde la propia clase), se incorporan el modificador protected y la visibilidad por defecto (conocida como package-private o visibilidad de paquete), que se aplica cuando no se escribe ningún modificador explícito.

El modificador protected en Java permite que un miembro sea accesible desde su propia clase, desde otras clases del mismo paquete y, de forma crucial, desde sus subclases (clases hijas que heredan de ella), incluso si estas últimas se encuentran en paquetes diferentes. Por su parte, la visibilidad por defecto restringe el acceso estrictamente a las clases que residen dentro del mismo paquete, fomentando la cohesión entre clases que trabajan juntas en un mismo módulo sin exponer sus detalles al resto del sistema.

En otros lenguajes orientados a objetos, el modelo de visibilidad presenta variaciones. En C++, por ejemplo, se utilizan public, private y protected con un comportamiento similar respecto a la herencia, pero al no existir el concepto de paquete como en Java, se emplea la palabra clave friend para otorgar acceso especial a clases o funciones externas concretas. Por otro lado, lenguajes como Python no implementan una ocultación estricta a nivel de compilador; en su lugar, se basan en convenciones de nomenclatura, prefijando los atributos con guiones bajos (como _atributo o __atributo) para indicar a otros programadores que dicho miembro debe tratarse como privado o protegido, delegando la responsabilidad de no romper la encapsulación al desarrollador.

A continuación se ilustran los cuatro niveles de visibilidad en Java:

package mi_paquete;

public class EjemploVisibilidad {
    // 1. Accesible desde cualquier lugar del proyecto
    public int datoPublico;      
    
    // 2. Accesible en el mismo paquete y por subclases en otros paquetes
    protected int datoProtegido; 
    
    // 3. Accesible SOLO dentro de 'mi_paquete' (package-private)
    int datoPorDefecto;          
    
    // 4. Accesible SOLO dentro de las llaves de 'EjemploVisibilidad'
    private int datoPrivado;     

    public EjemploVisibilidad() {
        // Dentro de la propia clase, todos los niveles son accesibles
        this.datoPublico = 1;
        this.datoProtegido = 2;
        this.datoPorDefecto = 3;
        this.datoPrivado = 4;
    }
}

graph LR
    subgraph Niveles de Visibilidad en Java
    A[public] -->|Todo el proyecto| B((🌍))
    C[protected] -->|Paquete + Subclases| D((📦 + 👶))
    E[default / sin modificador] -->|Solo mismo paquete| F((📦))
    G[private] -->|Solo misma clase| H((🔒))
    end
    
    style A fill:#c8e6c9,stroke:#2e7d32
    style C fill:#fff9c4,stroke:#fbc02d
    style E fill:#ffecb3,stroke:#ff8f00
    style G fill:#ffcdd2,stroke:#c62828


## 8. Responde: Los miembros de instancia privados de un objeto están ocultos para (a) otras clases o (b) otras instancias, aunque sean de la misma clase. Pon un ejemplo añadiendo un método `calcularDistanciaAPunto(Punto otro)` y explica la respuesta.

En Java (y en la inmensa mayoría de lenguajes orientados a objetos), los miembros de instancia privados de un objeto están ocultos exclusivamente para (a) otras clases. Esto significa que el modificador private define y aplica la restricción de visibilidad a nivel de la clase, no a nivel de la instancia (el objeto en memoria). Por lo tanto, un objeto puede acceder libremente y de forma directa a los atributos o métodos privados de cualquier otro objeto, siempre y cuando ambos hayan sido instanciados a partir exactamente de la misma clase.

Esta característica técnica de diseño resulta fundamental para poder implementar operaciones binarias o estructurales donde dos objetos del mismo tipo deben interactuar íntimamente. Si la privacidad funcionara a nivel de instancia, nos veríamos obligados a crear métodos públicos auxiliares para exponer los datos internos, rompiendo la encapsulación general frente a todo el programa solo para permitir que dos objetos idénticos se comunicaran entre sí. Al mantener la barrera de privacidad a nivel de clase, conservamos la interfaz pública inmaculada y segura frente a agentes verdaderamente externos.

Como se observa en el siguiente código, al programar el método dentro de las llaves de la clase Punto, el compilador nos otorga permisos plenos sobre cualquier referencia de tipo Punto que manejemos (en este caso, el parámetro otro). Acceder a otro.x u otro.y es totalmente lícito a pesar de su condición de privacidad.

public class Punto {
    private double x;
    private double y;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // El objeto actual (this) interactúa con otra instancia (otro) de la misma clase
    public double calcularDistanciaAPunto(Punto otro) {
        // Podemos acceder directamente a los atributos privados de la instancia 'otro'
        // sin necesidad de usar métodos públicos, porque estamos dentro de la clase Punto.
        double diferenciaX = this.x - otro.x;
        double diferenciaY = this.y - otro.y;
        
        return Math.sqrt((diferenciaX * diferenciaX) + (diferenciaY * diferenciaY));
    }
}

graph TD
    subgraph Dominio de la Clase Punto (Visibilidad Compartida)
        A[Objeto P1 <br/> this.x, this.y]
        B[Objeto P2 <br/> otro.x, otro.y]
        A <-->|Acceso Directo Permitido ✅| B
    end
    C[Clase Externa<br/>ej: Main] -->|Puede invocar calcularDistanciaAPunto| A
    C -.->|No puede acceder a x ni a y ❌| B
    
    style A fill:#c8e6c9,stroke:#2e7d32
    style B fill:#c8e6c9,stroke:#2e7d32
    style C fill:#ffcdd2,stroke:#c62828


## 9. ¿Qué son los métodos "getter" y "setter" en los lenguajes orientados a objetos?

En la Programación Orientada a Objetos, los métodos "getter" (consultores o accesores) y "setter" (modificadores o mutadores) son métodos públicos convencionales cuya principal responsabilidad es permitir la lectura y la escritura, respectivamente, del valor de un atributo privado de una clase. Su nomenclatura estándar en Java consiste en anteponer el prefijo get o set al nombre del atributo con la primera letra en mayúscula (por ejemplo, getNombre() y setNombre()). Existe una excepción habitual: para los métodos "getter" que devuelven un valor lógico o booleano, se suele emplear el prefijo is (como isValidado()).

Estos métodos representan la materialización práctica del principio de encapsulación y ocultación de información. Al obligar al resto del programa a utilizar un "setter" en lugar de modificar la variable de memoria directamente, la clase adquiere el control absoluto sobre los datos que recibe. Esto permite al programador introducir validaciones lógicas dentro del método para proteger las invariantes de la clase, rechazar datos anómalos, o incluso cambiar la estructura interna de los datos en el futuro sin que el código cliente (que seguirá llamando a los mismos métodos públicos) se vea afectado.

A continuación, se muestra un ejemplo clásico donde el "setter" actúa como guardián del estado de un objeto:

public class Persona {
    private int edad; // Atributo encapsulado

    // Getter: Permite consultar el estado interno de forma controlada
    public int getEdad() {
        return this.edad;
    }

    // Setter: Controla y valida la modificación del estado interno
    public void setEdad(int nuevaEdad) {
        // El setter protege la invariante lógica: la edad no puede ser negativa
        if (nuevaEdad >= 0) {
            this.edad = nuevaEdad;
        } else {
            throw new IllegalArgumentException("La edad no puede ser negativa.");
        }
    }
}

flowchart LR
    A[Código Externo<br/>ej: Main] -->|Llamada a getEdad()| B(Método Getter)
    A -->|Llamada a setEdad(25)| C(Método Setter)
    
    B -->|Retorna el valor actual| D[(Atributo privado<br/>int edad)]
    C -->|Valida regla y actualiza| D
    
    style A fill:#fff3e0,stroke:#ef6c00
    style B fill:#c8e6c9,stroke:#2e7d32
    style C fill:#c8e6c9,stroke:#2e7d32
    style D fill:#ffcdd2,stroke:#c62828


## 10. Cuando nos referimos a que la ocultación de información mejora la "seguridad" del programa, ¿nos referimos a que no pueda ser "hackeado"?

En el contexto de la Programación Orientada a Objetos, cuando se afirma que la ocultación de información mejora la "seguridad", no se hace referencia a la seguridad informática o a la protección contra ataques malintencionados (ciberseguridad o evitar ser "hackeado"). Más bien, se emplea el término en el sentido de "seguridad del código" (del inglés safety o thread-safety), es decir, en la creación de programas robustos, estables y libres de errores lógicos (bugs).

El objetivo principal de esta seguridad metodológica es proteger la integridad del estado de los objetos frente a los propios desarrolladores del sistema. Al declarar los atributos como privados, se evita que otros módulos del programa modifiquen los datos de forma accidental, directa e incontrolada, previniendo así la ruptura de las invariantes de clase. Se trata de un mecanismo de ingeniería de software para organizar el código, delimitar responsabilidades y evitar usos incorrectos de las clases dentro del equipo de programación.

Si un programa está expuesto a un atacante con conocimientos técnicos y acceso al entorno de ejecución, la ocultación de información tradicional no supone una barrera real. En lenguajes como Java, existen herramientas avanzadas (como la API de Reflection) que permiten inspeccionar y modificar variables privadas en tiempo de ejecución. Por tanto, la encapsulación es una norma de buen diseño arquitectónico, no un sistema criptográfico ni un escudo contra intrusiones externas o lectura de memoria.

A continuación, se ilustra conceptualmente esta diferencia:

public class SistemaSeguridad {
    // Esto proporciona "safety" (evita errores del programador), no "security" (contra hackers)
    private int pinAcceso; 

    public SistemaSeguridad(int pin) {
        this.pinAcceso = pin;
    }

    public boolean validarPin(int intento) {
        return this.pinAcceso == intento;
    }
    
    /* * Nota: Un programador despistado no puede hacer accidentalmente:
     * sistema.pinAcceso = 0000; // Error de compilación (Safety)
     * * Sin embargo, un atacante que inyecte código malicioso podría usar:
     * Field campo = SistemaSeguridad.class.getDeclaredField("pinAcceso");
     * campo.setAccessible(true); // Se salta la restricción 'private' (Security break)
     */
}

graph TD
    subgraph Ocultación de Información POO
        A[Atributo 'private']
        B[Evita modificaciones accidentales ✅]
        C[Mantiene invariantes de la clase ✅]
        D[Reduce bugs del equipo de desarrollo ✅]
        A --> B & C & D
    end
    
    subgraph Amenazas de Ciberseguridad
        E[Lectura directa de memoria RAM ❌]
        F[Uso de Reflection en Java ❌]
        G[Ataques de Inyección ❌]
        A -.->|NO ofrece protección contra| E & F & G
    end
    
    style A fill:#c8e6c9,stroke:#2e7d32
    style E fill:#ffcdd2,stroke:#c62828
    style F fill:#ffcdd2,stroke:#c62828
    style G fill:#ffcdd2,stroke:#c62828


## 11. ¿Qué diferencia hay entre **miembro de instancia** y **miembro de clase**? ¿Los miembros de clase también se pueden ocultar?

En Programación Orientada a Objetos, la diferencia fundamental radica en a quién pertenece el estado o el comportamiento en la memoria. Un miembro de instancia (atributo o método normal) pertenece a un objeto específico creado a partir de la clase. Cada objeto instanciado posee su propia copia independiente de los atributos, y sus métodos operan exclusivamente sobre ese estado particular utilizando la referencia interna (como this en Java). Por el contrario, un miembro de clase pertenece a la clase en sí misma, compartiéndose una única copia en memoria para todos los objetos de dicho tipo, independientemente de cuántos se hayan creado (incluso si no se ha creado ninguno). En Java, los miembros de clase se definen utilizando la palabra reservada static.

Respecto a la ocultación, los miembros de clase sí se pueden ocultar aplicando exactamente los mismos modificadores de acceso (private, protected, etc.) que a los miembros de instancia. Declarar un atributo estático como private garantiza que este dato compartido no pueda ser alterado de forma incontrolada desde el exterior. Esto resulta imprescindible para proteger constantes internas de la clase, mantener contadores globales de instancias creadas o gestionar recursos compartidos, obligando al resto del programa a utilizar métodos estáticos públicos ("getters" de clase) para consultarlos.

A continuación, se ilustra esta diferencia con un ejemplo en Java, mostrando cómo un miembro de clase oculto interactúa con los objetos instanciados:

public class Vehiculo {
    // Miembro de clase (static) y oculto (private).
    // Pertenece a la clase Vehiculo globalmente, no a un coche concreto.
    private static int totalVehiculosFabricados = 0;

    // Miembro de instancia y oculto (private).
    // Cada objeto Vehiculo tendrá su propio identificador único.
    private int idVehiculo;

    public Vehiculo() {
        // Al crear una instancia, modificamos el estado global compartido de la clase
        totalVehiculosFabricados++; 
        // Y asignamos el estado particular del objeto instanciado
        this.idVehiculo = totalVehiculosFabricados;
    }

    // Método de clase (static) que forma parte de la interfaz pública.
    // Permite consultar el estado global sin necesidad de instanciar un Vehiculo.
    public static int getTotalFabricados() {
        return totalVehiculosFabricados;
    }
    
    // Método de instancia. Necesita un objeto concreto para ser invocado.
    public int getIdVehiculo() {
        return this.idVehiculo; // Accede al estado particular de la instancia
    }
}

graph TD
    subgraph Memoria Compartida de la Clase
        A[Clase Vehiculo<br/>static totalVehiculosFabricados = 2]
    end
    
    subgraph Memoria Heap de las Instancias
        B[Objeto Vehiculo 1<br/>idVehiculo = 1]
        C[Objeto Vehiculo 2<br/>idVehiculo = 2]
    end
    
    B -.->|Actualiza y Lee| A
    C -.->|Actualiza y Lee| A
    
    style A fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    style B fill:#e3f2fd,stroke:#1565c0
    style C fill:#e3f2fd,stroke:#1565c0


## 12. Brevemente: ¿Tiene sentido que los constructores sean privados?

Sí, tiene pleno sentido y constituye una práctica de diseño avanzado muy habitual en la Programación Orientada a Objetos. Declarar un constructor como private impide que cualquier clase externa pueda instanciar objetos de dicha clase utilizando el operador new. Esta restricción es una forma de ocultación de información que resulta fundamental cuando se desea tomar el control absoluto sobre cómo, cuándo y cuántas instancias de una clase se pueden crear en el sistema.

Uno de los escenarios más comunes para esta técnica son las denominadas "clases de utilidad". Estas clases (como java.lang.Math en la biblioteca estándar de Java) están diseñadas para agrupar únicamente constantes y métodos de clase (static). Dado que no poseen un estado de instancia que deba ser inicializado, carece de sentido crear objetos de las mismas. Al privatizar el constructor, se bloquea la instanciación accidental, comunicando claramente a otros programadores que la clase debe utilizarse de forma puramente estática.

Otro uso primordial de los constructores privados se da en la implementación de patrones de diseño creacionales, como el patrón Singleton (que garantiza la existencia de una única instancia de la clase en toda la aplicación) o el uso de "métodos factoría". En estos casos, al ocultar el constructor, se obliga al código cliente a solicitar los objetos a través de métodos públicos estáticos, los cuales actúan como intermediarios y deciden si se debe crear un nuevo objeto, si se devuelve uno ya existente, o si se aplica alguna lógica de validación previa.

A continuación, se ilustra cómo se utiliza un constructor privado en el patrón Singleton:

public class GestorConfiguracion {
    // Variable estática que almacena la única instancia permitida
    private static GestorConfiguracion instanciaUnica;

    // 1. Constructor privado: nadie desde fuera puede hacer 'new GestorConfiguracion()'
    private GestorConfiguracion() {
        // Inicialización de la configuración...
    }

    // 2. Método de clase (static) que controla la creación
    public static GestorConfiguracion getInstancia() {
        if (instanciaUnica == null) {
            // Como estamos dentro de la clase, sí podemos usar el constructor privado
            instanciaUnica = new GestorConfiguracion();
        }
        return instanciaUnica;
    }
}

graph TD
    A[Código Cliente<br/>Otras clases] -.->|Intento de usar 'new'<br/>Acceso Denegado ❌| B[Constructor private]
    A -->|Llamada permitida ✅| C[Método public static<br/>getInstancia]
    C -->|Controla el acceso y llama a| B
    
    style A fill:#fff3e0,stroke:#ef6c00
    style B fill:#ffcdd2,stroke:#c62828
    style C fill:#c8e6c9,stroke:#2e7d32


## 13. ¿Cómo se indican los **miembros de clase** en Java? Pon un ejemplo, en la clase `Punto` definida anteriormente, para que incluya miembros de clase que permitan saber cuáles son los valores `x` e `y` máximos que se han establecido en todos los puntos que se hayan creado hasta el momento.

En Java, los miembros de clase se indican utilizando la palabra reservada static en su declaración. Al aplicar este modificador a un atributo o a un método, se establece que dicho elemento pertenece a la clase en su conjunto y no a una instancia específica. En consecuencia, existirá una única copia en memoria de ese atributo compartida por todos los objetos de la clase, lo que resulta el mecanismo ideal para mantener estados globales, contadores compartidos o, como en este caso, un registro histórico de los valores máximos alcanzados.

Para incorporar esta funcionalidad a la clase Punto, se deben declarar dos atributos estáticos adicionales, por ejemplo, maxX y maxY. La lógica de actualización debe ubicarse dentro del constructor de la clase; así, cada vez que se instancie un nuevo objeto Punto (y solo en ese momento), el programa comparará las coordenadas recién introducidas con los máximos históricos almacenados en las variables estáticas y las actualizará si las nuevas coordenadas son superiores. Para que el primer punto creado registre correctamente sus valores, es conveniente inicializar estos atributos estáticos con el valor mínimo representable por el tipo de dato.

Finalmente, para respetar rigurosamente el principio de ocultación de información, estos atributos estáticos deben declararse como privados (private). Para permitir que el resto del programa consulte dichos valores históricos, se debe proporcionar una interfaz pública a nivel de clase mediante métodos "getter" que también sean estáticos (public static double getMaxX()). De este modo, se podrá acceder a la información global invocando el método directamente sobre el nombre de la clase (ej. Punto.getMaxX()), sin necesidad de tener instanciado ningún objeto.

public class Punto {
    // Miembros de instancia (estado individual e independiente de cada punto)
    private double x;
    private double y;

    // Miembros de clase (estado global compartido por todos los puntos)
    // Inicializados al valor mínimo posible (Double.NEGATIVE_INFINITY o un valor análogo)
    private static double maxX = Double.NEGATIVE_INFINITY;
    private static double maxY = Double.NEGATIVE_INFINITY;

    public Punto(double x, double y) {
        this.x = x;
        this.y = y;
        
        // Actualización del estado estático compartido comparando valores
        if (x > maxX) {
            maxX = x;
        }
        if (y > maxY) {
            maxY = y;
        }
    }

    public double calcularDistanciaAOrigen() {
        return Math.sqrt((this.x * this.x) + (this.y * this.y));
    }

    // Métodos de clase (getters estáticos para la interfaz pública global)
    public static double getMaxX() {
        return maxX;
    }

    public static double getMaxY() {
        return maxY;
    }
}

classDiagram
    class Punto {
        <<class>>
        -static double maxX
        -static double maxY
        +static getMaxX() double
        +static getMaxY() double
    }
    class InstanciaPunto1 {
        -double x = 5.0
        -double y = 2.0
    }
    class InstanciaPunto2 {
        -double x = 1.0
        -double y = 8.0
    }
    
    InstanciaPunto1 ..> Punto : Actualiza estado global\n(maxX=5.0, maxY=2.0)
    InstanciaPunto2 ..> Punto : Actualiza estado global\n(maxY sube a 8.0)
    
    note for Punto "Estado global resultante:\nmaxX = 5.0\nmaxY = 8.0"


## 14. Como sería un método factoría dentro de la clase `Punto` para construir un `Punto` a partir de dos coordenadas, pero que las redondee al entero más cercano. Escribe sólo el código del método, no toda la clase ¿Has usado `static`? 

Para implementar un método factoría es estrictamente necesario utilizar el modificador static. Al ser estático, el método se convierte en un miembro de clase, lo que permite que el resto del programa pueda invocarlo directamente utilizando el nombre de la clase (por ejemplo, Punto.crearPuntoRedondeado(3.2, 5.8)) sin necesidad de tener instanciado un objeto Punto previamente. Si no fuera estático, se daría la paradoja de necesitar un objeto ya creado para poder pedirle que cree otro.

El propósito de este método es encapsular una lógica de creación específica. Recibe las coordenadas originales, aplica el procesamiento matemático necesario (en este caso, el redondeo al entero más cercano) y, finalmente, delega la creación real invocando internamente al constructor de la clase con los valores ya procesados. A continuación, se muestra el código de dicho método:

// Método factoría estático que devuelve una nueva instancia de Punto
    public static Punto crearPuntoRedondeado(double x, double y) {
        // Se emplea Math.round para redondear al entero más cercano.
        // Math.round(double) devuelve un tipo 'long'.
        long xRedondeado = Math.round(x);
        long yRedondeado = Math.round(y);
        
        // Se invoca al constructor instanciando el objeto con los valores redondeados
        return new Punto((double) xRedondeado, (double) yRedondeado);
    }

graph LR
    A[Código Cliente<br/>Otras Clases] -->|1. Llama al método estático<br/>Punto.crearPuntoRedondeado| B(Método Factoría<br/>Clase Punto)
    B -->|2. Aplica Math.round| C[Valores Enteros]
    C -->|3. Invoca constructor| D{new Punto(...)}
    D -->|4. Retorna nueva instancia| A
    
    style A fill:#fff3e0,stroke:#ef6c00
    style B fill:#c8e6c9,stroke:#2e7d32
    style D fill:#e3f2fd,stroke:#1565c0


## 15. Cambia la implementación de `Punto`. En vez de dos `double`, emplea un array interno de dos posiciones, intentando no modificar la interfaz pública de la clase.

El cambio de la estructura de datos interna de una clase sin alterar su interfaz pública es la demostración más clara de los beneficios de la ocultación de información. Al haber declarado originalmente las coordenadas como atributos privados, ninguna clase externa dependía directamente de las variables x e y. Por lo tanto, es posible sustituir estas variables individuales por una estructura diferente, como un array de tipo double de dos posiciones, sin provocar errores de compilación en el resto del sistema.

Para lograr esto manteniendo intacto el "contrato" de la clase, las firmas de los métodos públicos no deben sufrir alteraciones. El constructor seguirá recibiendo dos parámetros double (x e y), pero su lógica interna cambiará para instanciar el array y guardar esos valores en los índices 0 y 1 respectivamente. De igual forma, el método calcularDistanciaAOrigen() mantendrá su nombre y valor de retorno, pero adaptará su cálculo matemático para extraer los datos desde las posiciones del array.

A continuación, se presenta el código refactorizado de la clase Punto:

public class Punto {
    // El estado interno cambia radicalmente, pero sigue siendo privado (oculto)
    private double[] coordenadas;

    // La interfaz pública se mantiene INTACTA. 
    // Las clases externas no notarán que el interior ha cambiado.
    public Punto(double x, double y) {
        // Se inicializa el array y se guardan los parámetros
        this.coordenadas = new double[2];
        this.coordenadas[0] = x;
        this.coordenadas[1] = y;
    }

    // La interfaz pública se mantiene INTACTA.
    public double calcularDistanciaAOrigen() {
        // La lógica interna se adapta a la nueva estructura de datos
        double cx = this.coordenadas[0];
        double cy = this.coordenadas[1];
        
        return Math.sqrt((cx * cx) + (cy * cy));
    }
}

graph LR
    subgraph Código Cliente
        A[main] -->|new Punto(5.0, 3.0)| B(Interfaz Pública Invariable)
        A -->|calcularDistanciaAOrigen()| B
    end
    
    subgraph Clase Punto
        B -->|Antes: private double x, y| C[Versión Antigua]
        B -->|Ahora: private double[] coord| D[Versión Refactorizada]
    end
    
    style A fill:#fff3e0,stroke:#ef6c00
    style B fill:#c8e6c9,stroke:#2e7d32,stroke-width:2px
    style C fill:#ffcdd2,stroke:#c62828,stroke-dasharray: 5 5
    style D fill:#bbdefb,stroke:#1565c0


## 16. Si un atributo va a tener un método "getter" y "setter" públicos, ¿no es mejor declararlo público? ¿Cuál es la convención más habitual sobre los atributos, que sean públicos o privados? ¿Tiene esto algo que ver con las "invariantes de clase"?

Aunque a primera vista pueda parecer redundante y menos directo tener un atributo privado acompañado de un "getter" y un "setter" públicos, la convención universal y más estricta en la Programación Orientada a Objetos es declarar siempre los atributos como private. Exponer un atributo como public acopla irreversiblemente la representación interna de los datos al resto del programa. Si en el futuro se necesitara cambiar el tipo de dato de ese atributo o calcularlo al vuelo en lugar de almacenarlo, habría que modificar cada línea de código externo que accediera a él directamente, violando los principios de mantenibilidad.

Esta convención está intrínsecamente ligada al concepto de "invariantes de clase". Si un atributo se expone públicamente, cualquier agente externo puede asignarle un valor inválido o inconsistente en cualquier momento, corrompiendo el estado del objeto de forma silenciosa. Al forzar el acceso a través de un método "setter", se establece un punto de control centralizado (un "cuello de botella" arquitectónico) donde se puede introducir lógica de validación, lanzar excepciones si los datos son incorrectos y garantizar matemáticamente que las invariantes de la clase jamás se rompan.

Además de proteger las invariantes, el uso de métodos de acceso proporciona una flexibilidad fundamental en el diseño. Permite, por ejemplo, convertir un atributo en "solo lectura" simplemente omitiendo la programación de su "setter", o registrar eventos secundarios (como actualizar una interfaz gráfica o escribir en un archivo de log) cada vez que el valor interno sea modificado, acciones que serían imposibles si la asignación se realizara directamente sobre una variable pública.

public class CuentaBancaria {
    // Convención habitual: Estado privado
    private double saldo;

    // Si el atributo fuera public, cualquiera podría hacer: cuenta.saldo = -5000;
    // Rompiendo la invariante de que el saldo no puede ser negativo por asignación directa.

    public void setSaldo(double nuevoSaldo) {
        // El setter protege la invariante de clase
        if (nuevoSaldo >= 0) {
            this.saldo = nuevoSaldo;
        } else {
            System.out.println("Error: Violación de invariante. Saldo negativo.");
        }
    }
}

graph TD
    subgraph Atributo Público ❌
        A[Código Cliente] -->|cuenta.saldo = -500| B[Atributo 'saldo']
        B -.->|Invariante Rota| C((¡ERROR LÓGICO!))
    end
    
    subgraph Atributo Privado + Setter ✅
        D[Código Cliente] -->|cuenta.setSaldo(-500)| E(Método setSaldo)
        E -->|Valida: ¿Es menor que 0?| F{Bloqueo}
        F -->|Rechazado| G((ESTADO SEGURO))
    end
    
    style B fill:#ffcdd2,stroke:#c62828
    style C fill:#ffcdd2,stroke:#c62828
    style E fill:#c8e6c9,stroke:#2e7d32
    style G fill:#c8e6c9,stroke:#2e7d32


## 17. ¿Qué significa que una clase sea **inmutable**? ¿qué es un método modificador? ¿Un método modificador es siempre un "setter"? ¿Tiene ventajas que una clase sea inmutable?

Una clase es inmutable cuando el estado de sus objetos (sus datos internos) no puede ser alterado bajo ninguna circunstancia una vez que han sido construidos e inicializados en memoria. En Java, esto se logra habitualmente declarando sus atributos como private y final, proveyendo únicamente métodos de consulta ("getters") y omitiendo cualquier tipo de funcionalidad que permita la reasignación de dichos valores tras la ejecución del constructor. Por otro lado, un método modificador (o mutador) es cualquier método cuyo propósito o efecto directo sea alterar el valor de uno o varios atributos que conforman el estado interno del objeto.

Es importante destacar que un método modificador no es siempre un "setter". Aunque los "setters" (como setCoordenadaX(double)) son el ejemplo más directo y estandarizado de mutadores, existen multitud de métodos que modifican el estado sin seguir la convención de nomenclatura set o implicando lógicas mucho más complejas que una simple asignación. Por ejemplo, operaciones como ingresarDinero(double cantidad) en una cuenta bancaria, o acelerar(double incremento) en un vehículo, alteran los atributos del objeto y, por tanto, son métodos modificadores en toda regla. Evidentemente, una clase inmutable carece por completo de cualquier método modificador (sea "setter" o no).

Las ventajas de diseñar clases inmutables son muy significativas. Al no poder cambiar, sus objetos son inherentemente seguros en entornos de ejecución concurrente (multihilo), ya que evitan problemas de sincronización y colisiones de escritura. Además, su ciclo de vida es totalmente predecible: una vez validadas las invariantes en el constructor, es imposible que el objeto entre en un estado inválido en el futuro. Esto permite a los programadores compartir la misma referencia de un objeto por todo el sistema de forma completamente segura, eliminando la necesidad de realizar costosas "copias defensivas" de los datos.

A continuación, se ilustra cómo se diseña una clase inmutable en Java. Si se requiere realizar una operación que "modifique" conceptualmente el objeto, la convención dicta que se debe retornar una nueva instancia con el estado resultante, dejando la original intacta:

// Se suele marcar la clase como 'final' para evitar que subclases rompan la inmutabilidad
public final class PuntoInmutable {
    // El modificador 'final' obliga a inicializar en el constructor y prohíbe reasignaciones
    private final double x;
    private final double y;

    public PuntoInmutable(double x, double y) {
        this.x = x;
        this.y = y;
    }

    // Métodos consultores (getters) permitidos
    public double getX() { return this.x; }
    public double getY() { return this.y; }

    // NO existen métodos modificadores ni setters.
    // Para aplicar un cambio, se construye y retorna un NUEVO objeto.
    public PuntoInmutable trasladar(double desplazamientoX, double desplazamientoY) {
        return new PuntoInmutable(this.x + desplazamientoX, this.y + desplazamientoY);
    }
}

graph TD
    subgraph Objeto Mutable Clásico
        A[Punto M<br/>x=2, y=3] -->|Llamada a<br/>trasladar(1,1)| B[Mismo Punto M<br/>x=3, y=4]
        note1[Se modifica la memoria existente]
        B -.-> note1
    end
    
    subgraph Objeto Inmutable
        C[Punto I_1<br/>x=2, y=3] -->|Llamada a<br/>trasladar(1,1)| D[Punto I_2<br/>x=3, y=4]
        note2[Punto I_1 permanece INTACTO<br/>Se crea un nuevo objeto]
        C -.-> note2
        D -.-> note2
    end
    
    style A fill:#ffcdd2,stroke:#c62828
    style B fill:#ffcdd2,stroke:#c62828
    style C fill:#c8e6c9,stroke:#2e7d32
    style D fill:#c8e6c9,stroke:#2e7d32


## 18. ¿Es recomendable incluir métodos "setter" siempre y como convención?

No, en absoluto. Incluir métodos "setter" (modificadores) por defecto o como una convención sistemática para todos los atributos de una clase es una mala práctica en la Programación Orientada a Objetos. Si se exponen automáticamente todos los datos internos a través de métodos de escritura, se está rompiendo el propósito fundamental de la encapsulación, ya que el estado del objeto queda a merced de modificaciones externas arbitrarias, perdiendo el control sobre su integridad estructural y lógica.

El diseño correcto dicta que una clase solo debe proporcionar métodos modificadores para aquellos atributos cuyo cambio de valor tenga un sentido lógico y justificado dentro del dominio del problema que se está modelando. Existen multitud de datos que, por su naturaleza, nacen con el objeto y jamás deberían cambiar a lo largo de su ciclo de vida. Por ejemplo, en una clase Empleado, atributos como el DNI o la fecha de contratación deben establecerse a través del constructor y permanecer inalterables; proveer un método setDni() abriría la puerta a errores críticos de identidad en el sistema.

La estrategia más recomendable es diseñar las clases buscando el mayor grado de inmutabilidad posible. Se deben exigir todos los datos obligatorios en el constructor, protegerlos con el modificador private (y preferiblemente final si no van a cambiar) y escribir métodos "setter" única y exclusivamente cuando el comportamiento esperado del objeto requiera explícitamente la mutación de ese estado particular a posteriori. Esta reducción del estado mutable minimiza los efectos secundarios y facilita enormemente la depuración del código.

A continuación, se ilustra un diseño selectivo frente al uso indiscriminado de mutadores:

public class Empleado {
    // Atributo inmutable: se inicializa una vez en el constructor y no tiene setter
    private final String dni;
    
    // Atributo mutable: tiene sentido lógico que su valor cambie con el tiempo
    private double salario;

    public Empleado(String dni, double salarioInicial) {
        this.dni = dni;
        this.salario = salarioInicial;
    }

    // Getter para el DNI (lectura permitida)
    public String getDni() {
        return this.dni;
    }

    // NO SE INCLUYE setDni(). El DNI de un empleado es inmutable por diseño.

    // Getter y Setter para el salario (lectura y modificación permitidas)
    public double getSalario() {
        return this.salario;
    }

    public void setSalario(double nuevoSalario) {
        if (nuevoSalario >= this.salario) { // El setter protege la regla de negocio
            this.salario = nuevoSalario;
        } else {
            throw new IllegalArgumentException("No se puede reducir el salario.");
        }
    }
}


graph LR
    subgraph Estado del Objeto Empleado
        A[Atributo: dni]
        B[Atributo: salario]
    end
    
    A -->|Lectura ✅| C(getDni)
    A -.->|Bloqueado por diseño ❌| D(setDni)
    
    B -->|Lectura ✅| E(getSalario)
    B -->|Escritura controlada ✅| F(setSalario)
    
    style A fill:#e0f7fa,stroke:#006064
    style B fill:#fff3e0,stroke:#e65100
    style D fill:#ffcdd2,stroke:#c62828,stroke-dasharray: 5 5


## 19. ¿La clase `String` en Java es mutable o inmutable? ¿Qué ocurre al concatenar dos cadenas? ¿Qué debemos hacer si vamos a hacer una operación que implique concatenar muchas veces para construir paso a paso una cadena muy larga?

En Java, la clase String es estrictamente inmutable. Esto significa que una vez que se instancia un objeto de este tipo en la memoria, la secuencia de caracteres que contiene no puede ser alterada bajo ninguna circunstancia. Cualquier operación de la API de Java que aparentemente modifique una cadena (como reemplazar un carácter, convertir el texto a mayúsculas o extraer una subcadena) no altera el estado del objeto original; en su lugar, la máquina virtual crea, inicializa y devuelve un objeto String completamente nuevo que contiene el resultado final de dicha operación.

Debido a esta inmutabilidad, al concatenar dos cadenas (ya sea mediante el operador + o invocando el método concat()), el programa no añade los nuevos caracteres al final de la primera cadena. Lo que ocurre internamente es que se reserva un nuevo bloque de memoria con el tamaño exacto para albergar ambos textos combinados, se copia el contenido del primer objeto, luego el contenido del segundo, y finalmente se genera una nueva instancia de String que referencia a ese nuevo bloque. Las dos cadenas originales permanecen intactas en la memoria hasta que el Recolector de Basura (Garbage Collector) determine que ya no están siendo referenciadas y proceda a destruirlas.

Por este motivo, si se requiere realizar una operación que implique concatenar múltiples veces (por ejemplo, construir un documento de texto extenso paso a paso dentro de un bucle iterativo grande), el uso de la clase String resulta desastroso para el rendimiento del programa, ya que inundará la memoria con miles de objetos intermedios desechables. Para solventar este escenario, se debe emplear la clase StringBuilder. Esta clase representa una secuencia de caracteres mutable, permitiendo anexar (append), insertar o eliminar caracteres directamente sobre el mismo espacio de memoria interno pre-reservado (un buffer), evitando la constante creación y destrucción de objetos y mejorando el tiempo de ejecución de forma exponencial.

A continuación, se ilustra la diferencia de uso entre ambas estrategias en Java:

public class GeneradorTexto {

    // ❌ MALA PRÁCTICA: Concatenación iterativa con String (inmutable)
    public String generarTextoIneficiente(int repeticiones) {
        String resultado = "";
        for (int i = 0; i < repeticiones; i++) {
            // En cada iteración, se crea un NUEVO objeto String y se desecha el anterior
            resultado += i + ", "; 
        }
        return resultado;
    }

    // ✅ BUENA PRÁCTICA: Concatenación iterativa con StringBuilder (mutable)
    public String generarTextoEficiente(int repeticiones) {
        // Se crea un único objeto capaz de modificar su estado interno
        StringBuilder buffer = new StringBuilder(); 
        for (int i = 0; i < repeticiones; i++) {
            // Se añaden los datos al mismo bloque de memoria, sin crear objetos extra
            buffer.append(i).append(", "); 
        }
        // Solo al final, se convierte el buffer mutable en un String inmutable definitivo
        return buffer.toString(); 
    }
}

graph TD
    subgraph Bucle con String Inmutable ❌
        A[String S1 = \"0, \"] -->|S1 + \"1, \"| B[NUEVO String S2 = \"0, 1, \"]
        B -->|S2 + \"2, \"| C[NUEVO String S3 = \"0, 1, 2, \"]
        note1[La memoria se llena de<br/>objetos basura (S1, S2...)]
        A -.-> note1
        B -.-> note1
    end
    
    subgraph Bucle con StringBuilder Mutable ✅
        D[StringBuilder SB<br/>Buffer: [0, ]] -->|SB.append(\"1, \")| E[EL MISMO SB<br/>Buffer: [0, 1, ]]
        E -->|SB.append(\"2, \")| F[EL MISMO SB<br/>Buffer: [0, 1, 2, ]]
        note2[Solo existe un objeto<br/>optimizando la memoria]
        D -.-> note2
    end
    
    style A fill:#ffcdd2,stroke:#c62828
    style B fill:#ffcdd2,stroke:#c62828
    style C fill:#ffcdd2,stroke:#c62828
    style D fill:#c8e6c9,stroke:#2e7d32
    style E fill:#c8e6c9,stroke:#2e7d32
    style F fill:#c8e6c9,stroke:#2e7d32


## 20. En POO ¿Cómo se comparan objetos de una misma clase? ¿Por su contenido o por su identidad? ¿Qué es el método equals en Java? ¿Qué hace por defecto? ¿Cómo se deben comparar dos cadenas en Java? 

En la Programación Orientada a Objetos, existen dos formas fundamentales de comparar objetos: por su identidad y por su contenido. La comparación por identidad evalúa si dos variables de referencia apuntan exactamente al mismo espacio en la memoria (es decir, si son el mismo objeto físico), lo cual en Java se realiza habitualmente mediante el operador relacional ==. Por otro lado, la comparación por contenido evalúa si dos objetos distintos en memoria poseen el mismo estado interno o atributos lógicos equivalentes, lo que requiere un método específico diseñado para inspeccionar sus datos.

En Java, este mecanismo se articula a través del método equals, el cual está definido en la superclase base Object de la que heredan absolutamente todas las clases. Por defecto, la implementación original de equals realiza una comparación estricta por identidad, comportándose exactamente igual que el operador ==. Para que este método evalúe realmente el contenido, el programador debe sobrescribirlo (usando la anotación @Override sobre su propia clase) e implementar una lógica personalizada que compare uno a uno los atributos relevantes que definen la equivalencia de dos instancias.

Un caso crítico de esta distinción ocurre al trabajar con texto. Dado que la clase String es un objeto y no un tipo primitivo, utilizar el operador == para comparar dos variables de texto es una práctica incorrecta, ya que solo indicará si ambas referencias apuntan al mismo espacio de memoria (pudiendo devolver false aunque el texto escrito sea idéntico). Por consiguiente, para comparar el contenido de dos cadenas en Java siempre se debe invocar el método equals (por ejemplo, cadena1.equals(cadena2)), puesto que la clase String ya trae este método sobrescrito de fábrica para evaluar los caracteres internamente, garantizando un resultado correcto basado en el texto real.

A continuación, se ilustra la diferencia técnica entre ambas comparaciones:

public class DemostracionComparacion {
    public static void main(String[] args) {
        // Se fuerzan dos instancias distintas en memoria con el mismo texto
        String texto1 = new String("Programacion");
        String texto2 = new String("Programacion");

        // 1. Comparación por IDENTIDAD (¿Tienen la misma dirección de memoria?)
        if (texto1 == texto2) {
            System.out.println("Son el mismo objeto físico.");
        } else {
            System.out.println("Son objetos distintos en memoria (Dará este resultado).");
        }

        // 2. Comparación por CONTENIDO (¿Representan el mismo texto?)
        if (texto1.equals(texto2)) {
            System.out.println("Tienen el mismo contenido lógico (Dará este resultado).");
        }
    }
}

graph TD
    subgraph Memoria Heap
        A[Objeto String A<br/>Valor: \"Programacion\"]
        B[Objeto String B<br/>Valor: \"Programacion\"]
    end
    
    C[Variable texto1] -->|Referencia a| A
    D[Variable texto2] -->|Referencia a| B
    
    C -.->|texto1 == texto2 compara referencias<br/>Resultado: FALSE ❌| D
    A -.->|texto1.equals texto2 compara contenido<br/>Resultado: TRUE ✅| B
    
    style A fill:#fff3e0,stroke:#e65100
    style B fill:#fff3e0,stroke:#e65100
    style C fill:#e3f2fd,stroke:#1565c0
    style D fill:#e3f2fd,stroke:#1565c0


## 21. ¿Qué son las clases "wrapper" en un lenguaje de programación orientado a objetos? ¿Cómo se hace? ¿Es un proceso automático? ¿Qué ventajas tienen? ¿Todos los lenguajes orientados a objetos tienen tipos primitivos y necesitan wrappers? 

Las clases wrapper (o clases envoltorio) en la Programación Orientada a Objetos son clases especiales cuyo propósito principal es encapsular un tipo de dato primitivo (como int, double o char) dentro de un objeto real. En Java, cada tipo primitivo cuenta con su correspondiente clase wrapper en el paquete java.lang, tales como Integer, Double, Boolean o Character. Al instanciar estas clases, el valor primitivo queda almacenado como un estado interno inmutable dentro del objeto, permitiendo que dicho valor pueda ser referenciado y tratado con todos los comportamientos propios de una clase.

La conversión entre un tipo primitivo y su objeto envoltorio se realiza mediante un proceso que, en las versiones modernas de Java, es completamente automático. Este mecanismo se denomina autoboxing cuando el compilador transforma implícitamente un valor primitivo en su wrapper correspondiente, y unboxing cuando extrae automáticamente el valor primitivo almacenado dentro del objeto para realizar, por ejemplo, operaciones aritméticas. Gracias a esto, el código resulta mucho más limpio, ya que no se requiere invocar explícitamente métodos de conversión para pasar de un formato a otro.

La principal ventaja de estas clases radica en la compatibilidad estructural. En Java, las estructuras de datos avanzadas (como el marco de Colecciones que incluye ArrayList o HashMap) están diseñadas para almacenar únicamente referencias a objetos; por tanto, es obligatorio usar clases wrapper si se desea crear una lista de números. Además, proporcionan constantes y métodos estáticos de gran utilidad, como Integer.parseInt(). Cabe destacar que no todos los lenguajes orientados a objetos necesitan wrappers; lenguajes "puros" como Python, Ruby o Smalltalk tratan absolutamente todo como un objeto desde su concepción, careciendo de tipos primitivos tradicionales y, por ende, de esta dualidad.

A continuación, se ilustran estos conceptos en código Java:

import java.util.ArrayList;

public class EjemploWrappers {
    public static void demostracion() {
        // 1. Autoboxing: el compilador convierte automáticamente int (primitivo) a Integer (objeto)
        Integer numeroObjeto = 42; 
        
        // 2. Unboxing: se extrae automáticamente el valor int para hacer la suma
        int numeroPrimitivo = numeroObjeto + 10; 
        
        // 3. Uso en Colecciones: ArrayList exige objetos, no admite primitivos (como int o double)
        // ArrayList<double> listaMala = new ArrayList<>(); // Error de compilación
        ArrayList<Double> listaTemperaturas = new ArrayList<>();
        
        // Autoboxing en acción: convierte el primitivo 25.5 al objeto Double internamente
        listaTemperaturas.add(25.5); 
        
        // 4. Utilidad estática de las clases wrapper
        int valorConvertido = Integer.parseInt("150");
    }
}

graph LR
    subgraph Memoria Stack (Rápida)
        A[Primitivo: int x = 5]
    end
    
    subgraph Memoria Heap (Objetos)
        B[Objeto Wrapper: Integer]
    end
    
    A -->|Autoboxing| B
    B -->|Unboxing| A
    
    C[ArrayList] -.->|Solo puede apuntar a la Heap| B
    C -.->|Acceso denegado ❌| A
    
    style A fill:#ffecb3,stroke:#ff8f00
    style B fill:#c8e6c9,stroke:#2e7d32
    style C fill:#bbdefb,stroke:#1565c0


## 22. ¿En POO qué es un **tipo de dato enumerado**? ¿En Java, un tipo de dato enumerado es una clase? ¿Qué ventajas tienen en términos de encapsulación los enumerados en Java?

En la Programación Orientada a Objetos, un tipo de dato enumerado es una construcción estructurada que define un conjunto cerrado, finito y predefinido de valores constantes. Sirve para representar conceptos que tienen un número limitado de posibilidades lógicas en el dominio del problema, como los días de la semana, las estaciones del año o los estados de un pedido. En Java, un tipo de dato enumerado declarado con la palabra reservada enum es, a todos los efectos técnicos, una clase especial. Durante la compilación, el lenguaje la transforma en una clase final que hereda automáticamente de la superclase java.lang.Enum. Esto significa que los enumerados en Java, a diferencia de lenguajes más antiguos como C o C++, son objetos reales y completos que pueden tener sus propios atributos, constructores y métodos.

Las ventajas de los enumerados en términos de encapsulación son sobresalientes, centradas principalmente en la "seguridad de tipos" (type safety). Al utilizar un enumerado, se restringe drásticamente el dominio de valores que puede recibir una variable o un método. Si un método exige un tipo Mes, el compilador impedirá que se le pase cualquier cosa que no sea una de las doce instancias exactas definidas en el enumerado, eliminando la necesidad de escribir complejas validaciones lógicas ("invariantes") para comprobar si un valor numérico o una cadena de texto es válida. Además, la instanciación está totalmente controlada y oculta: la propia Máquina Virtual de Java garantiza que solo se creará una única instancia inmutable de cada constante (un patrón Singleton implícito), impidiendo que el código cliente utilice la palabra new para generar estados no deseados.

A continuación, se ilustra cómo un enumerado en Java actúa como una clase robusta y encapsulada:

public enum NivelPrioridad {
    // 1. Instancias únicas y predefinidas. El compilador las crea por nosotros.
    BAJA(10), 
    MEDIA(50), 
    ALTA(100);

    // 2. Estado interno encapsulado. Es buena práctica hacerlo inmutable (final).
    private final int valorNumerico;

    // 3. Constructor forzosamente privado (aunque no se escriba 'private', lo es).
    // Evita que desde fuera se hagan cosas como: new NivelPrioridad(99);
    NivelPrioridad(int valor) {
        this.valorNumerico = valor;
    }

    // 4. Interfaz pública para interactuar con la instancia del enumerado.
    public int getValorNumerico() {
        return this.valorNumerico;
    }
}

classDiagram
    class Enum {
        <<Clase Base java.lang.Enum>>
        +name() String
        +ordinal() int
    }
    class NivelPrioridad {
        <<enumeration>>
        BAJA
        MEDIA
        ALTA
        -final int valorNumerico
        -NivelPrioridad(int valor)
        +getValorNumerico() int
    }
    
    Enum <|-- NivelPrioridad : El compilador hereda\nautomáticamente
    
    note for NivelPrioridad "Las instancias BAJA, MEDIA y ALTA\nson objetos pre-construidos\nlistos para usarse."


## 23. Crea un tipo enumerado en Java que se llame `Mes`, con doce posibles instancias y que además proporcione métodos para obtener cuántos días tiene ese mes, el ordinal de ese mes en el año (1-12), empleando atributos privados y constructores del tipo enumerado.

Para definir un tipo de dato enumerado en Java que actúe como una clase completa, se deben declarar en primer lugar las instancias constantes que formarán parte del dominio, separadas por comas. En el caso del tipo Mes, se instancian los doce meses del año, proporcionando a cada uno los argumentos necesarios (número de días y posición en el año) exactamente igual que si se invocara a un constructor tradicional, pero omitiendo la palabra reservada new. El compilador se encarga de crear estos doce objetos únicos en memoria durante la carga de la clase.

Tras la declaración de las instancias, se definen los atributos que almacenarán el estado interno particular de cada mes. Para garantizar una correcta ocultación de información y asegurar la inmutabilidad de estas constantes (es decir, que enero siempre tenga 31 días y sea el primer mes), los atributos deben declararse como privados (private) y finales (final). A continuación, se implementa el constructor, el cual tiene visibilidad forzosamente privada en los enumerados, encargado de recibir los parámetros e inicializar el estado. Finalmente, se programan los métodos consultores ("getters") públicos para formar la interfaz, permitiendo al resto del programa acceder a la información de los meses de forma segura.

A continuación, se detalla el código del enumerado:

public enum Mes {
    // 1. Declaración de las 12 únicas instancias posibles del enumerado.
    // Se invoca implícitamente al constructor privado con los valores específicos.
    ENERO(31, 1),
    FEBRERO(28, 2), // Simplificación para años no bisiestos
    MARZO(31, 3),
    ABRIL(30, 4),
    MAYO(31, 5),
    JUNIO(30, 6),
    JULIO(31, 7),
    AGOSTO(31, 8),
    SEPTIEMBRE(30, 9),
    OCTUBRE(31, 10),
    NOVIEMBRE(30, 11),
    DICIEMBRE(31, 12);

    // 2. Estado interno fuertemente encapsulado (private y final)
    private final int numeroDias;
    private final int orden;

    // 3. Constructor (siempre es privado en un enum, se puede omitir la palabra 'private')
    Mes(int numeroDias, int orden) {
        this.numeroDias = numeroDias;
        this.orden = orden;
    }

    // 4. Interfaz pública para consultar el estado inmutable
    public int getNumeroDias() {
        return this.numeroDias;
    }

    public int getOrden() {
        return this.orden;
    }
}

classDiagram
    class Mes {
        <<enumeration>>
        ENERO
        FEBRERO
        ...
        DICIEMBRE
        -final int numeroDias
        -final int orden
        -Mes(int numeroDias, int orden)
        +getNumeroDias() int
        +getOrden() int
    }
    
    note for Mes "Las doce instancias constantes\nse inicializan internamente y\nno pueden ser modificadas\ndesde el exterior."


## 24. Añade a la clase `Mes` del ejercicio anterior cuatro métodos para devolver si ese mes tiene algunos días de invierno, primavera, verano u otoño, indicando con un booleano el hemisferio (norte o sur, parámetro `enHemisferioNorte`). Es decir: `esDePrimavera(boolean esHemisferioNorte)`, `esDeVerano(boolean esHemisferioNorte)`, `esDeOtoño(boolean esHemisferioNorte)`, `esDeInvierno(boolean esHemisferioNorte)`

Para extender la funcionalidad del enumerado Mes, se deben incorporar cuatro métodos públicos que actúen como consultas booleanas sobre el estado de la instancia. Dado que las estaciones astronómicas dependen de la ubicación geográfica (estando invertidas entre hemisferios), estos métodos requieren un parámetro booleano (enHemisferioNorte) para contextualizar la evaluación. Esta aproximación demuestra cómo un tipo enumerado en Java puede encapsular lógicas de negocio condicionales complejas, manteniendo ocultos los detalles de implementación y ofreciendo una interfaz semántica clara al resto del programa.

En la implementación de estos métodos, resulta fundamental recordar que los enumerados en Java garantizan que solo exista una única instancia en memoria para cada valor constante. Por este motivo, es totalmente seguro y eficiente utilizar el operador de identidad (==) para comparar la instancia actual (this) con las constantes del propio enumerado (como Mes.MARZO). Teniendo en cuenta que los equinoccios y solsticios ocurren habitualmente alrededor del día 21 del mes, los meses de transición (marzo, junio, septiembre y diciembre) pertenecerán simultáneamente a dos estaciones distintas, por lo que los métodos deben devolver true en ambos casos para dichos meses.

A continuación, se presenta la ampliación del código de la clase enumerada Mes:

public enum Mes {
    // ... (Instancias y constructores definidos en el ejercicio 23) ...

    public boolean esDePrimavera(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == MARZO || this == ABRIL || this == MAYO || this == JUNIO;
        } else {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE || this == DICIEMBRE;
        }
    }

    public boolean esDeVerano(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == JUNIO || this == JULIO || this == AGOSTO || this == SEPTIEMBRE;
        } else {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO || this == MARZO;
        }
    }

    public boolean esDeOtono(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == SEPTIEMBRE || this == OCTUBRE || this == NOVIEMBRE || this == DICIEMBRE;
        } else {
            return this == MARZO || this == ABRIL || this == MAYO || this == JUNIO;
        }
    }

    public boolean esDeInvierno(boolean enHemisferioNorte) {
        if (enHemisferioNorte) {
            return this == DICIEMBRE || this == ENERO || this == FEBRERO || this == MARZO;
        } else {
            return this == JUNIO || this == JULIO || this == AGOSTO || this == SEPTIEMBRE;
        }
    }
}

graph TD
    A[Código Cliente] -->|Mes.AGOSTO.esDeVerano(true)| B(Instancia AGOSTO)
    B --> C{¿enHemisferioNorte?}
    C -->|Sí| D[Evalúa: this == JUNIO || ... || this == SEPTIEMBRE]
    C -->|No| E[Evalúa: this == DICIEMBRE || ... || this == MARZO]
    D -->|Coincide con AGOSTO| F((Retorna TRUE))
    E -->|No coincide| G((Retorna FALSE))
    
    style B fill:#c8e6c9,stroke:#2e7d32
    style F fill:#c8e6c9,stroke:#2e7d32
    style G fill:#ffcdd2,stroke:#c62828
