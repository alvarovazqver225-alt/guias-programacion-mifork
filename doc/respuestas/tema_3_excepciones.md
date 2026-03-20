<!--
Posible prompt:
<prompt>
Tengo un cuestionario con preguntas sobre "Excepciones". Debes tener en cuenta que los conocimientos previos que tengo (y por tanto tus respuestas deben ser adaptadas), son:
- C/C++ sin orientación a objetos.
- Temas de Java previos: Clases y Objetos, Encapsulación.

Cada respuesta debe tener entre 2 - 4 párrafos de longitud (sin contar los trozos de código).

Por favor, escribe en impersonal las respuestas.

</prompt>
----
-->
# TEMA 3. Excepciones

## 1. Empecemos un tema sobre control de errores en lenguajes de programación, con algo básico. En C, donde no existen las excepciones, pongamos un ejemplo de una raíz que toma número flotante positivo. Queremos controlar el error si la función recibe un número negativo. El usuario debe ser informado pero desde fuera de la función `raiz` ¿Cómo indicamos ese error?. Enumera dos opciones diferentes de diseñar, poniendo un ejemplo de código de cada una.

En los lenguajes procedimentales clásicos como C, al carecer de un mecanismo estructurado y nativo para la propagación de excepciones, la notificación de errores desde una función hacia su invocador requiere convenciones de diseño específicas. Cuando una función detecta una precondición inválida, como recibir un número negativo para el cálculo de una raíz cuadrada, no debe imprimir el error directamente ni abortar la ejecución si se busca que el programa principal sea quien decida cómo gestionar el problema. Por ello, el error debe comunicarse de vuelta al código que realizó la llamada mediante el flujo normal de retorno.

Una de las aproximaciones más comunes consiste en utilizar el propio valor de retorno de la función para indicar el error, devolviendo un "valor centinela" que resulte imposible como resultado válido. En el caso de una raíz cuadrada, dado que el dominio de resultados esperados son los números reales positivos o el cero, se puede devolver un valor negativo (como -1.0) para señalizar que la operación ha fallado. El código llamador tiene la obligación implícita de comprobar el resultado antes de utilizarlo.

#include <stdio.h>
#include <math.h>

// Opción 1: Uso de valor centinela
double raiz_v1(double numero) {
    if (numero < 0) {
        return -1.0; // Código de error: el resultado de una raíz no puede ser negativo
    }
    return sqrt(numero);
}

