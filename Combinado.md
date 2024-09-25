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
    private static EstadisticasFutbol instancia;
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

    // Métodos para suscribirse y desuscribirse (Observer)
    public void AgregarObserver(IObserver observer)
    {
        observadores.Add(observer);
    }

    public void EliminarObserver(IObserver observer)
    {
        observadores.Remove(observer);
    }

    public void NotificarObservers(string mensaje)
    {
        foreach (var observer in observadores)
        {
            observer.Actualizar(mensaje);
        }
    }

    // Método para actualizar las estadísticas
    public void ActualizarEstadisticas(int goles, int tarjetas)
    {
        Goles = goles;
        Tarjetas = tarjetas;
        NotificarObservers($"Estadísticas actualizadas: Goles={Goles}, Tarjetas={Tarjetas}");
    }
}

// Clase Observador (por ejemplo, una pantalla de estadísticas)
public class PantallaEstadisticas : IObserver
{
    private string nombre;

    public PantallaEstadisticas(string nombre)
    {
        this.nombre = nombre;
    }

    public void Actualizar(string mensaje)
    {
        Console.WriteLine($"Pantalla {nombre} muestra: {mensaje}");
    }
}

// Uso del Singleton y Observer en el sistema de estadísticas de fútbol
class Program
{
    static void Main(string[] args)
    {
        // Obtener la única instancia del administrador de estadísticas
        EstadisticasFutbol adminEstadisticas = EstadisticasFutbol.ObtenerInstancia();

        // Crear algunas pantallas observadoras
        PantallaEstadisticas pantalla1 = new PantallaEstadisticas("Central");
        PantallaEstadisticas pantalla2 = new PantallaEstadisticas("Estadio");

        // Suscribir pantallas al administrador de estadísticas
        adminEstadisticas.AgregarObserver(pantalla1);
        adminEstadisticas.AgregarObserver(pantalla2);

        // Actualizar estadísticas del partido
        adminEstadisticas.ActualizarEstadisticas(2, 3);

        // Desuscribir una pantalla y actualizar estadísticas de nuevo
        adminEstadisticas.EliminarObserver(pantalla1);
        adminEstadisticas.ActualizarEstadisticas(3, 4);
    }
}
