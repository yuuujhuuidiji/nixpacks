import tkinter as tk
import sqlite3

# Função para criar a tabela se não existir
def criar_tabela():
    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute('''CREATE TABLE IF NOT EXISTS funcionarios (
                id INTEGER PRIMARY KEY,
                nome TEXT,
                email TEXT,
                setor TEXT,
                telefone TEXT,
                numero_desktop INTEGER,
                anydesk TEXT,
                ip_maquina TEXT,
                senha_desktop TEXT
                )''')
    conn.commit()
    conn.close()

# Função para inserir um novo funcionário
def inserir_funcionario():
    # Solicitar informações do usuário
    nome = nome_entry.get()
    email = email_entry.get()
    setor = setor_entry.get()
    telefone = telefone_entry.get()
    numero_desktop = numero_desktop_entry.get()
    anydesk = anydesk_entry.get()
    ip_maquina = ip_maquina_entry.get()
    senha_desktop = senha_desktop_entry.get()

    # Inserir o funcionário no banco de dados
    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute('''INSERT INTO funcionarios (nome, email, setor, telefone, numero_desktop, anydesk, ip_maquina, senha_desktop) 
                VALUES (?, ?, ?, ?, ?, ?, ?, ?)''', (nome, email, setor, telefone, numero_desktop, anydesk, ip_maquina, senha_desktop))
    conn.commit()
    conn.close()
    mensagem_funcionario_adicionado_sucesso()  # Exibir mensagem de sucesso

# Função para exibir todos os funcionários
def exibir_funcionarios():
    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute('''SELECT * FROM funcionarios''')
    funcionarios = c.fetchall()
    conn.close()

    resultado_text.delete(1.0, tk.END)  # Limpar o resultado anterior

    resultado_text.insert(tk.END, "\nLista de Funcionários:\n")
    for funcionario in funcionarios:
        resultado_text.insert(tk.END, f"\nNome: {funcionario[1]}\n")
        resultado_text.insert(tk.END, f"Email: {funcionario[2]}\n")
        resultado_text.insert(tk.END, f"Setor: {funcionario[3]}\n")
        resultado_text.insert(tk.END, f"Telefone: {funcionario[4]}\n")
        resultado_text.insert(tk.END, f"Número do Desktop: {funcionario[5]}\n")
        resultado_text.insert(tk.END, f"AnyDesk: {funcionario[6]}\n")
        resultado_text.insert(tk.END, f"IP da Máquina: {funcionario[7]}\n")
        resultado_text.insert(tk.END, f"Senha do Desktop: {funcionario[8]}\n")
        resultado_text.insert(tk.END, "---------------------------------\n")

# Função para consultar um funcionário
def consultar_funcionario():
    opcao_consulta = opcao_consulta_var.get()

    if opcao_consulta == 1:
        nome_funcionario = nome_consulta_entry.get()
        consulta = '''SELECT * FROM funcionarios WHERE nome = ?'''
        parametro = (nome_funcionario,)
    elif opcao_consulta == 2:
        telefone_funcionario = telefone_consulta_entry.get()
        consulta = '''SELECT * FROM funcionarios WHERE telefone = ?'''
        parametro = (telefone_funcionario,)
    elif opcao_consulta == 3:
        ip_maquina_funcionario = ip_maquina_consulta_entry.get()
        consulta = '''SELECT * FROM funcionarios WHERE ip_maquina = ?'''
        parametro = (ip_maquina_funcionario,)
    else:
        resultado_text.insert(tk.END, "Opção inválida.\n")
        return

    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute(consulta, parametro)
    funcionario = c.fetchone()
    conn.close()

    resultado_text.delete(1.0, tk.END)  # Limpar o resultado anterior

    if funcionario:
        resultado_text.insert(tk.END, "Funcionário encontrado:\n")
        resultado_text.insert(tk.END, f"\nNome: {funcionario[1]}\n")
        resultado_text.insert(tk.END, f"Email: {funcionario[2]}\n")
        resultado_text.insert(tk.END, f"Setor: {funcionario[3]}\n")
        resultado_text.insert(tk.END, f"Telefone: {funcionario[4]}\n")
        resultado_text.insert(tk.END, f"Número do Desktop: {funcionario[5]}\n")
        resultado_text.insert(tk.END, f"AnyDesk: {funcionario[6]}\n")
        resultado_text.insert(tk.END, f"IP da Máquina: {funcionario[7]}\n")
        resultado_text.insert(tk.END, f"Senha do Desktop: {funcionario[8]}\n")
    else:
        resultado_text.insert(tk.END, "Funcionário não encontrado.\n")