int main() {
    double valor = -4.0;
    double resultado = raiz_v1(valor);
    
    if (resultado < 0.0) {
        printf("Error: No se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("La raiz es: %f\n", resultado);
    }
    return 0;
}

Otra alternativa de diseño, que resulta indispensable cuando todo el rango posible de valores de retorno es válido (impidiendo usar un centinela) o cuando se requiere mayor detalle del error, es separar el resultado del cálculo del código de estado. Para implementar este diseño en C, la función devuelve un código entero que representa el estado de la ejecución (por ejemplo, 0 para éxito y otro valor para error) y el resultado del cálculo se devuelve indirectamente mediante un parámetro pasado por referencia utilizando punteros.

#include <stdio.h>
#include <math.h>

// Opción 2: Código de estado y paso por referencia
int raiz_v2(double numero, double *resultado) {
    if (numero < 0) {
        return -1; // Código de estado: Error
    }
    *resultado = sqrt(numero);
    return 0;      // Código de estado: Éxito
}

int main() {
    double valor = -4.0;
    double resultado;
    
    // Se evalúa el código de estado devuelto por la función
    if (raiz_v2(valor, &resultado) != 0) {
        printf("Error: No se puede calcular la raiz de un numero negativo.\n");
    } else {
        printf("La raiz es: %f\n", resultado);
    }
    return 0;
}


## 2. Brevemente ¿Qué es una **"excepción"**? ¿Con qué objetivo las usa un programador cuando implementa funciones o cuando las llama?

En el contexto de la programación orientada a objetos, se define una excepción como un evento anómalo o inesperado que ocurre durante la ejecución de un programa y que interrumpe el flujo normal de las instrucciones. A diferencia de C, donde los errores se representan mediante valores primitivos o códigos de estado como se vio en el caso anterior, en lenguajes como Java una excepción es un objeto real. Este objeto se instancia a partir de una clase específica y encapsula toda la información relevante sobre el error ocurrido, como el tipo de fallo, un mensaje descriptivo detallado y la secuencia de métodos que llevaron hasta ese punto.

Cuando se implementan funciones (denominadas métodos en Java), el objetivo principal de utilizar excepciones es separar de forma limpia la lógica de negocio del código destinado a la gestión de errores. En lugar de plagar la función con sentencias condicionales para devolver códigos de error, se delega la responsabilidad de notificar el fallo mediante el "lanzamiento" de una excepción. Esto permite mantener la firma del método centrada en su propósito principal (por ejemplo, devolver un double al calcular una raíz) y transferir la responsabilidad del fallo de manera estructurada.

Por otro lado, cuando se invocan funciones que son susceptibles de fallar, el programador utiliza este mecanismo con el objetivo de capturar y tratar dichos errores en el nivel del programa más adecuado. Se proporciona un canal de comunicación de errores que viaja de forma paralela al flujo de retorno estándar. De este modo, si el método llamador no tiene la información o el contexto necesario para solucionar el problema, la excepción se propaga de forma natural hacia los niveles superiores de la pila de llamadas hasta encontrar un bloque diseñado para controlarla, evitando que el programa finalice de forma silenciosa o abrupta sin justificación.


## 3. Reescribe el mismo ejemplo de raiz, pero en Java, metiendo ese método en una clase `Calculadora` y llama a dicho método desde el método `main`, mostrando cómo se puede controlar desde fuera.

En Java, siguiendo el paradigma de la orientación a objetos, las funciones no existen de forma aislada en el ámbito global como ocurre en C, sino que deben estar encapsuladas como métodos dentro de una clase. Para abordar este problema, se diseña una clase Calculadora que contendrá la lógica matemática. Posteriormente, desde el método main de la clase principal, se instancia un objeto de esta calculadora para poder solicitarle operaciones, aplicando así los principios de encapsulación que ya se conocen.

Para gestionar el caso de uso del número negativo, en lugar de devolver un valor centinela o utilizar punteros para modificar variables de estado por referencia (técnicas habituales en C), se utiliza el mecanismo de excepciones. Mediante la palabra reservada throw, se instancia y se lanza un nuevo objeto de error, concretamente un IllegalArgumentException, que es la clase estándar en Java para indicar que un método ha recibido un argumento inapropiado. En el momento en que se ejecuta el throw, el flujo normal del método se aborta de inmediato y el control se devuelve abruptamente a quien realizó la llamada.

Para controlar este error desde el exterior, el código llamador (en este caso el método main) emplea la estructura try-catch. El bloque try envuelve las instrucciones que son susceptibles de fallar, definiendo el "flujo de éxito". Si ocurre el error y se lanza la excepción, el entorno de ejecución salta directamente al bloque catch correspondiente, donde se "captura" el objeto excepción lanzado. A través de este objeto capturado, referenciado comúnmente con la variable e, se puede acceder a información vital encapsulada, como el mensaje descriptivo del fallo, permitiendo que el programa gestione el contratiempo y continúe su ejecución sin detenerse abruptamente.

// Clase que encapsula la lógica de las operaciones matemáticas
class Calculadora {
    
    // Método que calcula la raíz y notifica errores mediante excepciones
    public double raiz(double numero) {
        if (numero < 0) {
            // En lugar de devolver -1, se instancia y lanza un objeto de tipo excepción
            throw new IllegalArgumentException("El operando para la raiz no puede ser negativo: " + numero);
        }
        return Math.sqrt(numero); // Si todo va bien, se retorna el valor normalmente
    }
}

// Clase principal que contiene el punto de entrada del programa
public class Main {
    public static void main(String[] args) {
        Calculadora miCalc = new Calculadora();
        double valor = -4.0;
        
        // Se separa claramente el flujo normal (try) del manejo de errores (catch)
        try {
            double resultado = miCalc.raiz(valor);
            System.out.println("La raiz es: " + resultado);
            
        } catch (IllegalArgumentException e) {
            // Se captura el objeto excepción (e) y se extrae su información encapsulada
            System.err.println("Se ha producido un error controlado.");
            System.err.println("Motivo: " + e.getMessage());
        }
        
        System.out.println("El programa principal continua su ejecucion de forma segura.");
    }
}


## 4. ¿Qué es **"lanzar"** una excepción? ¿Qué es **"controlar"** o **"capturar"** una excepción? ¿Qué es que se **"propague"** una excepción? ¿Qué le va ocurriendo a las funciones en la pila de llamadas por donde se va propagando la excepción? ¿Las funciones que no la controlan se reanudan después de alguna forma? Explica con el mismo ejemplo anterior en Java de la raíz cuadrada.

"Lanzar" (en inglés, throw) una excepción consiste en crear un objeto que encapsula la información de un error y entregarlo al entorno de ejecución (la Máquina Virtual de Java), lo que provoca la interrupción inmediata de la función actual. Por el contrario, "controlar" o "capturar" (catch) significa interceptar ese objeto de error mediante una estructura try-catch dispuesta en el código llamador, asumiendo la responsabilidad de gestionar el fallo para que el programa pueda recuperarse y continuar operando sin colapsar.

Cuando una excepción se lanza pero el método actual no dispone de un bloque try-catch para capturarla, se produce lo que se denomina "propagación". A diferencia de C, donde los errores deben escalarse manualmente evaluando y retornando códigos de error en cada nivel de la jerarquía de funciones, en Java este proceso es automático. El entorno de ejecución retrocede por la pila de llamadas (call stack), buscando en los métodos anteriores algún manejador compatible con el tipo de excepción lanzada.

Durante esta propagación, las funciones intermedias por las que pasa la excepción se abortan de forma abrupta; sus variables locales se destruyen y cualquier código restante en ellas queda sin ejecutar. Es fundamental comprender que estas funciones intermedias no se reanudan en ningún momento. Una vez que la excepción abandona un ámbito, el flujo de ejecución nunca regresa a él; el programa continuará su curso única y exclusivamente a partir de la siguiente instrucción después del bloque catch que finalmente logre capturar el error.

class Calculadora {
    public double raiz(double numero) {
        if (numero < 0) {
            // 1. LANZAR: Se crea y lanza el objeto, interrumpiendo 'raiz'
            throw new IllegalArgumentException("Raiz de numero negativo");
        }
        return Math.sqrt(numero);
    }
    
    // Método intermedio para demostrar la propagación
    public void realizarOperacion(double valor) {
        System.out.println("Iniciando operacion...");
        // 2. PROPAGAR: Como no hay try-catch aquí, si 'raiz' falla, 
        // este método se aborta y la excepción sube a 'main'.
        double res = raiz(valor); 
        
        // Si hay excepción, esta línea JAMÁS se ejecutará (no hay reanudación)
        System.out.println("Operacion finalizada con exito."); 
    }
}

public class Main {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        
        // 3. CAPTURAR: El manejador se encuentra en la base de la pila
        try {
            calc.realizarOperacion(-9.0);
        } catch (IllegalArgumentException e) {
            System.err.println("Capturada en main: " + e.getMessage());
        }
        System.out.println("Fin del programa.");
    }
}




