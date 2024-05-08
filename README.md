# tempoemtempo
enviar mensagem de tempos em tempos
import time
import threading
import tkinter as tk
from tkinter import messagebox  # Importa o módulo messagebox
import pyautogui

# Variável global para controlar o estado do script
running = True
paused = False

def enviar_mensagem_linha_por_linha(texto):
    global running, paused
    
    linhas = texto.split('\n')  # Dividir o texto em linhas
    
    # Espera 3 segundos para mudar para a janela correta (se necessário)
    time.sleep(3)
    
    # Loop sobre cada linha do texto
    for linha in linhas:
        while paused:
            time.sleep(1)
            if not running:
                return
        
        pyautogui.typewrite(linha)  # Digita a linha
        pyautogui.keyDown('shift')   # Pressiona a tecla Shift
        pyautogui.press('enter')     # Pressiona Enter para enviar a linha
        pyautogui.keyUp('shift')     # Solta a tecla Shift
        time.sleep(8)                # Aguarda 8 segundos antes de enviar a próxima linha
        
        # Verifica se o script deve parar
        if not running:
            print("Script parado.")
            return
        
        # Verifica se há um caractere "?" na linha
        if "?" in linha:
            # Pausa o script e pergunta ao usuário se deseja continuar
            resposta = messagebox.askquestion("Pergunta", "Encontrado um '?' na frase. Deseja continuar para o próximo parágrafo?")
            if resposta == "no":
                print("Script interrompido pelo usuário.")
                return

def iniciar_script(texto_entry):
    global running, paused
    running = True
    paused = False
    texto = texto_entry.get("1.0", tk.END)
    enviar_mensagem_linha_por_linha(texto)

def parar_script():
    global running
    running = False

def pausar_continuar_script():
    global paused
    paused = not paused

def monitor_teclado():
    input("Pressione - para iniciar o script.\n")
    parar_script()

def iniciar_interface():
    root = tk.Tk()
    root.title("Enviar Mensagens")
    
    texto_label = tk.Label(root, text="Digite o texto que deseja enviar:")
    texto_label.pack()
    
    texto_entry = tk.Text(root, height=10, width=50)
    texto_entry.pack()
    
    iniciar_button = tk.Button(root, text="Iniciar Script", command=lambda: iniciar_script(texto_entry))
    iniciar_button.pack()

    pausar_continuar_button = tk.Button(root, text="Pausar/Continuar", command=pausar_continuar_script)
    pausar_continuar_button.pack()
    
    root.mainloop()

if __name__ == "__main__":
    # Inicia uma thread para monitorar o teclado
    keyboard_thread = threading.Thread(target=monitor_teclado)
    keyboard_thread.start()
    
    # Inicia a interface gráfica
    iniciar_interface()
    
    # Aguarda a thread do teclado terminar
    keyboard_thread.join()
