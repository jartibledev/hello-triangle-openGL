# Hello_triangle_OpenGL
## Instalación
### GLFW
Para *linkear* la librería de GLFW a nuestro proyecto en
Visual Studio Code, en`Configuration Properties`,accedemos a `Library Directories` y añadimos la ruta del archivo `glfw3.lib`.
Luego accedemos a `Input`, y añadimos en `Aditional Dependencies` el archivo `glfw3.lib` y el archivo `opengl32.lib`.
### GLAD
Creamos la carpeta Glad desde su [web](https://glad.dav1d.de/).Nos aseguramos de tener la opción de C++ activada,
y la última version de la API. Seleccionamos *Core* y marcamos la casilla *Generate a loader option*. Por ahora ignoaramos la sección de las extensiones.
Clickamos sobre *Generate* para producir los archivos de la librería. Debería devolvernos una carpeta con dos directorios: `KHR` y `glad`.

Creamos una carpeta `include` en nuestro directorio en la que incluiremos las carpetas `KHR` y `glad`. Aparte, sacamos el archivo `glad.c` y lo colocamos en la misma carpeta que nuestro archivo `main.cpp`.
La estructura de nuestro proyecto se debería ver así:
```text
TuProyecto/
│
├── include/            <-- Directorio de cabeceras de tu proyecto
│   ├── KHR/
│   │   └── khrplatform.h
│   └── glad/
│       └── glad.h
│
├── src/                <-- Directorio de código fuente
│   ├── main.cpp
│   └── glad.c          <-- Mueve glad.c aquí
│
└── CMakeLists.txt / Makefile
```
El archivo `glad.c` tiene que verse desde tu *Solution Explorer*, sino se ve, haz click derecho sobre *Source Files* y clicka sobre *Add>Existing Item* y busca `glad.c` y añádelo.
Luego haz click sobre *Properties* en el *Solution Explorer*. En el menú de la izquierda, ve a **Propiedades de configuración** > **C/C++** > **General**. 
En la casilla **Directorios de inclusión adicionales** (Additional Include Directories), añade la ruta a tu carpeta `include` (donde están las carpetas glad/ y KHR/).Haz clic en *Aplicar* y *Aceptar*.
Asegurate de incluir `C:\Proyectos\MiJuego\include\` y no `C:\Proyectos\MiJuego\include\glad\`, porque Visual Studio no leería el archivo glad que hay dentro.

## Hello Window
Añade los *imports* de **GLFW** y **GLAD**.
```c++
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```
Creamos la función `main`:
```c++
int main()
{
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  
    return 0;
}
```
- `glfwInit()`: inicializa GLFW.
- `glfwWindowHint`: configura GLFW. 
- `glfwWindowHint(opción_que_queremos_configurar, número_que_indica_el_valor_de_nuestra_opción);`

Podéis consultar las posibles opciones en la documentación de [GLFW's window handling](https://www.glfw.org/docs/latest/window.html#window_hints)

- ` glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);` es para iniciar GLFW en Mac OS.

Ahora inicializaremos la ventana:
```c++
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL)
{
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);
```
`glfwCreateWindow` tiene como primeros argumentos el ancho y el alto de la ventana rspectivamente. El tercer argumento
nos permite crear un nombre para la ventana. La función devuelve un objeto `GLFWwindow` que necesitaremos para las operaciones GLFW.

### GLAD
GLAD gestiona funciones punteros para OpenGL, así que inicializaremos GLAD.
```c++
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
} 
```
Pasamos GLAD para cargar el acceso de las funciones puntero de OpenGL con su OS específico. GLFW nos da `glfwGetProcAddress`
que define una función adecuada al OS que estamos compilando.

### Viewport
Antes de que podamos empezar a renderizar, tenemos que decirle a OpenGL el tamaño de la ventana que queremos mostrar y sus coordeandas.
```c++
glViewport(0, 0, 800, 600);
```
Los primeros dos argumentos son las coordenadas. El tercero y el cuarto las dimensiones de la ventana.
Necesitaremos que actualizar la ventana cuando el usuario redimensione la ventana con el *mouse*.Para ello definiremos una función *callback*:
```c++
void framebuffer_size_callback(GLFWwindow* window, int width, int height);
```