## 5. ¿Qué ventajas tiene frente a C, la **"propagación natural"** de las excepciones a través de la pila (*stack*) de llamadas?

La principal ventaja de la propagación automática de excepciones en Java respecto a C radica en la drástica mejora de la limpieza y legibilidad del código fuente. En C, para propagar un error a través de múltiples capas de funciones, cada nivel intermedio está obligado a comprobar explícitamente el valor de retorno de la función invocada y, en caso de fallo, retornar prematuramente para escalar el error al nivel superior. Esto entrelaza intrincadamente la lógica principal con el código de gestión de fallos. Por el contrario, en Java, los métodos intermedios pueden desentenderse por completo de las excepciones que no saben o no deben manejar. La propagación la asume la Máquina Virtual de Java, lo que permite escribir los algoritmos centrándose exclusivamente en el flujo normal o "camino feliz".

Otra ventaja fundamental es la seguridad frente a fallos silenciosos. En C, si un programador olvida comprobar el código de retorno de una función (algo extremadamente común al invocar utilidades), el programa simplemente continúa su ejecución con datos no válidos o en un estado inconsistente, provocando errores impredecibles más adelante. En Java, el mecanismo de excepciones impide ignorar los errores de forma inadvertida: si una excepción se propaga y nadie la captura en la pila de llamadas, el programa abortará su ejecución y mostrará la traza del error, obligando al desarrollador a ser consciente del problema.

