# El módulo `std.parallelism`

El módulo `std.parallelism` implementa primitivas de alto nivel para
implementar programación concurrente.

### La función `parallel`

La función [`std.parallelism.parallel`](http://dlang.org/phobos/std_parallelism.html#.parallel)
permite distribuir de forma automática el cuerpo de una sentencia `foreach`
entre diferentes hilos de ejecución:

    // Se ejecuta la operación de elevar al cuadrado
    // cada elemento del array de forma paralela.
    auto arr = iota(1,100).array;
    foreach(ref i; parallel(arr)) {
        i = i*i;
    }

La función `parallel` utiliza el operador `opApply` de forma interna. La
función global `parallel` es una forma rápida de llamar a la función
`taskPool.parallel`, que es un `TaskPool` (un grupo de hilos) que usa tantos
hilos de ejecución como el número total de CPU menos uno. Crear una instancia
propia permite controlar el grado de paralelismo.

Hay que tener cuidado de que el cuerpo de cada iteración pasada a `parallel`
no modifique elementos a los que otra unidad de ejecución tenga acceso.

La función opcional `workingUnitSize` especifica el número de elementos
procesados por cada hilo de ejecución.

### La función `reduce`

La función [`std.algorithm.iteration.reduce`](http://dlang.org/phobos/std_algorithm_iteration.html#reduce),
conocida en otros contextos de programación funcional como *accumulate* o
*foldl*, llama a la función `fun(acc, x)` para cada elemento `x` donde `acc`
es el resultado previo:

    // 0 es la "semilla"
    auto sum = reduce!"a + b"(0, elements);

La función [`Taskpool.reduce`](http://dlang.org/phobos/std_parallelism.html#.TaskPool.reduce)
es la función análoga a `reduce` que usa paralelismo:

    // Encuentra la suma de un rango en paralelo, usando el
    // primer elemento de cada unidad de trabajo como semilla.
    auto sum = taskPool.reduce!"a + b"(nums);

La función `TaskPool.reduce` divide el rango en subrangos que se reducen en
paralelo. Una vez que los resultados han sido calculados, estos resultados
son reducidos en sí mismos.

### La función `task()`

La función [`task`](http://dlang.org/phobos/std_parallelism.html#.task) es un
envoltorio para una función que podría tardar mucho tiempo en ejecutarse o que
se debería ejecutar en su propio hilo. Incluso puede ser encolada en un grupo
de tareas (*taskpool*):

    auto t = task!read("foo.txt");
    taskPool.put(t);

O podría ser ejecutada directamente en un nuevo hilo de ejecución propio:

    t.executeInNewThread();

Para obtener el resultado de una tarea se llama a la función `yieldForce`
sobre ella. Esta función se bloquea hasta que el resultado está disponible.

    auto fileData = t.yieldForce;

### En profundidad

- [Parallelism in _Programming in D_](http://ddili.org/ders/d.en/parallelism.html)
- [std.parallelism](http://dlang.org/phobos/std_parallelism.html)

## {SourceCode}

```d
import std.parallelism : task,
    taskPool, TaskPool;
import std.array : array;
import std.stdio : writeln;
import std.range : iota;

string theTask() {
    import core.thread : dur, Thread;
    Thread.sleep( dur!("seconds")(1) );
    return "Hola, mundo.";
}

void main() {
    // Grupo de tareas con dos hilos.
    auto myTaskPool = new TaskPool(2);
    // Parar el grupo de tareas es importante.
    scope(exit) myTaskPool.stop();

    // Se inicia una tarea que tarda mucho
    // en ejecutarse. Mientras, se hacen otras
    // cosas...
    auto task = task!theTask;
    myTaskPool.put(task);

    auto arr = iota(1, 10).array;
    foreach(ref i; myTaskPool.parallel(arr)) {
        i = i*i;
    }

    writeln(arr);

    import std.algorithm.iteration : map;

    // Se usa la función `reduce` para
    // calcular la suma de todos los
    // cuadrados en paralelo.
    auto result = taskPool.reduce!"a+b"(
        0.0, iota(100).map!"a*a");
    writeln("Suma de los cuadrados: ", result);

    // Obtenemos el resultado que previamente
    // enviamos a segundo plano.
    writeln(task.yieldForce);
}
```
