import tkinter as tk
import pyperclip
import re
from bs4 import BeautifulSoup


# Expresión regular para detectar comentarios en cualquier lenguaje de programación
comment_regex = r'((#|\/\/|\/\*|\-\-|<!--)[^\n\r]*[\n\r])|([\n\r]\s*\/\/[^\n\r]*)'


def filter_clipboard():
    # Obtener el contenido actual del portapapeles
    clipboard_content = pyperclip.paste()

    # Ventana de confirmación
    confirm_window = tk.Tk()
    confirm_window.title("Confirmación")
    confirm_label = tk.Label(confirm_window, text="¿Quieres aplicar el filtro de limpieza al código?")
    confirm_label.pack()
    yes_button = tk.Button(confirm_window, text="Sí", command=lambda: apply_filter(clipboard_content, confirm_window))
    yes_button.pack(side="left")
    no_button = tk.Button(confirm_window, text="No", command=confirm_window.destroy)
    no_button.pack(side="right")
    confirm_window.mainloop()


def apply_filter(clipboard_content, confirm_window):
    # Filtrar los elementos SVG del contenido del portapapeles
    soup = BeautifulSoup(clipboard_content, 'html.parser')
    for svg in soup.find_all('svg'):
        svg.extract()
    filtered_content = soup.prettify()

    # Eliminar los comentarios del código
    filtered_content = re.sub(comment_regex, '', filtered_content)

    # Mantener la identación del código
  

    # Eliminar las líneas de código vacías
    filtered_content = "\n".join([line for line in filtered_content.split("\n") if line.strip()])

    # Copiar el contenido filtrado de vuelta al portapapeles
    pyperclip.copy(filtered_content)

    # Cerrar la ventana de confirmación
    confirm_window.destroy()



# Ejecutar el programa en bucle
while True:
    # Esperar a que el usuario copie algo al portapapeles
    current_clipboard = pyperclip.paste()
    while current_clipboard == pyperclip.paste():
        pass

    # Preguntar si se desea aplicar el filtro al nuevo contenido copiado
    filter_clipboard()