Adicionalmente, esta propagación natural fomenta la centralización del manejo de errores. En lugar de estar forzado a tratar un posible fallo en cada uno de los métodos intermedios por los que transita, se puede situar un único bloque try-catch en un nivel superior y estratégico de la aplicación (por ejemplo, en el menú principal o en el controlador de la interfaz). Este único bloque será capaz de interceptar y gestionar los errores provenientes de cualquier profundidad en la pila de llamadas, evitando la duplicación de código y simplificando el mantenimiento.

Para ilustrar la diferencia, a continuación se contrasta mentalmente la carga de trabajo en C frente a la limpieza en Java cuando hay varios métodos anidados:

// --- Modelo mental en C (usando códigos de retorno) ---
// int capaBaja() { ... return error_code; }
// int capaMedia() { 
//     int status = capaBaja(); 
//     if (status != 0) return status; // Código repetitivo obligado
//     return 0; 
// }
// int capaAlta() {
//     int status = capaMedia();
//     if (status != 0) return status; // Código repetitivo obligado
//     return 0;
// }

// --- Modelo en Java (Propagación natural) ---
class Capas {
    // La capa baja lanza la excepción si algo falla
    public void capaBaja() {
        throw new RuntimeException("Fallo en disco");
    }

    // Las capas intermedias quedan totalmente limpias de sentencias 'if'
    // Si capaBaja falla, la excepción atraviesa este método automáticamente.
    public void capaMedia() {
        capaBaja();
    }

    public void capaAlta() {
        capaMedia();
    }
}

public class Main {
    public static void main(String[] args) {
        Capas app = new Capas();
        try {
            // Un solo bloque catch gestiona los fallos de cualquier profundidad
            app.capaAlta(); 
        } catch (RuntimeException e) {
            System.err.println("Error capturado en el nivel superior: " + e.getMessage());
        }
    }
}


## 6. En orientación a objetos, ¿las excepciones suelen ser objetos? ¿Qué ventajas tiene esto en términos de encapsulación? ¿Podemos entonces crear excepciones personalizadas?

En la programación orientada a objetos, las excepciones no son simples códigos numéricos enteros o indicadores globales como ocurre habitualmente en C, sino que son instancias reales de clases, es decir, objetos completos. Cuando ocurre un evento anómalo durante la ejecución, el entorno instancia un objeto en memoria que representa específicamente ese error concreto. Toda la jerarquía de errores en Java deriva de una clase base fundamental, de la cual heredan todas las demás excepciones, lo que permite que el mecanismo de control las trate de forma estructurada.

La principal ventaja de que las excepciones sean objetos radica precisamente en la encapsulación. Al igual que cualquier otra clase en Java, un objeto excepción puede poseer múltiples atributos internos para almacenar el estado y el contexto exacto en el que se produjo el fallo. Esto incluye cadenas de texto con mensajes explicativos, la traza completa de la pila de llamadas e, incluso, copias de los parámetros que provocaron el problema. El código que captura la excepción (el bloque catch) no necesita acceder directamente a las variables internas de la función que falló; en su lugar, utiliza los métodos públicos del objeto excepción (como getters) para extraer la información necesaria y gestionar el error de forma segura.

