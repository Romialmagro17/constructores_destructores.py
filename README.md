# constructores_destructores.py
import os

class ManejadorArchivo:
    """
    Esta clase demuestra el uso de constructores (__init__) y destructores (__del__)
    para gestionar la apertura y cierre de un archivo.
    """

    def __init__(self, nombre_archivo, modo):
        """
        Constructor de la clase ManejadorArchivo.

        Se llama automáticamente cuando se crea una nueva instancia de la clase.
        Su propósito principal es inicializar los atributos del objeto.
        En este caso, abre un archivo en el modo especificado.

        Args:
            nombre_archivo (str): El nombre del archivo a manejar.
            modo (str): El modo de apertura del archivo (ej. 'w' para escritura, 'r' para lectura).
        """
        self.nombre_archivo = nombre_archivo
        self.modo = modo
        self.archivo = None
        try:
            self.archivo = open(nombre_archivo, modo)
            print(f"Constructor: Archivo '{self.nombre_archivo}' abierto en modo '{self.modo}'.")
        except IOError as e:
            print(f"Error al abrir el archivo en el constructor: {e}")

    def escribir_linea(self, linea):
        """
        Escribe una línea de texto en el archivo.
        """
        if self.archivo:
            try:
                self.archivo.write(linea + "\n")
                print(f"Escrito en '{self.nombre_archivo}': '{linea}'")
            except IOError as e:
                print(f"Error al escribir en el archivo: {e}")
        else:
            print("El archivo no está abierto.")

    def __del__(self):
        """
        Destructor de la clase ManejadorArchivo.

        Se llama automáticamente cuando un objeto está a punto de ser destruido
        (es decir, cuando ya no hay referencias a él y el recolector de basura de Python
        determina que puede ser liberado).
        Su propósito principal es realizar tareas de limpieza, como cerrar recursos
        o liberar memoria.
        En este caso, cierra el archivo si está abierto.
        """
        if self.archivo:
            self.archivo.close()
            print(f"Destructor: Archivo '{self.nombre_archivo}' cerrado.")
        else:
            print(f"Destructor: No hay archivo abierto para '{self.nombre_archivo}'.")

# --- Programa Principal para Demostrar el Uso ---

if __name__ == "__main__":
    nombre_archivo_ejemplo = "ejemplo.txt"

    print("--- Demostración 1: Apertura y Escritura ---")
    # Creamos una instancia de ManejadorArchivo, lo que llama al constructor
    manejador1 = ManejadorArchivo(nombre_archivo_ejemplo, 'w')
    manejador1.escribir_linea("Hola, este es un ejemplo de constructor y destructor.")
    manejador1.escribir_linea("El archivo se cerrará automáticamente cuando el objeto sea destruido.")

    # Al salir de este bloque o cuando 'manejador1' ya no tenga referencias,
    # el destructor __del__ se activará automáticamente.
    del manejador1 # Forzamos la eliminación de la referencia para ver la activación del destructor

    print("\n--- Demostración 2: Manejo de un objeto que se va del alcance ---")
    def usar_manejador_temporal():
        # Creamos una instancia dentro de una función
        manejador_temp = ManejadorArchivo("temporal.log", 'w')
        manejador_temp.escribir_linea("Este es un archivo temporal.")
        # Cuando la función termine, manejador_temp quedará sin referencias y su destructor se llamará.
    
    usar_manejador_temporal()
    print("La función 'usar_manejador_temporal' ha terminado. El destructor para 'temporal.log' debería haberse llamado.")

    print("\n--- Demostración 3: Archivo que no puede ser abierto (manejo de errores en constructor) ---")
    # Intentamos abrir un archivo en un modo inválido (solo lectura pero intentamos escribir)
    # Esto activará el manejo de errores en el constructor
    manejador_error = ManejadorArchivo("/ruta/no/existente/archivo_error.txt", 'w')
    # Este objeto existirá pero su archivo interno será None si hubo un error.
    # El destructor aún se llamará cuando se elimine la referencia.

    # Limpiamos el archivo de ejemplo creado
    if os.path.exists(nombre_archivo_ejemplo):
        os.remove(nombre_archivo_ejemplo)
        print(f"\nArchivo '{nombre_archivo_ejemplo}' eliminado.")
    if os.path.exists("temporal.log"):
        os.remove("temporal.log")
        print(f"Archivo 'temporal.log' eliminado.")