# Função para excluir um funcionário
def excluir_funcionario():
    nome_funcionario = nome_exclusao_entry.get()
    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute('''DELETE FROM funcionarios WHERE nome = ?''', (nome_funcionario,))
    conn.commit()
    conn.close()
    mensagem_funcionario_excluido_sucesso()  # Exibir mensagem de sucesso

# Função para excluir todos os funcionários
def excluir_todos_funcionarios():
    conn = sqlite3.connect('dados_funcionarios.db')
    c = conn.cursor()
    c.execute('''DELETE FROM funcionarios''')
    conn.commit()
    conn.close()
    exibir_funcionarios()  # Atualizar a exibição após a exclusão
    mensagem_funcionarios_excluidos_sucesso()  # Exibir mensagem de sucesso

# Função para limpar o resultado
def limpar_resultado():
    resultado_text.delete(1.0, tk.END)

# Configuração da interface gráfica
root = tk.Tk()
root.title("Sistema de Funcionários")
root.configure(bg="#1e272e")  # Cor de fundo azul escuro

# Seção para adicionar funcionário
frame_adicionar = tk.Frame(root, bg="#1e272e")
frame_adicionar.pack(padx=10, pady=10, side="left")
tk.Label(frame_adicionar, text="Adicionar Funcionário:", font=("Arial", 12, "bold"), bg="#1e272e", fg="white").grid(row=0, column=0, columnspan=2, pady=(20,0), sticky="w")
tk.Label(frame_adicionar, text="Nome:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=1, column=0, sticky="w")
nome_entry = tk.Entry(frame_adicionar)
nome_entry.grid(row=1, column=1)
tk.Label(frame_adicionar, text="Email:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=2, column=0, sticky="w")
email_entry = tk.Entry(frame_adicionar)
email_entry.grid(row=2, column=1)
tk.Label(frame_adicionar, text="Setor:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=3, column=0, sticky="w")
setor_entry = tk.Entry(frame_adicionar)
setor_entry.grid(row=3, column=1)
tk.Label(frame_adicionar, text="Telefone:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=4, column=0, sticky="w")
telefone_entry = tk.Entry(frame_adicionar)
telefone_entry.grid(row=4, column=1)
tk.Label(frame_adicionar, text="Número do Desktop:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=5, column=0, sticky="w")
numero_desktop_entry = tk.Entry(frame_adicionar)
numero_desktop_entry.grid(row=5, column=1)
tk.Label(frame_adicionar, text="AnyDesk:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=6, column=0, sticky="w")
anydesk_entry = tk.Entry(frame_adicionar)
anydesk_entry.grid(row=6, column=1)
tk.Label(frame_adicionar, text="IP da Máquina:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=7, column=0, sticky="w")
ip_maquina_entry = tk.Entry(frame_adicionar)
ip_maquina_entry.grid(row=7, column=1)
tk.Label(frame_adicionar, text="Senha do Desktop:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=8, column=0, sticky="w")
senha_desktop_entry = tk.Entry(frame_adicionar)
senha_desktop_entry.grid(row=8, column=1)
# Botão para adicionar funcionário
tk.Button(frame_adicionar, text="Adicionar Funcionário", font=("Arial", 10), command=inserir_funcionario).grid(row=9, column=0, columnspan=2, pady=(10,0))

# Seção para consultar o funcionário
frame_consultar = tk.Frame(root, bg="#1e272e")
frame_consultar.pack(padx=10, pady=10, side="left")
tk.Label(frame_consultar, text="Consultar Funcionário:", font=("Arial", 12, "bold"), bg="#1e272e", fg="white").grid(row=0, column=0, columnspan=2, pady=(20,0), sticky="w")
opcao_consulta_var = tk.IntVar()
opcao_consulta_var.set(1)
tk.Radiobutton(frame_consultar, text="Nome", font=("Arial", 10), variable=opcao_consulta_var, value=1, bg="#1e272e", fg="white").grid(row=1, column=0, sticky="w")
nome_consulta_entry = tk.Entry(frame_consultar)
nome_consulta_entry.grid(row=1, column=1)
tk.Radiobutton(frame_consultar, text="Telefone", font=("Arial", 10), variable=opcao_consulta_var, value=2, bg="#1e272e", fg="white").grid(row=2, column=0, sticky="w")
telefone_consulta_entry = tk.Entry(frame_consultar)
telefone_consulta_entry.grid(row=2, column=1)
tk.Radiobutton(frame_consultar, text="IP da Máquina", font=("Arial", 10), variable=opcao_consulta_var, value=3, bg="#1e272e", fg="white").grid(row=3, column=0, sticky="w")
ip_maquina_consulta_entry = tk.Entry(frame_consultar)
ip_maquina_consulta_entry.grid(row=3, column=1)
# Botão para consultar o funcionário
tk.Button(frame_consultar, text="Consultar", font=("Arial", 10), command=consultar_funcionario).grid(row=4, column=0, columnspan=2, pady=(10,0))

# Seção para excluir funcionário
frame_excluir = tk.Frame(root, bg="#1e272e")
frame_excluir.pack(padx=10, pady=10, side="left")
tk.Label(frame_excluir, text="Excluir Funcionário:", font=("Arial", 12, "bold"), bg="#1e272e", fg="white").grid(row=0, column=0, columnspan=2, pady=(20,0), sticky="w")
tk.Label(frame_excluir, text="Nome:", font=("Arial", 10), bg="#1e272e", fg="white").grid(row=1, column=0, sticky="w")
nome_exclusao_entry = tk.Entry(frame_excluir)
nome_exclusao_entry.grid(row=1, column=1)
# Botão para excluir funcionário
tk.Button(frame_excluir, text="Excluir Funcionário", font=("Arial", 10), command=excluir_funcionario).grid(row=2, column=0, columnspan=2, pady=(10,0))
# Botão para excluir todos os funcionários
tk.Button(frame_excluir, text="Excluir Todos", font=("Arial", 10), command=excluir_todos_funcionarios).grid(row=3, column=0, columnspan=2, pady=(10,0))

# Seção para exibir todos os funcionários
frame_exibir_todos = tk.Frame(root, bg="#1e272e")
frame_exibir_todos.pack(padx=10, pady=10, side="left")
# Botão para exibir todos os funcionários
tk.Button(frame_exibir_todos, text="Exibir Todos", font=("Arial", 10), command=exibir_funcionarios).grid(row=0, column=0, columnspan=2, pady=(10,0))

# Seção para exibir resultado
frame_resultado = tk.Frame(root, bg="#1e272e")
frame_resultado.pack(padx=10, pady=10, side="left")
resultado_text = tk.Text(frame_resultado, width=50, height=20)
resultado_text.grid(row=0, column=0)
# Botão para limpar o resultado
tk.Button(frame_resultado, text="Limpar Resultado", font=("Arial", 10), command=limpar_resultado).grid(row=1, column=0, pady=(10,0))

# Funções para exibir mensagens de sucesso
def mensagem_funcionario_adicionado_sucesso():
    resultado_text.delete(1.0, tk.END)
    resultado_text.insert(tk.END, "Funcionário adicionado com sucesso!\n")

def mensagem_funcionario_excluido_sucesso():
    resultado_text.delete(1.0, tk.END)
    resultado_text.insert(tk.END, "Funcionário excluído com sucesso!\n")

def mensagem_funcionarios_excluidos_sucesso():
    resultado_text.delete(1.0, tk.END)
    resultado_text.insert(tk.END, "Todos os funcionários foram excluídos com sucesso!\n")

root.mainloop()