Como consecuencia directa de este diseño y gracias al mecanismo de herencia, es totalmente posible y muy recomendable crear excepciones personalizadas. Si las excepciones estándar provistas por el lenguaje no describen con suficiente precisión un error específico del dominio de la aplicación (por ejemplo, un saldo insuficiente en un software bancario o una lectura fuera de rango en un sensor), se permite definir una nueva clase que herede de la clase base de excepciones. Esto faculta al desarrollador para añadir atributos propios y métodos específicos que enriquezcan la información del error, logrando un código más semántico y fácil de depurar.

// 1. Creación de una excepción personalizada aplicando herencia
class ValorNegativoException extends RuntimeException {
    // Atributo propio encapsulado para almacenar el valor conflictivo
    private double valorErroneo;

    // El constructor recibe el mensaje estándar y el dato específico
    public ValorNegativoException(String mensaje, double valor) {
        super(mensaje); // Se inicializa la parte de la clase padre (RuntimeException)
        this.valorErroneo = valor;
    }

    // Método público (getter) para acceder a la información encapsulada
    public double getValorErroneo() {
        return this.valorErroneo;
    }
}

class Calculadora {
    public double raiz(double numero) {
        if (numero < 0) {
            // 2. Se instancia y lanza el objeto de la excepción personalizada
            throw new ValorNegativoException("No se admiten radicandos negativos", numero);
        }
        return Math.sqrt(numero);
    }
}

public class Main {
    public static void main(String[] args) {
        Calculadora calc = new Calculadora();
        try {
            calc.raiz(-25.0);
        } catch (ValorNegativoException e) {
            // 3. Se utiliza el método propio de la excepción personalizada
            System.err.println("Error controlado: " + e.getMessage());
            System.err.println("El programa detecto el valor critico: " + e.getValorErroneo());
        }
    }
}


## 7. En relación con las ventajas de la encapsulación, comparando el ejemplo en C con Java. ¿Qué **información esencial** lleva cualquier **objeto excepción** que es muy útil tener cuando se llega a un manejador?

En lenguajes como C, la notificación de un error suele limitarse a un valor numérico (como -1 o un código de estado) desprovisto de contexto. Si este código se propaga a través de varias funciones, el manejador final que lo recibe solo sabe que "algo falló", pero carece de datos sobre el estado del sistema o la ubicación exacta del problema, obligando a realizar una depuración manual tediosa. Por el contrario, en Java, gracias a la encapsulación propia de la orientación a objetos, un objeto excepción se comporta como una "caja negra" que captura una instantánea completa del entorno en el instante exacto en que se generó la anomalía, transportando toda esta información hasta el bloque catch.

La pieza de información esencial más valiosa que encapsula cualquier excepción en Java es la traza de la pila (stack trace).  Esta traza es un registro cronológico y detallado que muestra la secuencia exacta de llamadas a métodos que desembocó en el fallo. Incluye el nombre de las clases implicadas, los métodos concretos e, incluso, los números de línea exactos en los archivos de código fuente donde se detuvo la ejecución. Toda esta información se almacena internamente en el objeto cuando se ejecuta la instrucción throw.

Además de la traza de la pila, el objeto excepción siempre suele llevar consigo un mensaje de detalle en formato de cadena de texto, accesible típicamente mediante el método getMessage(). Este mensaje describe semánticamente la naturaleza del error (por ejemplo, "El archivo config.txt no existe" en lugar de un simple código 404). Asimismo, el objeto puede encapsular opcionalmente la "causa" del error, es decir, otra excepción previa que haya desencadenado el fallo actual, permitiendo mantener un historial completo de lo ocurrido.

public class AnalisisError {
    
    public void metodoC() {
        // Se lanza una excepción estándar con un mensaje descriptivo
        throw new RuntimeException("Fallo critico al procesar los datos de entrada");
    }
    
    public void metodoB() {
        metodoC(); // La excepción pasa por aquí de forma transparente
    }
    
    public void metodoA() {
        metodoB(); // Sigue propagándose
    }

