# Patrones de Diseño: Singleton y Observer

## 1. Patrón Singleton

### Definición:
El patrón **Singleton** asegura que una clase solo tenga una **única instancia** y proporciona un punto de acceso global a ella. Es útil cuando se necesita gestionar un recurso compartido, como una conexión a base de datos o, en este caso, el administrador de estadísticas de fútbol.

### Características clave:
- Garantiza una única instancia de la clase.
- Proporciona un acceso global a la instancia.
- Controla el acceso a recursos compartidos o mantiene el estado centralizado en todo el sistema.

---

## 2. Patrón Observer (Observador)

### Definición:
El patrón **Observer** define una relación de **uno a muchos** entre objetos, donde cuando uno de los objetos cambia su estado, todos sus dependientes son **notificados** y actualizados automáticamente. Es ideal para sistemas donde se necesita informar a múltiples componentes sobre cambios en los datos o estado.

### Características clave:
- Desacopla el objeto que genera eventos (sujeto) de los que reaccionan a esos eventos (observadores).
- Los observadores se pueden suscribir o darse de baja.
- Los observadores son automáticamente actualizados cuando cambia el estado del sujeto.

---

## Combinación: Singleton y Observer

En la administración de estadísticas de fútbol:
- **Singleton** asegura que haya solo un único **Administrador de Estadísticas** que maneje los datos del sistema.
- **Observer** permite que varios módulos (interfaces gráficas, sistemas de notificación, etc.) se **suscriban** a las actualizaciones de estadísticas y respondan automáticamente cuando cambian.

---

## Ejemplo en C# para la administración de estadísticas de fútbol

```csharp
using System;
using System.Collections.Generic;

// Interfaz para el patrón Observer
public interface IObserver
{
    void Actualizar(string mensaje);
}

// Interfaz para el Sujeto (Publisher)
public interface ISujeto
{
    void AgregarObserver(IObserver observer);
    void EliminarObserver(IObserver observer);
    void NotificarObservers(string mensaje);
}

// Singleton para el administrador de estadísticas (Sujeto del patrón Observer)
public class EstadisticasFutbol : ISujeto
{
    // Atributo privado estático para garantizar que solo hay una instancia
    private static EstadisticasFutbol instancia;

    // Lista de observadores suscritos
    private List<IObserver> observadores = new List<IObserver>();

    // Datos de las estadísticas
    public int Goles { get; private set; }
    public int Tarjetas { get; private set; }

    // Constructor privado para el Singleton
    private EstadisticasFutbol() { }

    // Método para obtener la única instancia del Singleton
    public static EstadisticasFutbol ObtenerInstancia()
    {
        if (instancia == null)
        {
            instancia = new EstadisticasFutbol();
        }
        return instancia;
    }

    // Métodos para agregar y eliminar observadores (Observer)
    public void AgregarObserver(IObserver observer)
    {
        observadores.Add(observer);
    }

    public void EliminarObserver(IObserver observer)
    {
        observadores.Remove(observer);
    }

    // Método para notificar a todos los observadores sobre un cambio en las estadísticas
    public void NotificarObservers(string mensaje)
    {
        foreach (var observer in observadores)
        {
            observer.Actualizar(mensaje);
        }
    }

    // Método para actualizar las estadísticas de fútbol y notificar a los observadores
    public void ActualizarEstadisticas(int goles, int tarjetas)
    {
        Goles = goles;
        Tarjetas = tarjetas;
        // Usamos string.Format para formatear el mensaje correctamente
        string mensaje = string.Format("Estadísticas actualizadas: Goles={0}, Tarjetas={1}", Goles, Tarjetas);
        NotificarObservers(mensaje);
    }
}

// Clase Observador (por ejemplo, una pantalla de estadísticas)
public class PantallaEstadisticas : IObserver
{
    private string nombre;

    // Constructor para darle un nombre a la pantalla
    public PantallaEstadisticas(string nombre)
    {
        this.nombre = nombre;
    }

    // Implementación del método Actualizar, que será llamado cuando cambien las estadísticas
    public void Actualizar(string mensaje)
    {
        Console.WriteLine(string.Format("Pantalla {0} muestra: {1}", nombre, mensaje));
    }
}

// Programa principal para ejecutar la prueba
public class Program // Hacer la clase pública
{
    public static void Main(string[] args) // Método Main debe ser público
    {
        // Obtener la única instancia del administrador de estadísticas
        EstadisticasFutbol adminEstadisticas = EstadisticasFutbol.ObtenerInstancia();

        // Crear algunas pantallas observadoras
        PantallaEstadisticas pantalla1 = new PantallaEstadisticas("Central");
        PantallaEstadisticas pantalla2 = new PantallaEstadisticas("Estadio");

        // Suscribir las pantallas al administrador de estadísticas
        adminEstadisticas.AgregarObserver(pantalla1);
        adminEstadisticas.AgregarObserver(pantalla2);

        // Actualizar las estadísticas del partido
        Console.WriteLine("Actualización 1:");
        adminEstadisticas.ActualizarEstadisticas(2, 3);

        // Eliminar una pantalla y actualizar nuevamente las estadísticas
        adminEstadisticas.EliminarObserver(pantalla1);
        Console.WriteLine("\nActualización 2:");
        adminEstadisticas.ActualizarEstadisticas(3, 4);

        Console.ReadLine(); // Pausa para que se pueda ver la salida en la consola
    }
}


