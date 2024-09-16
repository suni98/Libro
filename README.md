# Libro
# Clase Libro
class Libro:
    def __init__(self, titulo, autor, categoria, isbn):
        self._titulo = titulo
        self._autor = autor  # Tupla (nombre_autor, apellido_autor)
        self.categoria = categoria
        self.isbn = isbn

    def __str__(self):
        return f"{self._titulo} by {self._autor[0]} {self._autor[1]} (ISBN: {self.isbn})"

# Clase Usuario
class Usuario:
    def __init__(self, nombre, id_usuario):
        self.nombre = nombre
        self.id_usuario = id_usuario
        self.libros_prestados = []

    def prestar_libro(self, libro):
        self.libros_prestados.append(libro)

    def devolver_libro(self, libro):
        if libro in self.libros_prestados:
            self.libros_prestados.remove(libro)

    def listar_libros_prestados(self):
        return [str(libro) for libro in self.libros_prestados]

    def __str__(self):
        return f"Usuario {self.nombre} (ID: {self.id_usuario})"

# Clase Biblioteca
class Biblioteca:
    def __init__(self):
        self.libros_disponibles = {}  # Diccionario con ISBN como clave y objeto Libro como valor
        self.usuarios_registrados = {}  # Diccionario con ID de usuario como clave y objeto Usuario como valor
        self.usuarios_id = set()  # Set para IDs únicos

    def añadir_libro(self, libro):
        if libro.isbn not in self.libros_disponibles:
            self.libros_disponibles[libro.isbn] = libro
        else:
            print(f"El libro con ISBN {libro.isbn} ya está en la biblioteca.")

    def quitar_libro(self, isbn):
        if isbn in self.libros_disponibles:
            del self.libros_disponibles[isbn]
        else:
            print(f"No se encontró el libro con ISBN {isbn}.")

    def registrar_usuario(self, usuario):
        if usuario.id_usuario not in self.usuarios_id:
            self.usuarios_registrados[usuario.id_usuario] = usuario
            self.usuarios_id.add(usuario.id_usuario)
        else:
            print(f"El usuario con ID {usuario.id_usuario} ya está registrado.")

    def dar_de_baja_usuario(self, id_usuario):
        if id_usuario in self.usuarios_registrados:
            del self.usuarios_registrados[id_usuario]
            self.usuarios_id.remove(id_usuario)
        else:
            print(f"No se encontró el usuario con ID {id_usuario}.")

    def prestar_libro(self, isbn, id_usuario):
        if isbn in self.libros_disponibles and id_usuario in self.usuarios_registrados:
            libro = self.libros_disponibles.pop(isbn)
            usuario = self.usuarios_registrados[id_usuario]
            usuario.prestar_libro(libro)
        else:
            print("No se puede prestar el libro. Verifique el ISBN y el ID del usuario.")

    def devolver_libro(self, isbn, id_usuario):
        usuario = self.usuarios_registrados.get(id_usuario)
        if usuario:
            for libro in usuario.libros_prestados:
                if libro.isbn == isbn:
                    usuario.devolver_libro(libro)
                    self.libros_disponibles[isbn] = libro
                    break
        else:
            print(f"Usuario con ID {id_usuario} no encontrado o libro no prestado.")

    def buscar_libro(self, titulo=None, autor=None, categoria=None):
        resultados = []
        for libro in self.libros_disponibles.values():
            if (titulo and titulo.lower() in libro._titulo.lower()) or \
               (autor and (autor.lower() in libro._autor[0].lower() or autor.lower() in libro._autor[1].lower())) or \
               (categoria and categoria.lower() in libro.categoria.lower()):
                resultados.append(libro)
        return resultados

    def __str__(self):
        return f"Biblioteca con {len(self.libros_disponibles)} libros disponibles y {len(self.usuarios_registrados)} usuarios registrados."

# Pruebas del sistema
if __name__ == "__main__":
    # Crear libros
    libro1 = Libro("Cien Años de Soledad", ("Gabriel", "García Márquez"), "Ficción", "978-0060883287")
    libro2 = Libro("Don Quijote", ("Miguel", "de Cervantes"), "Clásico", "978-0142437230")

    # Crear biblioteca
    biblioteca = Biblioteca()
    
    # Añadir libros a la biblioteca
    biblioteca.añadir_libro(libro1)
    biblioteca.añadir_libro(libro2)

    # Registrar usuarios
    usuario1 = Usuario("Juan", 1)
    usuario2 = Usuario("María", 2)
    
    biblioteca.registrar_usuario(usuario1)
    biblioteca.registrar_usuario(usuario2)

    # Prestar un libro
    biblioteca.prestar_libro("978-0060883287", 1)
    
    # Listar libros prestados por un usuario
    print("Libros prestados por Juan:", usuario1.listar_libros_prestados())

    # Buscar un libro por título
    print("Resultados de búsqueda por título 'Don Quijote':", [str(libro) for libro in biblioteca.buscar_libro(titulo="Don Quijote")])
    
    # Devolver un libro
    biblioteca.devolver_libro("978-0060883287", 1)
    
    # Comprobar libros disponibles
    print(biblioteca)