    public static void main(String[] args) {
        AnalisisError analizador = new AnalisisError();
        
        try {
            analizador.metodoA();
        } catch (RuntimeException e) {
            // El manejador recibe el objeto 'e' con TODA la información encapsulada
            
            // 1. Se extrae el mensaje de error básico
            System.err.println("MENSAJE DEL ERROR: " + e.getMessage());
            System.err.println("--------------------------------------------------");
            
            // 2. Se imprime la traza de la pila completa. 
            // Mostrará exactamente que el error nació en metodoC, pasó por metodoB, luego metodoA y llegó al main.
            System.err.println("TRAZA DE LA PILA (Stack Trace):");
            e.printStackTrace(); 
        }
    }
}


## 8. En Java, sobre el bloque **"try-catch"**, ¿se pueden tener más de un bloque `catch`? ¿cuántos bloques `catch` se ejecutan?

En Java, se permite asociar múltiples bloques catch a un único bloque try. Esta característica resulta fundamental cuando las instrucciones encapsuladas dentro del try son susceptibles de generar diferentes tipos de errores y cada uno de ellos requiere una estrategia de recuperación distinta. Haciendo un paralelismo con C, esta estructura se comporta de manera conceptualmente similar a una sentencia switch o a una cadena de if-else if, donde la selección de la rama a ejecutar no depende de evaluar una variable numérica entera, sino de evaluar el tipo exacto de la clase a la que pertenece el objeto excepción que ha sido lanzado.

Respecto a la cantidad de bloques que se ejecutan, la respuesta es estrictamente uno. Cuando se lanza una excepción, el entorno de ejecución evalúa los bloques catch de forma secuencial, de arriba hacia abajo. En el momento en que encuentra el primer bloque cuyo parámetro coincide con el tipo de la excepción lanzada, se ingresa en dicho bloque. Una vez finalizada la ejecución de ese único manejador catch, se ignoran automáticamente todos los demás y el flujo del programa salta a la primera instrucción que haya después de toda la estructura try-catch.

Es indispensable estructurar estos bloques teniendo en cuenta la jerarquía de clases. Se deben colocar siempre las excepciones más específicas (las clases derivadas) en la parte superior y las excepciones más genéricas (las clases base) en la parte inferior. Si se ubicara un catch con una clase genérica al principio, este interceptaría absolutamente todos los errores, bloqueando el paso a los manejadores más específicos situados debajo. Afortunadamente, en Java el compilador es estricto con esto y, si detecta un bloque inalcanzable, abortará el proceso de compilación indicando un error estructural.

public class MultiplesExcepciones {
    public static void main(String[] args) {
        String texto = null;
        int divisor = 0;

        try {
            // Dependiendo de qué instrucción falle primero, saltará una excepción distinta.
            // En este caso, fallará la división antes de llegar a usar el texto.
            int resultado = 10 / divisor; // Lanza ArithmeticException
            System.out.println("Longitud: " + texto.length()); // Lanzaría NullPointerException

        } catch (ArithmeticException e) {
            // Solo se ejecuta si ocurre un error matemático
            System.err.println("Error especifico capturado: No se puede dividir por cero.");
            
        } catch (NullPointerException e) {
            // Solo se ejecuta si se intenta usar una referencia a un objeto nulo
            System.err.println("Error especifico capturado: La variable no apunta a ningun objeto.");
            
        } catch (Exception e) {
            // Bloque genérico colocado SIEMPRE al final.
            // Actúa como una cláusula "default" para capturar cualquier otro error no previsto.
            System.err.println("Error general capturado: " + e.getMessage());
        }

        System.out.println("El flujo principal se reanuda aqui tras ejecutar como maximo un catch.");
    }
}



## 9. Si las excepciones producen rupturas en el código llamador, ¿cómo podemos garantizar que se ejecuta siempre finalmente un código necesario para cierre de ficheros, liberacion de recursos, antes de que continúe propagándose la excepción? Pon un ejemplo en Java con `finally`, tanto con `catch` como sin él.

