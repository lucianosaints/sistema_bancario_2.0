# sistema_bancario_2.0
desafio_botcamp_santander_2025_DIO
## sistema_bancario_2.0

Roadmap

✅a função saque deve receber os argumentos apenas por nome
(keyword only).sugestão de agumentos: saldo,valor,extrato,limite,numero_saques
limite_saques. 
sugestao de retorno: saldo e extrato

✅a função de deposito deve receber os argumentos apenas por
posição (positional only).sugestão de argumentos:saldo , valor
extrato.sugestão de retorno :saldo e extrato.

✅a função extrato deve receber os argumentos por posição e
nome (positional pnly e keyword only).argumentos posicionais:
saldo, argumentos e nomeados: extrato. 

✅cria duas novas funcoes: criar usuario e criar conta corrente.
✅cria tambem uma função que busque ser o usuario possui conta ou não.

ao criar usuario deve armazenar os usuarios em uma lista,um usuario é
composto por:nome, data de nascimento, cpf e endereço.
o endereço é uma string com formato: rua, bairro. cidade/sigla estado.
deve ser armazenado somente os numeros do cpf . não podendo cadatrar 2 usuarios com mesmo CPF.

ao criar uma conta corrente deve armazena contas em um lista , uma conta é composta por:
agencia ,numero da conta e usuario. o numero da conta é sequencial em 1 . o numero da agencia 
é fixo"0001".o usuario pode ter mais de uma conta, mas uma conta
pertence apenas a um usuario.

para vincular um usuario a uma conta, coloque um filtro de usuarios
buscando pelo numero do CPF informado para cada usuario da lista.

novas funçoes adicionadas 
✅ buscar_usuario_por_nome

✅ remover_usuario

✅ editar_dados_usuario

✅ transferir_valor

✅ salvar_dados_em_arquivo e carregar_dados_de_arquivo

import textwrap
import json

def menu():
    menu = """\n
    ================ MENU ================
    [d]\tDepositar
    [s]\tSacar
    [e]\tExtrato
    [nc]\tNova conta
    [lc]\tListar contas
    [nu]\tNovo usuário
    [uc]\tUsuário possui conta?
    [bu]\tBuscar usuário por nome
    [ru]\tRemover usuário
    [eu]\tEditar usuário
    [t]\tTransferência entre contas
    [salvar]\tSalvar dados
    [q]\tSair
    => """
    return input(textwrap.dedent(menu))

def depositar(saldo, valor, extrato, /):
    if valor > 0:
        saldo += valor
        extrato += f"Depósito:\tR$ {valor:.2f}\n"
        print("\n=== Depósito realizado com sucesso! ===")
    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")
    return saldo, extrato

def sacar(*, saldo, valor, extrato, limite, numero_saques, limite_saques):
    excedeu_saldo = valor > saldo
    excedeu_limite = valor > limite
    excedeu_saques = numero_saques >= limite_saques

    if excedeu_saldo:
        print("\n@@@ Operação falhou! Você não tem saldo suficiente. @@@")
    elif excedeu_limite:
        print("\n@@@ Operação falhou! O valor do saque excede o limite. @@@")
    elif excedeu_saques:
        print("\n@@@ Operação falhou! Número máximo de saques excedido. @@@")
    elif valor > 0:
        saldo -= valor
        extrato += f"Saque:\t\tR$ {valor:.2f}\n"
        numero_saques += 1
        print("\n=== Saque realizado com sucesso! ===")
    else:
        print("\n@@@ Operação falhou! O valor informado é inválido. @@@")

    return saldo, extrato

def exibir_extrato(saldo, /, *, extrato):
    print("\n================ EXTRATO ================")
    print("Não foram realizadas movimentações." if not extrato else extrato)
    print(f"\nSaldo:\t\tR$ {saldo:.2f}")
    print("==========================================")

def criar_usuario(usuarios):
    cpf = input("Informe o CPF (somente números): ").strip()
    cpf = "".join(filter(str.isdigit, cpf))
    if filtrar_usuario(cpf, usuarios):
        print("\n@@@ Já existe usuário com esse CPF! @@@")
        return

    nome = input("Informe o nome completo: ")
    data_nascimento = input("Informe a data de nascimento (dd-mm-aaaa): ")
    endereco = input("Informe o endereço (rua, bairro - cidade/sigla estado): ")

    usuarios.append({
        "nome": nome,
        "data_nascimento": data_nascimento,
        "cpf": cpf,
        "endereco": endereco
    })

    print("\n=== Usuário criado com sucesso! ===")

def filtrar_usuario(cpf, usuarios):
    return next((usuario for usuario in usuarios if usuario["cpf"] == cpf), None)

def criar_conta(agencia, numero_conta, usuarios):
    cpf = input("Informe o CPF do usuário: ").strip()
    cpf = "".join(filter(str.isdigit, cpf))

    usuario = filtrar_usuario(cpf, usuarios)

    if usuario:
        print("\n=== Conta criada com sucesso! ===")
        return {
            "agencia": agencia,
            "numero_conta": numero_conta,
            "usuario": usuario
        }

    print("\n@@@ Usuário não encontrado, fluxo de criação de conta encerrado! @@@")

def listar_contas(contas):
    for conta in contas:
        linha = f"""\n
        Agência:\t{conta['agencia']}
        C/C:\t\t{conta['numero_conta']}
        Titular:\t{conta['usuario']['nome']}
        """
        print("=" * 100)
        print(textwrap.dedent(linha))

