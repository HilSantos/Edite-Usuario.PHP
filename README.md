# Edite-Usuario.PHP
Criação do editeusuario.php

<?php
// Inclui arquivos de segurança, cabeçalho da página e conexão com o banco de dados //
include('segurancadez.php');
include('cabecalho.php');
include('conn.php');

// Verifica se o formulário foi enviado via POST (edição confirmada) //
if($_SERVER['REQUEST_METHOD']=='POST'){

    // Captura os dados enviados pelo formulário //
    $id = $_POST['id'];
    $nome = $_POST['nome'];
    $apelido = $_POST['apelido'];
    $cpf = $_POST['cpf'];
    $email = $_POST['email'];
    $cep = $_POST['cep'];
    $rua = $_POST['rua'];
    $numero = $_POST['numero'];
    $bairro = $_POST['bairro'];
    $cidade = $_POST['cidade'];
    $uf = $_POST['uf'];
    $nascimento = $_POST['dt_nascimento'];
    $telefone = $_POST['telefone'];
    $nivel = $_POST['nivel'];

     $sql = "UPDATE `tb_usuarios` 
    SET `nome_usuario`='$nome',`apelido_usuario`='$apelido',`cpf_usuario`='$cpf',
    `email_usuario`='$email',`cep_usuario`='$cep',`rua_usuario`='$rua',`numero_rua_usuario`='$numero',
    `bairro_usuario`='$bairro',`cidade_usuario`='$cidade',`uf_usuario`='$uf',`nascimento_usuario`='$nascimento',
    `telefone_usuario`='$telefone',`nivel_usuario`='$nivel' WHERE id_usuario = $id";

    // Executa a atualização no banco //
    mysqli_query($link, $sql);
    mysqli_close($link);
    // Redireciona para a lista de usuários após a atualização //
    header('Location: listausuarios.php');
    exit();
}

// Se não houver ID via GET, redireciona para a lista //
if(!isset($_GET['id'])){
    header('Location: listausuarios.php');
    exit();
}

// Obtém os dados do usuário para preencher o formulário //
include('conn.php'); // Inclui a conexão com o banco de dados //
$id = $_GET['id'];
$sql = "SELECT * FROM tb_usuarios WHERE id_usuario = $id";
$result = mysqli_query($link, $sql);
$tbl = mysqli_fetch_array($result); // Armazena os dados do usuário //
if(!$tbl){
    // Se não encontrar o usuário, redireciona para a lista //
    mysqli_close($link);
    header('Location: listausuarios.php');
    exit();
}
// Fecha a conexão com o banco de dados //
mysqli_close($link);
?>


<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="cadastra.css">
    <title>Cadastra Usuario</title>
</head>
<body>
    <div class="container">
    <h1>Detalha Usuario</h1>
    <br>
    <form action="editeusuario.php" method="post">
        <input type="hidden" name="id" value="<?=$tbl[0]?>">
        <label for="nome">Nome:</label>
        <input type="text" value="<?=$tbl[1]?>" name="nome">
        <br>
        <label for="apelido">Alias:</label>
        <input type="text" value="<?=$tbl[2]?>" name="apelido">
        <br>
        <label for="cpf">CPF:</label>
        <input type="text" value="<?=$tbl[3]?>" name="cpf">
        <br>
        <label for="email">Email:</label>
        <input type="email" value="<?=$tbl[4]?>" name="email">
        <br>
        <label for="cep">CEP:</label>
        <input type="text" value="<?=$tbl[5]?>" id="cep" name="cep">
        <br>
        <label for="rua">Logradouro:</label>
        <input type="text" value="<?=$tbl[6]?>" id="rua" name="rua">
        <br>
        <label for="numero">Numero:</label>
        <input type="text" value="<?=$tbl[7]?>" name="numero">
        <br>
        <label for="bairro">Bairro:</label>
        <input type="text" value="<?=$tbl[8]?>" id="bairro" name="bairro">
        <br>
        <label for="cidade">Cidade:</label>
        <input type="text" value="<?=$tbl[9]?>" id="cidade" name="cidade">
        <br>
        <label for="uf">UF:</label>
        <input type="text" value="<?=$tbl[10]?>" id="uf" name="uf">
        <br>
        <label for="dt_nascimento">Data de Nascimento:</label>
        <input type="date" value="<?=$tbl[11]?>" name="dt_nascimento">
        <br>
        <label for="telefone">Telefone:</label>
        <input type="text" value="<?=$tbl[14]?>" name="telefone">
        <br>
        <label for="nivel">Nivel:</label>
        <select id="nivel" name="nivel">
            <option value="1"<?=$tbl[16]==1?"selected":"" ?>>Usuario</option>
            <option value="10"<?=$tbl[16]==10?"selected":"" ?>>Administrador</option>
        </select>
        <br>
        <br>
        <input type="submit" value="Gravar">
    </div>
    </form>
</body>
</html>
<script>
        document.addEventListener("DOMContentLoaded", function() {
            const cepInput = document.getElementById("cep");
 
cepInput.addEventListener("blur", function() {
                let cep = cepInput.value.replace(/\D/g, ''); // Remove tudo que não é número //
 
if (cep.length === 8) { // Valida se são 8 dígitos //
                    // Faz a requisição para a API ViaCEP //
                    fetch(`https://viacep.com.br/ws/${cep}/json/`)
                        .then(response => {
                            if (!response.ok) {
                                throw new Error('Erro ao buscar o CEP');
                            }
                            return response.json();
                        })
                        .then(data => {
                            if (data.erro) {
                                alert("CEP não encontrado.");
                                return;
                            }
                            // Preenche os campos do formulário //
                            document.getElementById("rua").value = data.logradouro;
                            document.getElementById("bairro").value = data.bairro;
                            document.getElementById("cidade").value = data.localidade;
                            document.getElementById("uf").value = data.uf;
                        })
                        .catch(error => {
                            console.error("Erro na busca do CEP: ", error);
                            alert("Não foi possível buscar o endereço.");
                        });
                } else {
                    alert("Formato de CEP inválido. Deve conter 8 dígitos numéricos.");
                }
            });
        });
</script>