En lenguajes como C, la gestión de recursos críticos (como la apertura de ficheros o la reserva de memoria dinámica) exige un cuidado extremo frente a los errores. Si una función debe abortar su ejecución y retornar un código de error prematuramente, es obligatorio duplicar manualmente las instrucciones de limpieza (como un fclose o un free) antes de cada punto de retorno para evitar fugas de memoria o bloqueos del sistema. En Java, el lanzamiento de una excepción provoca un salto abrupto que interrumpe inmediatamente el flujo secuencial normal. Si no se toma ninguna precaución, cualquier código de limpieza situado al final de un método jamás se ejecutará si ocurre un error, dejando los recursos "colgando" mientras la excepción asciende por la pila de llamadas.

Para solucionar este problema estructural y garantizar que un fragmento de código se ejecute de forma incondicional, Java proporciona el bloque finally. Este bloque se añade al final de una estructura try y su propósito exclusivo es albergar las instrucciones de cierre o liberación de recursos. El comportamiento garantizado por la Máquina Virtual de Java es que, sin importar si el bloque try finaliza con éxito, si se interrumpe por una excepción o incluso si contiene una sentencia de retorno en su interior, el código dentro de finally siempre se ejecutará justo antes de ceder el control al exterior.

Es importante destacar la versatilidad de esta estructura. El bloque finally se puede combinar con uno o varios bloques catch cuando el método tiene la capacidad y la responsabilidad de capturar el error y, además, necesita limpiar sus recursos locales. Alternativamente, se permite utilizar la estructura try-finally (sin la presencia de ningún catch) en aquellos escenarios donde el método debe permitir que la excepción se propague de forma natural hacia niveles superiores, pero tiene la obligación ineludible de cerrar los recursos que él mismo abrió antes de que el error abandone definitivamente su ámbito.

import java.io.FileInputStream;
import java.io.IOException;

public class GestorFicheros {

    // Ejemplo 1: try acompañado de catch y de finally
    public void leerYCapturar() {
        FileInputStream fis = null;
        try {
            System.out.println("1. Intentando abrir el fichero...");
            fis = new FileInputStream("archivo.txt");
            
            // Lógica de lectura que podría fallar...
            
        } catch (IOException e) {
            System.err.println("2. Error capturado: No se pudo procesar el fichero.");
        } finally {
            // Este bloque garantiza que el fichero se cierra, haya ocurrido un error o no
            System.out.println("3. FINALLY (con catch): Ejecucion garantizada de limpieza.");
            if (fis != null) {
                try {
                    fis.close();
                } catch (IOException ex) {
                    System.err.println("Error ignorado durante el cierre.");
                }
            }
        }
    }

    // Ejemplo 2: try acompañado EXCLUSIVAMENTE de finally (sin catch)
    public void leerYPropagar() throws IOException {
        FileInputStream fis = null;
        try {
            System.out.println("A. Abriendo fichero para lectura...");
            fis = new FileInputStream("otro_archivo.txt");
            
            // Si ocurre un error aquí, NO se captura. Se propagará hacia el método llamador.
            
        } finally {
            // Antes de que la excepción abandone este método, se asegura el cierre.
            System.out.println("B. FINALLY (sin catch): Se asegura el cierre antes de que la excepcion escape.");
            if (fis != null) {
                fis.close(); 
            }
        }
    }
}


## 10. En Java, el bloque `finally` puede ir sin `catch`? ¿Se ejecuta siempre tanto si ocurre como si no ocurre una excepción? ¿Y si hay un `return` en medio del `try`?

En Java, es completamente válido estructurar un bloque finally sin acompañarlo de un catch, conformando lo que se conoce como una estructura try-finally. Se recurre a este diseño cuando una función adquiere recursos (como memoria, conexiones a bases de datos o descriptores de ficheros) y ocurre un error que no le corresponde gestionar, pero tiene la responsabilidad ineludible de liberar dichos recursos antes de que la excepción se propague hacia las capas superiores. Haciendo un paralelismo con C, equivaldría a asegurar la invocación de free() para liberar memoria dinámica justo antes de abandonar una función con un código de error, evitando así fugas de memoria, pero delegando el tratamiento del error al código llamador.