def usuario_possui_conta(cpf, contas):
    cpf = "".join(filter(str.isdigit, cpf))
    return any(conta['usuario']['cpf'] == cpf for conta in contas)

def buscar_usuario_por_nome(nome, usuarios):
    encontrados = [u for u in usuarios if nome.lower() in u["nome"].lower()]
    if encontrados:
        print("\n=== Usuários encontrados ===")
        for u in encontrados:
            print(f"{u['nome']} - CPF: {u['cpf']}")
    else:
        print("\n@@@ Nenhum usuário encontrado com esse nome. @@@")

def remover_usuario(cpf, usuarios, contas):
    cpf = "".join(filter(str.isdigit, cpf))
    if usuario_possui_conta(cpf, contas):
        print("\n@@@ Não é possível remover: o usuário possui conta vinculada. @@@")
        return

    for i, usuario in enumerate(usuarios):
        if usuario["cpf"] == cpf:
            usuarios.pop(i)
            print("\n=== Usuário removido com sucesso. ===")
            return

    print("\n@@@ Usuário não encontrado. @@@")

def editar_dados_usuario(cpf, usuarios):
    usuario = filtrar_usuario(cpf, usuarios)
    if not usuario:
        print("\n@@@ Usuário não encontrado. @@@")
        return

    print("\n--- Deixe em branco se não quiser alterar ---")
    usuario['nome'] = input(f"Novo nome ({usuario['nome']}): ") or usuario['nome']
    usuario['data_nascimento'] = input(f"Nova data de nascimento ({usuario['data_nascimento']}): ") or usuario['data_nascimento']
    usuario['endereco'] = input(f"Novo endereço ({usuario['endereco']}): ") or usuario['endereco']

    print("\n=== Dados atualizados com sucesso. ===")

def transferir_valor(contas, origem_cpf, destino_cpf, valor):
    origem = next((c for c in contas if c["usuario"]["cpf"] == origem_cpf), None)
    destino = next((c for c in contas if c["usuario"]["cpf"] == destino_cpf), None)

    if not origem or not destino:
        print("\n@@@ Uma das contas não foi encontrada. @@@")
        return

    origem["usuario"].setdefault("saldo", 0)
    destino["usuario"].setdefault("saldo", 0)

    if valor <= 0:
        print("\n@@@ Valor inválido para transferência. @@@")
        return

    if valor > origem["usuario"]["saldo"]:
        print("\n@@@ Saldo insuficiente para transferência. @@@")
        return

    origem["usuario"]["saldo"] -= valor
    destino["usuario"]["saldo"] += valor
    print("\n=== Transferência realizada com sucesso. ===")

def salvar_dados_em_arquivo(usuarios, contas, filename="dados_banco.json"):
    with open(filename, "w") as f:
        json.dump({"usuarios": usuarios, "contas": contas}, f, indent=4)
    print("\n=== Dados salvos com sucesso. ===")

def carregar_dados_de_arquivo(filename="dados_banco.json"):
    try:
        with open(filename, "r") as f:
            dados = json.load(f)
            return dados.get("usuarios", []), dados.get("contas", [])
    except FileNotFoundError:
        return [], []

def main():
    LIMITE_SAQUES = 3
    AGENCIA = "0001"

    saldo = 0
    limite = 500
    extrato = ""
    numero_saques = 0

    usuarios, contas = carregar_dados_de_arquivo()

    while True:
        opcao = menu()

        if opcao == "d":
            valor = float(input("Informe o valor do depósito: "))
            saldo, extrato = depositar(saldo, valor, extrato)

        elif opcao == "s":
            valor = float(input("Informe o valor do saque: "))
            saldo, extrato = sacar(
                saldo=saldo,
                valor=valor,
                extrato=extrato,
                limite=limite,
                numero_saques=numero_saques,
                limite_saques=LIMITE_SAQUES
            )

        elif opcao == "e":
            exibir_extrato(saldo, extrato=extrato)

        elif opcao == "nu":
            criar_usuario(usuarios)

        elif opcao == "nc":
            numero_conta = len(contas) + 1
            conta = criar_conta(AGENCIA, numero_conta, usuarios)
            if conta:
                contas.append(conta)

        elif opcao == "lc":
            listar_contas(contas)

        elif opcao == "uc":
            cpf = input("Informe o CPF do usuário: ")
            if usuario_possui_conta(cpf, contas):
                print("\n=== Este usuário POSSUI conta cadastrada. ===")
            else:
                print("\n@@@ Este usuário NÃO possui conta cadastrada. @@@")

        elif opcao == "bu":
            nome = input("Digite o nome a ser buscado: ")
            buscar_usuario_por_nome(nome, usuarios)

        elif opcao == "ru":
            cpf = input("Informe o CPF do usuário a remover: ")
            remover_usuario(cpf, usuarios, contas)

        elif opcao == "eu":
            cpf = input("Informe o CPF do usuário a editar: ")
            editar_dados_usuario(cpf, usuarios)

        elif opcao == "t":
            origem = input("CPF de quem envia: ").strip()
            destino = input("CPF de quem recebe: ").strip()
            valor = float(input("Valor da transferência: "))
            transferir_valor(contas, origem, destino, valor)

        elif opcao == "salvar":
            salvar_dados_em_arquivo(usuarios, contas)

        elif opcao == "q":
            salvar_dados_em_arquivo(usuarios, contas)
            print("\nAté logo!")
            break

        else:
            print("Operação inválida, tente novamente.")

main()