La característica fundamental del bloque finally es que su ejecución está prácticamente garantizada por el entorno de ejecución. Se ejecutará indefectiblemente tanto si las instrucciones del bloque try finalizan de manera exitosa y normal, como si se produce una excepción, independientemente de si esta es capturada por un catch intermedio o si se deja propagar libremente. Actúa como una red de seguridad incondicional.

Esta garantía de ejecución se mantiene firme incluso ante la presencia de sentencias de ruptura del flujo, como un return, break o continue ubicados dentro del try o del catch. A diferencia de C, donde la instrucción return provoca la salida inmediata e irrevocable de la función ignorando cualquier código posterior, en Java la Máquina Virtual intercepta ese retorno. El valor a devolver se evalúa y se guarda temporalmente en memoria; a continuación, el control salta para ejecutar el bloque finally en su totalidad y, solo cuando este concluye, se efectúa el retorno real hacia el método invocador.

public class DemostracionFinally {
    
    // Método que demuestra el uso de try-finally (sin catch) con un return en medio
    public static int operarYRetornar(boolean forzarError) {
        try {
            System.out.println("1. Iniciando bloque try...");
            if (forzarError) {
                // Si hay error, se lanza. El finally se ejecuta antes de que la excepción salga.
                throw new RuntimeException("Error inesperado en la operacion");
            }
            
            System.out.println("2. Alcanzado el return dentro del try.");
            // En C, la función terminaría aquí mismo. En Java, el finally toma el control primero.
            return 42; 
            
        } finally {
            // Este bloque se ejecuta incondicionalmente siempre
            System.out.println("3. Ejecutando finally: Liberando recursos criticos.");
        }
    }

    public static void main(String[] args) {
        System.out.println("--- CASO 1: Ejecucion normal ---");
        int resultado = operarYRetornar(false);
        System.out.println("Valor retornado al main: " + resultado);
        
        System.out.println("\n--- CASO 2: Ejecucion con excepcion ---");
        try {
            operarYRetornar(true);
        } catch (RuntimeException e) {
            System.err.println("Excepcion capturada en main tras el finally: " + e.getMessage());
        }
    }
}


## 11. En Java, qué son las excepciones **"controladas"** y las **"no controladas"**? ¿Qué papel juega `RuntimeException`? Pon un ejemplo de excepciones típicas controladas y no controladas que incluso nosotros mismos podríamos usar. Haz dos listas con 3 o 4 ejemplos de situación donde se suele preferir una excepción controlada y donde se suele preferir una excepción no controlada.

### Respuesta


## 12. ¿Qué es y para qué se usa `throws`? ¿Por qué es alternativa a capturar una excepción controlada?

### Respuesta


## 13. Pon un ejemplo en Java de firma de método que incluya `throws`, de una función que abre un fichero pero que declara que no le interesa menejar la excepción de si el fichero no existe, sino que se propague hacia arriba. Eso sí, acuérdate del `finally`.

### Respuesta


## 14. ¿Podemos poner en `throws` excepciones no controladas, como `RuntimeException`? ¿Debería el método llamador entonces poner `try-catch` en ese caso? ¿Qué sentido tendría?

### Respuesta


## 15. ¿Cuándo se recomienda usar excepciones controladas, como `IOException`, y cuándo no controladas como `IllegalArgumentException`? ¿Existen en todos los lenguajes ambas opciones? En los que sólo existe una opción, ¿cuál es la más habitual?

### Respuesta


## 16. ¿Tiene sentido lanzar excepciones dentro del `catch`? ¿Se puede relanzar la misma excepción capturada? ¿Cuándo tendría sentido hacer esto último? Pon ejemplos de ambos casos.

### Respuesta


## 17. ¿En qué consiste que una excepción sea la **"causa"** de otra excepción? Pon un ejemplo en Java, donde capturemos una excepción de bajo nivel y la encapsulemos en otra personalizada de alto nivel. Cuando una excepción sale por pantalla y tiene una causa, ¿se ve?

### Respuesta

